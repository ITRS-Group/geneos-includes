# Load Monitoring Include Files

This page contains Gateway include files for monitoring the load of your Geneos Gateways. The primary challenge when monitoring a busy Gateway, either for diagnostic purposes or to be alerted if load exceeds desired limits, is that the Gateway itself may stop responding and have a backlog of monitored data (the 'Max Data Age'). This can be addressed by letting your Gateways write snapshots of internal data to an XML file which can then be loaded and processed by a less loaded Gateway. The stats file is written from it's own thread and so it not generally impacted by peaks in processing load in the Gateway process.

There are three include files:

* [`record.xml`](record.xml)

  To enable your Gateway(s) to write out stats files you can either make a small change to your configuration by hand or use this include file.
* [`monitor.xml`](monitor.xml)

  This include contains the basic configuration to load stats files for any number of Gateways on the same host and to transform some of the counters into more useful CPU percentages over known periods. To use it you must create Managed Entities, one per Gateway being monitored, and set some variables to suit.
* [`monitor_rules.xml`](monitor_rules.xml)

  This include contains a number of Rules to apply to the views created in the `monitor.xml` above but have been separated out as the chosen thresholds and other logic may not suit all users. Start with this file but change it as you learn more about the behaviour of Geneos Gateways in your environment.
    Note: At this time, there are no severity setting Rules defined.

## How to use

### On Gateways To Be Monitored

To enable a load monitoring stats file to be written at regular intervals you should either make a small change in your Gateway configuration or use the small include file `record.xml` to make the same change.

* To make the change directly:

  1. Open your Gateway Setup Editor
  2. In the section Operating Environment -> Advanced click anywhere in the box at the bottom of the page labelled `Write stats to file` and set the filename to either `stats.xml` (the default we use here) or to anther file, potentially in a different directory, as appropriate for your Geneos environment. The file generated is around 0.5MB in size and is a snapshot and not a growing historical log. Each Gateway **MUST** write to it's own file.
  3. Note down ths file path and name you have used as this will be required, for each Gateway, below.

* To use the defaults in the `record.xml` include file:

  1. Copy the XML below:

     ```xml
     <includeGroup name="Gateway Load Monitor">
       <include>
         <priority>10601</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/gateway/load/record.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
     </includeGroup>
     ```

  2. Open the Gateway Setup Editor for your Gateway
  3. Right click on the Includes section
  4. Select Paste XML
  5. Validate and save

### On the Monitoring Gateway

You should probably create a new Gateway specifically to monitor load on your other Gateways. This is not required, but you may encounter issues if you cannot ensure the load of the monitoring Gateway is low.

* To use these files directly from this site:

  1. Copy the XML below:

     ```xml
     <includeGroup name="Geneos Load Monitor">
         <include>
             <priority>10401</priority>
             <required>false</required>
             <location>https://itrs-group.github.io/geneos-includes/gateway/load/monitor.xml</location>
             <reloadInterval>300</reloadInterval>
         </include>
         <include>
             <priority>10402</priority>
             <required>false</required>
             <location>https://itrs-group.github.io/geneos-includes/gateway/load/monitor_rules.xml</location>
             <reloadInterval>300</reloadInterval>
         </include>
     </includeGroup>
     ```

  2. Open the Gateway Setup Editor for your Gateway
  3. Right click on the Includes section
  4. Select Paste XML
  5. Validate and save

* To use local copies, which is recommended for any production environment, download the files by selecting the names in the list above and then change the location path(s) once you have pasted the XML into your configuration.

#### Configuring Monitoring

For each Gateway you want to monitor you must create a Managed Entity. Optionally you can also collect some system data for each Gateway is you also deploy a Netprobe. Only one Netprobe per host, not Gateway, is required in this case.

```xml
<managedEntity name="Example Load Monitor">
  <probe ref="localhost"></probe>
  <addTypes>
      <type ref="Gateway Load Monitor"></type>
      <type ref="Gateway Environment"></type>
  </addTypes>
  <var name="gwLoadDirectory">
      <string>/path/to/directory/containing/stats/file</string>
  </var>
  <var name="gwLoadGatewayName">
      <string>Example</string>
  </var>
</managedEntity>
```

If you cannot deploy a new Netprobe then you should create a Managed Entity mounted on a Virtual Probe, like this:

