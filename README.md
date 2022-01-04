# numishare-faq

## "Error saving Numishare configuration file."
Problem occures when not all required fields are filled. Example: 
If the Collection Type is "cointype" and no "Type Series" is entered.

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
  
## Multiple collections with different designs
### Let's assume the tomcat is installed in /usr/local/tomcat/:
    cp -r /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/numishare /usr/local/projects/mymint_01
    cp -r /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/numishare /usr/local/projects/mycollection_01
### Enter the resource directory:
    cd /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/
    mkdir ./numishare-projects
    cd ./numishare-projects
    ln -s /usr/local/projects/mymint_01 mymint_01
    ln -s /usr/local/projects/mycollection_01 mycollection_01
### Edit /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/numishare/page-flow.xml and add for for example:
    # <page path="/numishare/mymint_01/" model="xpl/models/config.xpl" view="oxf:/numishare-projects/mymint_01/xpl/views/pages/index.xpl"/>
    # <page path="/numishare/mycollection_01/" model="xpl/models/config.xpl" view="oxf:/numishare-projects/mycollection_01/xpl/views/pages/index.xpl"/>
    vi /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/numishare/page-flow.xml
    # Info: consider to map an external file into the docker container.
### Now you can modify the start page by changing the files:
    vi /usr/local/projects/mymint_01/ui/xslt/pages/index.xsl
    vi /usr/local/projects/mycollection_01/ui/xslt/pages/index.xsl
    # Info: consider to map an external file into the docker container.
    Example:
    <body>
        <xsl:call-template name="header"/>
        <img src="{$include_path}/images/banner.jpg" style="width:100%"/>
        ...
    </body>
    will force to load the image via:
        http://localhost:8081/orbeon/themes/mymint_01/images/banner.jpg

### (Optional) Assign separate themes
    mkdir /var/lib/tomcat7/webapps/orbeon/WEB-INF/resources/apps/themes
    cd /var/lib/tomcat7/webapps/orbeon/WEB-INF/resources/apps/themes
    ln -s /usr/local/projects/mymint_01/ui mymint_01
    ln -s /usr/local/projects/mymint_01/ui mycollection_01
    # Login to the admin interface and change for each collection: 
    # modify settings > Theme and Layout > Theme Folder > "mymint_01" or "mycollection_01"
    # modify settings > Theme and Layout > Theme URI > <http://localhost:8081>/orbeon/themes/
    # Modify style.css in /usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/themes/mymint_01/css/ or the linked location.
    # Info: consider to map an external file (like /usr/local/projects/mymint_01/ui/css/style.css) into the docker container.
    
 ### Info for images
    If you use a separate theme, then the images are in "/usr/local/tomcat/webapps/orbeon/WEB-INF/resources/apps/themes/mymint_01/images" or (due to the symbolic link) in the folder "/usr/local/projects/alpen/ui/images"

### fuseki: java.io.IOException: Cannot run program "ps": error=2, No such file or directory

(from https://github.com/stain/jena-docker/issues/34)

    docker run --name fuseki-data -v /fuseki busybox
    docker run -d --name fuseki -p 3030:3030 --volumes-from fuseki-data stain/jena-fuseki
    NO DATA UPLOADING BEFORE RUNNING COMMANDS BELOW
    Run the commands:

    docker exec -it fuseki bash

    root@7fc3133f5863:/jena-fuseki# apt-get update
    root@7fc3133f5863:/jena-fuseki# apt-get install -y procps
    root@7fc3133f5863:/jena-fuseki# exit

    Run docker restart fuseki to see if it throws errors.
    Upload data (I uploaded the pizza ontology)
    Run docker restart fuseki again to see if it throws errors.

## Customizing menue

See https://github.com/ewg118/numishare/issues/108

If you want to add a page which is not visible in the main menu, change the public number (found out by experiment)
  ...
    </content>
    </page>
    <page public="2" stub="rechtliches" id="rechtliches">
    <content lang="en">
        <title>Rechtliches</title>
        <short-title>Rechtliches</short-title>
        <text>
            <h2>Rechtliche Informationen</h2>
  ...
            
  It can be accessed by: http://localhost/orbeon/numishare/myinstance/pages/rechtliches?lang=de
            
