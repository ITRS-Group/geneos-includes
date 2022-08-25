# Legacy Includes

This page contains include files sourced from `geneos-utils` 4.10.1 distribution. They are preserved here unchanged for simple integrations.

* [GLOBAL_Administration.xml](GLOBAL_Administration.xml)
* [GLOBAL_Authentication.xml](GLOBAL_Authentication.xml)
* [GLOBAL_Infrastructure.xml](GLOBAL_Infrastructure.xml)
* [ICP_Source.xml](ICP_Source.xml)
* [POC_Infrastructure.xml](POC_Infrastructure.xml)

## Using these files

If your Gateways have Internet access then you can use any of these directly from this site by doing the following:

1. Copy the XML below

     ```xml
     <includeGroup name="Geneos Includes">
       <include disabled="true">
         <priority>8020</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/legacy/GLOBAL_Administration.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
       <include disabled="true">
         <priority>8050</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/legacy/GLOBAL_Authentication.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
       <include disabled="true">
         <priority>8070</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/legacy/GLOBAL_Infrastructure.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
       <include disabled="true">
         <priority>9020</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/legacy/ICP_Source.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
       <include disabled="true">
         <priority>9040</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/legacy/POC_Infrastructure.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
     </includeGroup>
     ```

2. Open the Gateway Setup Editor for your Gateway
3. Right click on the Includes section
4. Select Paste XML
5. Select each include you want and right-click and Enable (or CTRL-D)
6. Validate and save

The Required set to false and the Reload interval of 5 minutes means that even if your Gateway can no longer reach this site, the files will continue to be available through the `cache/` directory, but you will not be able to view or edit them.