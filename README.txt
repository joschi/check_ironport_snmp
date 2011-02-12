NAME
    check_ironport_snmp - Nagios plugin to monitor Cisco IronPort Email Security
                          Appliances via SNMP


REQUIREMENTS
    * You will need a recent version of Net-SNMP <http://www.net-snmp.org/>
    * You will have to load the MIBs ASYNCOS-MAIL-MIB.txt and IRONPORT-SMI.txt
      which you can download from IronPort's support page <http://www.cisco.com/web/ironport/>
    * You will need to activate SNMP on your IronPort Email Security Appliance(s)


DESCRIPTION
    This script acts as a plugin module for the Nagios IT infrastructure
    monitoring system. It polls an IronPort Email Security Appliance for status
    information through SNMP, parses for the requested value, compares
    that value against warning and critical levels, and responds with a
    status string and appropriate exit status.

    This script handles authentication (only when using SNMPv3) and the
    following checks:

    *  cpu (CPU utilization, measured in percent)
    *  mem (Memory utilization, measured in percent)
    *  memoryavail (Memory availability status, does not require thresholds)
    *  diskio (Disk I/O utilization, measured in percent)
    *  queue (Queue utilization, measured in percent)
    *  queueavail (Queue availability status, does not require thresholds)
    *  resourceconservation -> Resource Conservation Reason, does not require thresholds)
    *  temperature (Device temperature, measured in °C)
    *  raid (RAID status, does not require thresholds)
    *  fan (Fans status, does not require thresholds)
    *  psstatus (Power supply status, does not require thresholds)
    *  psredundancy (Power supply redundancy, does not require thresholds)
    *  openfiles (Open files or sockets)
    *  mailtransferthreads (running mail transfer threads)
    *  dns (Pending and outstanding DNS requests)


    This has been tested with two Cisco IronPort C160 appliances and a
    Nagios 3.2 installation under Ubuntu Linux 10.04. 

    You will ideally want to use a secured connection over SNMPv3 using a
    passphrase provided with the parameter -P. The user name defaults to
    'v3get' in this case.
    At very minimum, it would be good practice to whitelist any IPs which
    will need access to status information on the IronPort Appliance.


SYNOPSIS
  Command Line Interface
    Poll CPU utilization with SNMPv2c without encryption or authentication:

        check_ironport_snmp -H example.com -C public -t cpu -w 90 -c 98

    Poll RAID status with SNMPv3:

            check_ironport_snmp -H example.com -v 3 -C my_community -P secret \
            -t raid

  Running within Nagios
    In your command definitions (e.g. objects/commands.cfg):

            define command{
	            command_name    check_ironport
	            command_line    $USER1$/check_ironport_snmp -H $HOSTNAME$ \
                                      -C $ARG1$ -t $ARG2$ -c $ARG3$ -w $ARG4$
            }
            
	        define command{
	            command_name    check_ironport_auth
	            command_line    $USER1$/check_ironport_snmp -H $HOSTNAME$ -v 3 \
                                      -C $ARG1$ -P $ARG2$ -t $ARG3$ -c $ARG4$ -w $ARG5$
            }

    In the configuration file for your IronPort Email Security Appliance:

	define service{
		use                   my-service-template
		host_name             example.com
		service_description   CPU Utilization
		check_command         check_ironport!public!cpu!90!95
	}

	define service{
		use                   my-service-template
		host_name             example.com
		service_description   Open Files or Sockets
		check_command         check_ironport!public!openfiles!200!300
	}

	define service{
		use                   my-service-template
		host_name             example.com
		service_description   Queue Utilization
		check_command         check_ironport_auth!public!secret!queue!80!90
	}

	define service{
		use                   my-service-template
		host_name             example.com
		service_description   Memory Utilization
		check_command         check_ironport_auth!public!secret!mem!90!95
	}


AUTHOR
    This script is written and maintained by Jochen Schalanda
    <jochen@schalanda.name> and is available on GitHub, at
    <https://github.com/joschi/check_ironport_snmp>.


LICENSE
    Copyright (c) 2011 Jochen Schalanda

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in
    all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
    THE SOFTWARE.
