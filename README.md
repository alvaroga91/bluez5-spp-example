# spp-echo-server

Simple Bluetooth SPP echo server based on BlueZ 5 calls

Tested to work in a Colibri iMX6ULL with BlueZ 5.46, with Android's Serial Bluetooth Terminal and Windows 10 Bluetooth COM feature (set as ongoing):
https://superuser.com/questions/1237268/making-a-bluetooth-device-recognized-as-a-com-port

How to use:
Connect to or your from other device using your communication manager. Is highly likely that you won't have SPP available on the end you are launching this but you will have other services available.
Works better if communication is trusted. 

Launch this application and service should become available from other ends software. 

# Original comments

This is an example Bluetooth Serial Port Profile client and server
application which uses bluez 5.x to exercise the Bluetooth Serial
Port Profile (1.2).

To run as a server, just invoke from the command-line using 'sudo' and
no arguements.

To run as a client, adding any string after the command name will trigger
this mode.

First, the application registers itself to bluez using the ProfileManager1's
RegisterProfile method.  It will indicate to bluez what role it's playing
(client or server).  If run as server, this will cause the BT controller to
list the Serial Port profile when the bluetoothctl 'show' command is run.
Likewise, from a remote device, the bluetootctl 'info' command will also show
the Serial Port profile listed for the device.  When run a client, the Serial
Port profile will not be listed on either side.

When a connection is triggered on the client, bluez will call the NewConnection
method and pass an active Bluetooth RFCOMM socket which then can be used to
write data to the server.  On the server side, if the client device is paired,
but trusted, then the default agent needs to be configured (eg. agent on,
default-agent), and if so, a prompt will be displayed asking an admin to
confirm the incoming server request.

If trusted, or an admin confirms the service request, then the server's
NewConnection method will be called with a fd representing the other end of
the BT socket.

In the current implementation, the IO is done very simply with read/write calls.
Also, the server explicitly sets the socket to blocking mode to simplify the
code. When boths sides complete their IO, the application exits.

To trigger the connection from client to server, the following dbus-send
command can be used:

$ sudo dbus-send --system --print-reply --dest=org.bluez \
  /org/bluez/hciX/dev_XX_YY_ZZ_AA_BB_CC \
  org.bluez.Device1.ConnectProfile \
   string:"00001101-0000-1000-8000-00805f9b34fb"

 * hciX - replace X with controller index (eg. hci0)
 * dev_XX_YY_ZZ_AA_BB_CC - replace with server's BT control MAC address

