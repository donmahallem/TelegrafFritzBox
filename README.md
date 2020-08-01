[![MIT license](https://img.shields.io/github/license/Schmidsfeld/TelefrafFritzBox?color=blue)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/Schmidsfeld/TelefrafFritzBox/compare)
[![made-with-python](https://img.shields.io/badge/Python-3.7%2C%203.8-green)](https://www.python.org)
[![HitCount](http://hits.dwyl.com/Schmidsfeld/TelefrafFritzBox.svg)](http://hits.dwyl.com/Schmidsfeld/TelefrafFritzBox)

# TelefrafFritzBox
A Telegraf collector written in Phthon, gathering several crucial metrics for the Fritz!Box router by AVM via the TR-064 protocol. This collection includes a main phyton3 script, a telegraf config file and a Grafana dashboard.

The communication with the router is based on the FritzConnection library https://github.com/kbr/fritzconnection by Klaus Bremer.

Forking and modifying this script is explicitly encouraged (hence you most likely need to adjust for your situation). I would appreciate if you drop me a note f you implement other stuff so I can backport it into the main script.


## The End Result
Information that you get
* Full status of your current and past transfer speeds
* Current and possible line speeds
* The line dampening and noise margin
* The errors occurring the line
* Local networks (LAN and WLAN traffic)
* connected WLAN clients

![Grafana dashboard](FritzBoxDashboard.png?raw=true)

## Details
### Concept
The script utilizes a single connection to the FritzBox router with the FritzConnection library. From there it reads out several service variable collections via TR-064. Note, that for performance reasons only one connection is established and every statistics output is only requested once. From the dictionary responses containing several variables each, the desired variables are extracted manually and parsed. The parsed arguments are then formatted appropriately as integers or strings according to the influxDB naming scheme. Lastly the gathered information is output as several lines in the format directly digested by Telegraf / InfluxDB.

### Output
* The output is formatted in the influxDB format. 
* By default the influxDB dataset FritzBox will be generated
* All datasets are tagged by the hostname of the router and grouped into different sources
* All names are sanitized (no "New" in variable names)
* All variables are cast into appropriate types (integer for numbers, string for expressions and float for 64bit total traffic)

![Grafana dashboard](OutputScript.png?raw=true)

## Install
Several prerequisites need to be met to successfully install this script and generate the metrics. Some seem to be obvious but will be mentioned here for sake of complete documentation. 
* Have an operational TIG stack (Telegraf, InfluxDB Grafana) with all of them installed and operational.
* Activate the TR-064 protocoll in the Fritzbox (Heimnetz -> Netzwerk -> Netzwerkeinstellungen)
* Have a dedicated user on the Fritz!Box (the username:Telegraf is assumed, but any will do)
* Install python3 and pip  (for example: apt install python3-pip)
* Locally clone the fritzconnection library (pip3 install fritzconnection)
* Clone this project (git clone https://github.com/Schmidsfeld/TelefrafFritzBox/
* Edit the python file and adjust the credentials (nano ./TelefrafFritzBox/telegrafFritzBox.py)
* Make it executeamle (chmod +x ./TelefrafFritzBox/telegrafFritzBox.py)
* Test the output (python3 ./TelefrafFritzBox/telegrafFritzBox.py)
* Copy it to the appropiate locatio (cp ./TelefrafFritzBox/telegrafFritzBox.py /usr/local/bin)
* Copy the Telegraf config file to the correct location or append it to your current file (cp ./TelefrafFritzBox/telegrafFritzBox.conf /etc/telegraf/telegraf.d)
* Restart your Telegraf service (systemctl restart telegraf)
* Load your Grafana Dashboard




