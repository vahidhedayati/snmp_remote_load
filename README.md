snmp_remote_load
================

nagios snmp remote load check - against auto cpu count (Linux)


http://exchange.nagios.org/directory/Plugins/System-Metrics/CPU-Usage-and-Load/snmp_remote_load/details

# This script takes:
# -H -l -p
 Once given a hostname and community string with or without specific load limit
 with no load limit, using snmp it will look at amount of cpu's and calculate load average / cpu count -
 -- UPDATES - debian - ubuntu systems run:
 sudo apt-get install snmp-mibs-downloader


This has now been updated in the uploaded script, you can revert back to old commented method if you do not with to use HOST-RESOURCES-MIB and find the old commented out method works for you.

Anyhow when executed this is what happens, it returns the actual cpus on the machine, the script has been set to increment by two for any amount found so that we begin to get alerts when the load is really really high rather than as soon as it hits a 100%. This is another hack found further in the script where cpu_count++ occurs twice


# Help -h
./snmp_remote_load -h
./snmp_remote_load -H localhost -l 1 -p public
./snmp_remote_load -H localhost -p public
-----------------------------------
-H is hostname set to localhost
-l is load limit set to 1 or not set at all
-p is snmp passsword
-----------------------------------
You can leave the load blank , this then defines the load according to CPU's
so a 4 cpu server will have a load set to 6 if it goes above 4 then alerts are sent



# Auto load check against CPU count
./snmp_remote_load -H localhost -p public
./snmp_remote_load -H localhost -l -p public
|Actual_cpus:_4|Alert_Limit:6|-|1 minute load->OK:1.59167|-|5 minute load->OK:1.545|-|15 minute load->OK:1.575|-|FINAL_RESULT:GOOD

./snmp_remote_load -H localhost -p public
|Actual_cpus:_4|Alert_Limit:6|-|1 minute load->OK:1.59167|-|5 minute load->OK:1.545|-|15 minute load->OK:1.575|-|FINAL_RESULT:GOOD


# Define a limit
./snmp_remote_load -H localhost -l 1 -p public
|Actual_cpus:_4|Alert_Limit:1|-|1 minute load->CRITICAL:2.35833|-|5 minute load->CRITICAL:1.78333|-|15 minute load->CRITICAL:1.59833|-|FINAL_RESULT:CRITICAL


# Command configuration
define command {

command_name check_load_remote

command_line $USER1$/snmp_remote_load -H $HOSTADDRESS$ -l $ARG1$ -p $ARG2$

}



define command {

command_name check_auto_load_remote

command_line $USER1$/snmp_remote_load -H $HOSTADDRESS$ -p $ARG1$

}



# Service configuration
define service{

use ....

hostgroup_name generic-uat

service_description cpu_load

check_command check_auto_load_remote!public!

}


# this will set the load max level to 2
define service{

use ....

hostgroup_name generic-uat

service_description cpu_load

check_command check_load_remote!2!public!

}
