# numishare-faq

## Orbeon error after uploading xxx.xml to objects folder
The error occured when trying to edit the onbject:

    Orbeon Forms - An Error has Occurred

This was cause by the following line in the uploaded file:

    <department/>
    
After removing it, all works well. I assume the line was added by a "try and cancel" for the department entry.

## How to open port 80 for numishare on CentOs

Check your active zones

    firewall-cmd --get-active-zones

Add service http to desired zone (here public)

    firewall-cmd --zone=public --add-service=http --permanent

Reload configuration

    firewall-cmd --reload

## How to enable internal http connection on CentOs

If you are using an Apache Frontend with Numishare the the frontend might not be able to communicate with the numishare backend. The error in Apaches error_log might look like:

    [Fri Oct 12 08:39:40.875454 2018] [proxy_http:error] [pid 304] [client 192.168.178.52:1037] AH01114: HTTP: failed to make connection to backend: localhost
    [Fri Oct 12 08:42:00.068989 2018] [proxy:error] [pid 300] (13)Permission denied: AH00957: HTTP: attempt to connect to 127.0.0.1:10200 (localhost) failed
    [Fri Oct 12 08:42:00.069139 2018] [proxy:error] [pid 300] AH00959: ap_proxy_connect_backend disabling worker for (localhost) for 60s

If SELinux is enabled, then the /var/log/audit/audit.log might look like:

    type=AVC msg=audit(1234567890.123:12345): avc:  denied  { name_connect } for  pid=999 comm="httpd" dest=10200 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:trisoap_port_t:s0 tclass=tcp_socket

Try to set the following boolean:

    /usr/sbin/setsebool httpd_can_network_connect 1
    
If it solves your problem, make the change permanent using:

    /usr/sbin/setsebool -P httpd_can_network_connect 1

## Numishare-docker on Windows
* Install Docker for Windows (https://docs.docker.com/docker-for-windows/install/)
* Set WSL 2 as default: wsl.exe --set-default-version 2 (https://docs.docker.com/docker-for-windows/wsl/)
* Install Linux for Windows (ex. Windows Store > Ubunto) (https://docs.microsoft.com/de-de/windows/wsl/install-win10)
* If "docker" cannot be started, then enable integration with additional distros: (Ubunto), in Docker Desktop > Settings > Resources
* Start Ubunto
* Follow the installation steps in README.md of Numishare-docker.

* To access a container (example):

  docker exec -ti orbeon bash
  
  