```xml
<managedEntity name="Example Load Monitor">
  <virtualProbe ref="Virtual Probe"/>
  <addTypes>
    <type ref="Gateway Load Monitor"></type>
  </addTypes>
  <var name="gwLoadDirectory">
    <string>/path/to/directory/containing/stats/file</string>
  </var>
  <var name="gwLoadGatewayName">
    <string>Example</string>
  </var>
</managedEntity>
```

Note that the `Gateway Environment` Type is not used as this requires plugins that are only available on a Netprobe.

There are a number of variables that can be used to override default behaviour:

* For Load Monitoring
  * `gwLoadDirectory` - Default `/tmp`

    This must be set to the directory containing the stats file. It is also used to locate the Gateway log file, see below.
  * `gwLoadStatsFile` - Default `stats.xml`

    The name of the stats file your gateways write to. You may wish to changes this if you write to a file containing the name of the gateway in a common directory.
  * `gwLoadSampleInterval` - Default 20 seconds

    The sample interval for the Gateway Load samplers. This should always be less than half of the shortest history period you configure, e.g. 30 seconds or less if you want to see valid per-minute data.
  * `gwLoadHistoryColumns` - Default `[ cpu1Min, cpu5Min ]` Also available `[ cpuHour, cpuDay ]`

    A string list variable that sets which additional columns are added to the many of the dataviews. If the columns exist then Rules run to populate them using History Periods and a delta calculation. Enabling all of these can be expensive in both CPU and memory and will also delay the Gateway start-up. The default lets you see the percentage of CPU used for each metric for the previous 1 and 5 minutes, but you can also add the last hour and last day by redefining this string list variable.
  * `gwLoadDatabaseColumns` - Default `[ cpu1Min, cpu5Min, updates1Min, updates5Min ]` - Also available `[ cpuHour, cpuDay, updatesHour, updatesDay ]`

    As above, you can change the columns calculated and displayed in the Database Logging monitor views, as required. The defaults show the CPU percentage and volume of updates for the last minute and last 5 minutes.

* For Additional Environment Monitoring (with Netprobe)
  * `gwLoadGatewayName` - Default `none`, Required

    This should be set to an identifier that can be used to match the Gateway process. Normally this is the Gateway name.
  * `gwLoadLogFile` - Default `gateway.log`

    The gateway log file name. Normally does not need to be changed.
  * `gwLoadEnvInterval` - Default 5 seconds

    The sample interval for the environment samplers.
  * `gwLoadEnvHistoryColumns` - Default `[ hour, day ]`

    As above for `gwLoadHistoryColumns` this string list can be used to limit which additional columns are calculated. As Data Quality statistics are published to the Gateway log every 10 minute there are only two calculated columns for the last hour and last day, and in both cases they show the maximum values for each period. The data ijn the Gateway log is not granular enough to produce averages and so on.

## More Details

The configurations above include a number of settings that have been chosen to work well in most environments. The main monitor.xml file sets these value, which can be overridden in the main Gateway configuration file:

* Rule Threads - Set to 4

  While the default value for this is to not use rule threads, setting this to 4 gives a good balance of available CPU resources versus processing latency.
* Persistence - Write period 30, Rewrite period 500

  History Period data, along with some others, can be saved to a local file on a regular cycle so that in case of a Gateway restart this data can be re-loaded and continue to be useful. The standard times have been increased to reduce continual small updates to local disk.
* Rule Startup Delay - 45 seconds

  As the majority of the work is Rule processing and there is no data when the Gateway starts up, we delay the start of Rule processing by 45 seconds to give the Gateway plenty of time to make initial connections and so on. The 45 seconds is less than the default sample interval of 60 seconds, so Rules should be running before the first samples are taken.
* No sample on start-up - False

  Sampling on start-up has been set to false for all the samplers. Just like the Rule Startup Delay above, this gives the Gateway plenty of time to reach a stable, quiescent state.

The choice of which samplers to use and the specific configuration of groupings has been made based on prior experience but there may be many other combinations of filters, grouping etc. on the Gateway Load plugins that would work better in your environment. We welcome feedback and additional proposals.

Finally; Database Logging, while configured for very basic load monitoring, has not been given the detailed review that the other components have at this stage. Again, we welcome feedback and changes.
