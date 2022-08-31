# Load Monitoring Include Files

This page contains Gateway include files for monitoring the load of your Geneos Gateways. The primary challenge when monitoring a busy Gateway, either for diagnostic purposes or to be alerted if load exceeds desired limits, is that the Gateway itself may stop responding and have a backlog of monitored data (the 'Max Data Age'). This can be addressed by letting your Gateways write snapshots of internal data to an XML file which can then be loaded and processed by a less loaded Gateway. The stats file is written from it's own thread and so it not generally impacted by peaks in processing load in the Gateway process.

There are three include files:

* [`record.xml`](record.xml)
  * To enable your Gateway(s) to write out stats files you can either make a small change to your configuration by hand or use this include file.
* [`monitor.xml`](monitor.xml)
  * This include contains the basic configuration to load stats files for any number of Gateways on the same host and to transform some of the counters into more useful CPU percentages over known periods. To use it you must create Managed Entities, one per Gateway being monitored, and set some variables to suit.
    Note: At this time, there are no Rules defined.
* [`monitor_rules.xml`](monitor_rules.xml)
  * This include contains a number of Rules to apply to the views created in the `monitor.xml` above but have been separated out as the chosen thresholds and other logic may not suit all users. Start with this file but change it as you learn more about the behaviour of Geneos Gateways in your environment.

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
     <includeGroup name="Gateway Load">
       <include>
         <priority>10601</priority>
         <required>true</required>
         <location>https://itrs-group.github.io/geneos-includes/gateway/load/record.xml</location>
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
     <includeGroup name="Geneos Load Monitoring">
         <include>
             <priority>10401</priority>
             <required>true</required>
             <location>https://itrs-group.github.io/geneos-includes/gateway/load/monitor.xml</location>
         </include>
         <include>
             <priority>10402</priority>
             <required>true</required>
             <location>https://itrs-group.github.io/geneos-includes/gateway/load/monitor_rules.xml</location>
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
      <type ref="Gateway Load"></type>
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
    <type ref="Gateway Load"></type>
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
    The name of the stats file your gateways write to. You may wish to changes this if you write to a file containing the name of the gateway in a commmon directory.
  * `gwLoadSampleInterval` - Default 60 seconds
    The sample interval for the Gateway Load samplers.
  * `gwLoadHistoryColumns` - Default `[ cpu1Min, cpu5Min, cpuHour, cpuDay]`
    A string list that can be used to limit - but not add to - the time interval columns used to present the calculated CPU loads. If the number of Gateways being monitored is large then the load of on monitoring gateway can also increase. by removing unwanted columns you can reduce both the Rule load and the volumn of History Periods used.

* For Additional Environment Monitoring (with Netprobe)
* `gwLoadGatewayName` - Default `none`, Required
  This should be set to an identifier that can be used to match the Gateway process. Normally this is the Gateway name.
* `gwLoadLogFile` - Default `gateway.log`
  The gateway log file name. Normally does not need to be changed.
* `gwLoadEnvInterval` - Default 5 seconds
  The sample interval for the environment samplers.
* `gwLoadEnvHistoryColumns` - Default `[ hour, day]`
  As above for `gwLoadHistoryColumns` this string list can be used to limit which additional columns are calculated.

