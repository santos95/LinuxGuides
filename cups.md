# CUPS (Common unix printing system)
## Provide a standarized way to manage printers and print jobs
## Support local and network printers
## Support a wide range of printers
## Allow printing over a network using IPP (Internet Printing Protocol)
## Web interface
## Access Control - Manage user permissions for printing
## Print job queues - Handles and prioritizes print jobs

### install cups
    sudo apt-get update 
    sudo apt-get install cups

### Enable and start cups 
    sudo systemctl enable cups
    sudo systemctl start cups

### check cups status
    systemctl status cubs

### access web interface
    http://localhost:631

### add user to lpadmin group
    sudo usermod -aG lpadmin $USER

### Managing printers with cups
#### List printers    
    lpstat -p
#### set default printer
    lpadmin -d PRINTER_NAME

#### print a test page
    lp -d PRINTER_NAME /etc/nsswitch.conf

#### remove a printer
    lpadmin -x PRINTER_NAME

#### to add a new printer using lpadmin 
#####  find available printers - shows availables printesr
    lpinfo -v

    # example
    network ipp://192.168.1.100/ipp
    direct usb://HP/LaserJet_1020

##### to add a printer
    sudo lpadmin -p MyPrinter -E -v DEVICE_URI -m DRIVER_NAME
    sudo lpadmin -p HP-test -E -v //192.168.1.100/ipp
    lpadmin -p hpM604 -m drv:///sample.drv/generpcl.ppd -v socket://172.16.199.22/ -E

    the command does:
    Adds a printer named hpM604 to CUPS.
    Uses a generic PCL driver (generpcl.ppd).
    Connects to a network printer at IP 172.16.199.22 using the JetDirect (port 9100) protocol. -use websocket
    Enables the printer so it can start receiving jobs immediately.

    sudo lpadmin -p OfficePrinter -E -v ipp://192.168.1.100/ipp -m everywhere

###### set the printer as default 
    lpadmin -d MyPrinter
###### show the status of installed printers and the default printer  - to check if printer is enable and ready
###### -p lista all configured printers and their current status -d display default printer
lpstat -p -d
### File locations
#### main config
    cat /etc/cups/cupsd.conf
#### log file
    cat /var/log/cups/
#### printers list
    cat /etc/cups/printers.conf

### troubleshooting cups issues
#### restart cups service
    sudo systemctl restart cups
#### check logs for errors
    sudo tail -fn 100 /var/log/cups/error_log
#### test printing - show all the status info
    lpstat -t
#### check default printer quicly - diagnosing printer setup issues 
    lpstat -s # similar -t but summary
##### show a list of info like 
    scheduler is running
    system default destination: HP_LaserJet # default printer
    device for HP_LaserJet: usb://HP/LaserJet?serial=XYZ123 # device uris for installed printers
    # list of available printers - show all installed printers and their current state
    printer HP_LaserJet is idle.  enabled since Fri 12 Jan 2024 02:30:00 PM UTC
    # Displays the print job queue and any pending jobs.
    HP_LaserJet accepting requests since Fri 12 Jan 2024 02:30:00 PM UTC
