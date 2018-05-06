These are the commands to help you during the workshop.

# OS Creds
If you are using the provided VM these are the creds to use:
* User: `tuskcon`
* Password: `tuskcon`

# Before we start
Ensure that have a copy of the latest code and Git Pull everything
1. `cd ADSB-Out`
1. `git pull`
1. `cd ../tuskcon-2018-vm`
1. `git pull`

# dump1090
dump1090 is a tool for the RTLSDR which will take ADS-B signals. You will need a RTLSDR to run this tool.

## Run dump1090 actual planes
1. `cd dump1090`
1. `./dump1090 --net`
1. Browse to http://localhost:8080/

## Run dump1090 with ADSB-Out
1. `cd dump1090`
1. `./dump1090 --net --freq 915000000`
1. Browse to http://localhost:8080/

# ADSB-Out
[ADSB-Out](https://github.com/nzkarit/ADSB-Out) is a tool for broadcasting ADS-B signals. **DO NOT** broadcast on the aviation frequency. Broadcast on an ISM band such as 915MHz. When using `hackrf_transfer` use the `-f 915000000` flag to broadcast on 915MHz. You will need a HackRF to run this tool.

## Broadcast Single plane
1. `cd ADSB-Out`
1. `./ADSB_Encoder.py`
1. `sudo hackrf_transfer -t Samples_256K.iq8s -f 915000000 -s 2000000 -x 10`

## Broadcast a single entry 10 times
The `-r` for ADSB_Encoder.py when generating the samples, will repeat X times. If a CSV file is being used it will send each row that many times. There is currently no configurable gap between the messages.
1. `cd ADSB-Out`
1. `./ADSB_Encoder.py -r 10`
1. `sudo hackrf_transfer -t Samples_256K.iq8s -f 915000000 -s 2000000 -x 10`

## Broadcast from a CSV
1. `cd ADSB-Out`
1. `./ADSB_Encoder.py --csv example.csv`
1. `sudo hackrf_transfer -t Samples_256K.iq8s -f 915000000 -s 2000000 -x 10`

## ADSB-Out help
ADSB_Encoder.py has built in help which can be access by running:
* `./ADSB_Encoder.py --help`
The help is as follows:
```
$ ./ADSB_Encoder.py --help
usage: ADSB_Encoder.py [-h] [-i ICAO] [--lat LATITUDE] [--lon LONGITUDE]
                       [-a ALTITUDE] [--ca CAPABILITY] [--tc TYPECODE]
                       [--ss SURVEILLANCESTATUS] [--nicsb NICSUPPLEMENTB]
                       [--time TIME] [-s SURFACE] [-o OUTPUTFILENAME]
                       [-r REPEATS] [--csv CSVFILE]

This tool will generate ADS-B data in a form that a hackRF can broadcast. In
addition to providing the information at the command the defaults can be
changed in the config.cfg file and the the logging config changed in
logging.cfg.

optional arguments:
  -h, --help            show this help message and exit
  -i ICAO, --icao ICAO  The ICAO number for the plane in hex. Ensure the ICAO
                        is prefixed with '0x' to ensure this is parsed as a
                        hex number. This is 24 bits long. Default: 0xABCDEF
  --lat LATITUDE, --latitude LATITUDE
                        Latitude for the plane in decimal degrees. Default:
                        12.34
  --lon LONGITUDE, --long LONGITUDE, --longitude LONGITUDE
                        Longitude for the place in decimal degrees. Default:
                        56.78
  -a ALTITUDE, --alt ALTITUDE, --altitude ALTITUDE
                        Altitude in decimal feet. 12 bits. Default: 9876.5
  --ca CAPABILITY, --capability CAPABILITY
                        The capability. (Think this is always 5 from ADS-B
                        messages. More info would be appreciated). 5 indicates
                        that the responder is capable of Communication A and
                        B, and the plane is not on the ground. 3 bits.
                        Default: 5
  --tc TYPECODE, --typecode TYPECODE
                        The type for the ADS-B message. 11 is an air position
                        message. See https://adsb-decode-guide.readthedocs.io/
                        en/latest/content/introduction.html#ads-b-message-
                        types for more information. 5 bits. Default: 11
  --ss SURVEILLANCESTATUS, --surveillancestatus SURVEILLANCESTATUS
                        The surveillance status. (Think this is always 0 from
                        ADS-B messages. More info would be appreciated).
                        Default: 0
  --nicsb NICSUPPLEMENTB, --nicsupplementb NICSUPPLEMENTB
                        The NIC supplement-B.(Think this is always 0 from
                        ADS-B messages. More info would be appreciated).
                        Default: 0
  --time TIME           The time. (Think this is always 0 from ADS-B messages.
                        More info would be appreciated). Default: 0
  -s SURFACE, --surface SURFACE
                        If the plane is on the ground or not. Default: False
  -o OUTPUTFILENAME, --out OUTPUTFILENAME, --output OUTPUTFILENAME
                        The iq8s output filename. This is the file which you
                        will feed into the hackRF. Default: Samples_256K.iq8s
  -r REPEATS, --repeats REPEATS
                        How many repeats of the data to perform. Default: 1
  --csv CSVFILE, --csvfile CSVFILE, --in CSVFILE, --input CSVFILE
                        Import a CSV file with the plane data in it. Default:
```
# Making a CSV
The CSV parsing is dynamic and so long as there are column names which match the names in the [config.cfg](https://github.com/nzkarit/ADSB-Out/blob/master/config.cfg) the application should be able to make it work. There needs to be column names in row 1 and it is expecting that the delimiter is a comma.

There is an example above which demonstrates how to run using a CSV file.

# CSV Generator
These are some details on the CSV generator scripts that will generate CSV files which will target one aspect of the ADS-B message which you are wanting to stress.

## generateAllICAO.py
1. `cd ADSB-Out`
1. In generateAllICAO.py set minICAO and maxICAO for how many planes you wish generate. This is in hex by default.
1. `./generateAllICAO.py`
1. `python3 allICAO.py`
1. `sudo bash hackRFAllICAO.sh`

## generateAllLat.py
1. `cd ADSB-Out`
1. In generateAllLat.py set minLat and maxLat for how many latitudes you wish generate, step is what the step size in the latitude is going to be.
1. `./generateAllLat.py`
1. `python3 allLat.py`
1. `sudo bash hackRFAllLat.sh`

## generateAllAlt.py
1. `cd ADSB-Out`
1. In generateAllAlt.py set minAlt and maxAlt for how many altitudes you wish generate, step is what the step size in the altitude is going to be. There is a bug that altitudes > 50175 feet are not handled.
1. `./generateAllAlt.py`
1. `python3 allAlt.py`
1. `sudo bash hackRFAllAlt.sh`
