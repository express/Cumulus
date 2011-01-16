
Cumulus
=======================================

Cumulus is a crossplatform "rendezvous" service to assist P2P connection in RTMFP peer's communication. In RTMFP protocol, Cumulus plays the server role.
Cumulus can be used as a library to include in your "publisher" software, or be installed as a service on your server computer.

We remind you that Cumulus is licensed under the [GNU General Public License], so **CumulusLib can't be linked with any closed source project** (see [license]).

Status
------------------------------------
Cumulus is in development, much work remains to be done.
If you are a developer **help us** to evolve and enhance Cumulus, else you can always make a **donation** ([us]|[eu]) for that we spent more time on it, in fact it's not technical skills that hinder us but lack of time.
Also with the intention to understand better the exchange between a official RTMFP server and a Flash client, we have created a [worketable] to facilitate sharing of information exchanged and its presentation.

Usage
------------------------------------

### CumulusService

CumulusService executable includes the following argument launch options, for developement and installation usage:

- **registerService**,
register the application as a service.

- **unregisterService**,
unregister the application as a service.

- **displayName**,
specify a display name for the service (only with /registerService).

- **startup=automatic|manual**,
specify the startup mode for the service (only with /registerService).

- **cirrus=address[,dump]**,
cirrus address to activate a "man-in-the-middle" developer mode in bypassing flash packets to the official cirrus server of your choice, it's a instable mode to help Cumulus developers.
You may add (after a comma) an option to include middle packets process for your dumping (only with *dump* argument), "p2p.rtmfp.net:10007,1" for example.

- **dump[=file]**,
enables packet traces in the console. Optionnal *file* argument also allows a file dumping. Used often with *cirrus=address[,dump]* option to observe flash/cirrus exchange.

- **log=level**,
log level argument beetween 0 and 8 : none, fatal, critic, error, warn, note, info, debug, trace. Default value is 6 (note), all logs until info level are displayed.

- **help**,
displays help information about command-line usage.

Command-line way is preferred during development and test usage. To statically configure the service the better practice is a optionnal configuration file to the installation directory.
The possible configurations are:

- **port**,
equals 1935 by default (RTMFP server default port), it's the port used by CumulusService to listen incoming RTMFP requests.

- **keepAliveServer**,
time in seconds for periodically sending packets keep-alive with server, 15s by default (valid value is from 5s to 255s).

- **keepAlivePeer**,
time in seconds for periodically sending packets keep-alive between peers, 10s by default (valid value is from 5s to 255s).

- **auth.whitelist**,
boolean value to interpret the *auth* file as a whitelist (true) or a blacklist (false, value by default).

*auth* file must be without extension and put in the executable program folder. It's the file rules for newcomer acceptance or rejection.
It contains host client followed by path page (separated by a comma).

    #host, 		  path
    www.site.fr 					# match all pages of "www.site.fr"
    www.site.fr, /home/index.html 	# match only the "/home/flower.html" page of "www.site.fr"
    www.site.fr, /home/flower.swf   # match the "http://www.site.fr/home/flower.swf" usage on any web pages and host
	
If it's configured as a "blacklist", a client which matchs a line will be rejected.
If it's configured as a "whitelist", a newcomer which math a line is accepted.

The configuration file must have *CumulusService* as base name and can be a *ini*, *xml*, or *properties* file type, as you like (personnal choice of preferred style).

**CumulusService.ini**

    ;Cumulus server configurations
    port = 1985 
	keepAlivePeer = 10
	keepAliveServer = 15
	[auth]
	whitelist = true

**CumulusService.xml**

    <config>
      <port>1985</port>
	  <keepAlivePeer>10</port>
	  <keepAliveServer>15</port>
	  <auth whitelist="true" />
    </config>

**CumulusService.properties**

    # Cumulus server port
    port=1985
	keepAlivePeer=10
	keepAliveServer=15
	auth.whitelist=true

If this configuration file doesn't exist, default values will be used.

### CumulusLib

CumulusService is almost a empty project, and all CumulusLib usage is included in main.cpp file.
Looks its file content is still the best way to learn to play with ;-)

A brief overview:

    #include "RTMFPServer.h"

    using namespace Cumulus;
	
	class ClientHandler: private ClientHandler {
	public:
		ClientHandler(){}
		bool onConnection(const Client& client) {
			...
		}
		void onFailed(const Client& client,const std::string& msg) {
			...
		}
		void onDisconnection(const Client& client) {
			...
		}
	};
	
	ClientHandler clientHandler;
    RTMFPServer server(clientHandler);
    server.start();
    ...
    server.stop();
	
### Flash side

If your Cumulus instance is started in local, Flash client can connect it by a classical NetConnection:

    _netConnection.connect("rtmfp://localhost/");

Here the port has its default value 1935. If you configure a different port on CumulusService you must indicate this port in the URL (after localhost, of course).

In "man-in-the-middle" mode (see command-line argument *cirrus* in usage part) you must indacted on side flash your Cirrus key developer.
	
	_netConnection.connect("rtmfp://localhost/KEY");
	
Of course "KEY" must be replaced by your Cirrus development key.

__notice:__ The *ipMulticastMemberUpdatesEnabled* NetGroup mode is not supporter for this moment.

Build
------------------------------------

Cumulus source code is crossplatform.

### Dependencies

Cumulus has the following dependencies:

- [OpenSSL] is required.

- [Poco] in its Basic edition is required.

### Building

**Windows**

Visual Studio 2008 file solutions and projects are included.
It finds the external librairies in "External/lib" folder and external includes in "External/include" folder at the root project.
So you must put Poco and OpenSSL includes/libs in these folders.
You can find OpenSSL binaries for windows on [Win32OpenSSL].
Poco builds with Visual Studio interpreter command line (see its readme file about *buildwin.cmd*),
but this is a example for Visual Studio 2010 which build quickly (just in static mode and without Poco samples) :

	buildwin 100 build static_mt both Win32 nosamples devenv

**Linux/Unix**
	
CumulusLib building manipulation are:

	make
	make clean

CumulusService works in a same way:

	make
	make clean

Thanks
------------------------------------
Special thanks to Key2 and Andrei of [C++ RMTP Server] who by their preliminary work has made this project possible.


[C++ RMTP Server]: [http://www.rtmpd.com] "www.rtmpd.com"
[GNU General Public License]: http://www.gnu.org/licenses/ "www.gnu.org/licenses"
[license]: https://github.com/OpenRTMFP/Cumulus/raw/master/LICENSE "LICENSE"
[OpenSSL]: http://www.openssl.org/ "www.openssl.org"
[Poco]: http://pocoproject.org/ "pocoproject.org" 
[worketable]: http://openrtmfp.github.com/Cumulus/ "Cumulus Worketable"
[Win32OpenSSL]: [http://www.slproweb.com/products/Win32OpenSSL.html] "www.slproweb.com"
[us]: https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=M24B32EH2GV3A "Donation US"
[eu]: https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=QPWT9V67YWSGG "Donation EU"
