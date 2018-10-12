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
