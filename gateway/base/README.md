# Base Include Files

This page contains baseline Gateway include files for typical deployments and also seeks to establish sensible defaults.

* [`common.xml`](common.xml)
  This file contains:
  * A set of standard global variables with all the available `Macro` types which have the same names as their Macro type. They are described in the [documentation](https://docs.itrsgroup.com/docs/geneos/current/Gateway_Reference_Guide/gateway_user_variables_and_environments.htm#environments_environment_var_macro)
  * Setting the number of [Rule threads](https://docs.itrsgroup.com/docs/geneos/current/Gateway_Reference_Guide/gateway_operating_environment.htm#operatingEnvironment_numRuleEvaluationThreads) to 4, which is reasonable for most hosts.
  * Setting the [DNS cache expiry time](https://docs.itrsgroup.com/docs/geneos/5.14.0/Gateway_Reference_Guide/gateway_operating_environment.htm#operatingEnvironment_dnsCacheExpiryTime) to 5 minutes rather than the default 12 hours, so that your Gateway will pick up changes to DNS in line with more typical TTLs.

## How to use

To use these files directly from this site:

  1. Copy the XML below

     ```xml
     <includeGroup name="Geneos Base Includes">
       <include>
         <priority>10050</priority>
         <required>false</required>
         <location>https://itrs-group.github.io/geneos-includes/gateway/base/common.xml</location>
         <reloadInterval>300</reloadInterval>
       </include>
     </includeGroup>
     ```

  2. Open the Gateway Setup Editor for your Gateway
  3. Right click on the Includes section
  4. Select Paste XML
  5. Validate and save
  
To use a local copy, which is recommended in any production environment, download the files by selecting the names in the list above and then change the location path(s) once you have pasted the XML into your configuration.
