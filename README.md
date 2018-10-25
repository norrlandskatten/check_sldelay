# check_sldelay
Nagios plugin that check delays in Stockholms public transport. The plugin calculates the percentage of departures from a given station that are more than specified minutes delayed.

Installation guide for OP5 Monitor below.

## Prerequisites
**jq** installed on your OP5 Monitor server. Installation instructions can be found [here](https://stedolan.github.io/jq/download/).

Install on CentOS7: `yum install jq`

**Trafiklab** API key. You can obtain a API key for free at [trafiklab.se](https://www.trafiklab.se).

Once you have created an account at Trafiklab you have to create a project and specify that you want to use the API for *SL Realtidsinformation 4*.

## Installation
### Download and prepare plugin
Download the check_sldelay plugin to your monitoring host.

```
wget -O /opt/plugins/custom/check_sldelay https://raw.githubusercontent.com/norrlandskatten/check_sldelay/master/check_sldelay
```

Make it executable.

```
chmod +x /opt/plugins/custom/check_sldelay
```

Edit check_sldelay and add your API key.

```
vim /opt/plugins/custom/check_sldelay
```

```
[...]
#User defined variables
APIKEY="PASTE_YOUR_KEY_HERE"
[...]
```
## Usage
```
Usage: check_sldelay -T <type> -s <site> -w <warning-minutes> -c <critical-minutes> -W <warning-percent> -C <critical-percent>

Options:

  --help or -h
      Print help message

  --version or -V
      Print version of plugin

  --type or -T
      Select type of transportation
      valid input is BUS, METRO or TRAIN

  --site or -s
      Specify site to check delay at
      siteid from SL Platsuppslag
      example 1002 for centralen or 9192 for slussen

  --warning-minutes or -w
      Warning threshold in minutes

  --critical-minutes or -c
      Critical threshold in minutes

  --warning-percent or -W
      Warning threshold in percent

  --critical-percent or -C
      Critical threshold in percent

Example:

  $ /opt/plugins/custom/check_sldelay -T METRO -s 9192 -w 1 -c 10 -W 1 -C 20
  CRITICAL - 25 percent of the departures are more than 10 minute(s) delayed | 'percent'=25%;1;20;;
  ```

### Add command to Monitor
Add new command to OP5 Monitor.

Navigate to Manage > Configure > Commands.

Add the following information:

**command_name:** `check_sldelay`

**command_line:** `$USER1$/custom/check_sldelay -T $ARG1$ -s $ARG2$ -w $ARG3$ -c $ARG4$ -W $ARG5$ -C $ARG6$`


Click "Submit" then "Save" to save the new command.

### Add service
Navigate to the host object you want to use or create a new one and add a new service.

Example:

**service_description:** `Delayed metros at Centralen`

**check_command:** `check_sldelay`

**check_command_args:** `METRO!1002!5!20!10!20`

Click "Submit" then "Save" to save the new service.
