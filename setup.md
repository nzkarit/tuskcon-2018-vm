These are some instruction I put together to get the VM up and running. I have been working on on Ubuntu 18.04 Desktop.

# PreReq
1. `sudo apt install git cmake libusb-dev libusb-1.0-0-dev open-vm-tools build-essential librtlsdr-dev pkg-config python3-numpy`

# Setup RTLSDR
1. `git clone git://git.osmocom.org/rtl-sdr.git`
1. `cd rtl-sdr`
1. `mkdir build`
1. `cd build`
1. `cmake ../ -DINSTALL_UDEV_RULES=ON`
1. `make`
1. `sudo make install`
1. `sudo ldconfig`
1. `sudo cp ../rtl-sdr.rules /etc/udev/rules.d/`
1. `sudo vi /etc/modprobe.d/blacklist-rtl.conf`
1. Add `blacklist dvb_usb_rtl28xxu`
1. Reboot

## Test RTLSDR
1. Plug in an rtlsdr
1. `rtl_test`

# Setup HackRF
1. `sudo apt install hackrf`

## Test HackRF
1. `sudo hackrf_info`

# Setup dump1090
1. `git clone https://github.com/MalcolmRobb/dump1090.git`
1. `cd dump1090`
1. make

## Test dump1090
1. `./dump1090 --net`
1. Browse to http://localhost:8080/

# Setup ADSB Out
1. `git clone https://github.com/nzkarit/ADSB-Out.git`


## Test ADSB Out
1. `cd dump1090`
1. `./dump1090 --net --freq 915000000`
1. Browse to http://localhost:8080/
1. `cd ADSB-Out`
1. `./ADSB_Encoder.py`
1. `sudo hackrf_transfer -t Samples_256K.iq8s -f 915000000 -s 2000000 -x 10`
1. In browser observe a plane on the map
