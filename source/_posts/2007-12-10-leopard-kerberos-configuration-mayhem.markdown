---
layout: post
title: Leopard Kerberos configuration mayhem
categories: - Mac OS X
            - Directory Systems
---
Editing the file <code>/Library/Preferences/edu.mit.Kerberos</code> might not always get you the expected result in Leopard. After editing my kerberos realm with kerberos.app, I didn't see the expected changes. After sniffing on the network I realized the client was still trying to connect to the old kdc server. It took me quite some time to figure out why the change in the configuration file did not cause the client to use the new kdc. 
<!--more-->
It appears there is a record in the local DirectoryService that overrules the old configuration file. If you try to find it using dscl or Workgroup Manager you can find it at <code>/Local/Default/config/Kerberos:EXAMPLE.COM</code>. This record contains an XMLPlist object with the kerberos configuration. This is what the XMLPlist attribute should look like:

``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
      <dict>
        <key>KADM_List</key>
        <array>
          <string>kadm.example.com</string>
        </array>
        <key>KDC_List</key>
        <array>
          <string>kdc1.example.com</string>
          <string>kcc2.example.com</string>
        </array>
      </dict>
    </plist>
```

Mac OS X Server has used this format before to distribute kerberos configuration to clients. On Tiger this record in the Open Directory server would be used to create the file <code>/Library/Preferences/edu.mit.Kerberos</code>. On Leopard this record is duplicated to the local directory and this record overrules the configuration in the edu.mit.Kerberos file. If you are changing your kerberos configuration by changing the edu.mit.Kerberos file and your changes do not seem to work, check your local directory for kerberos records. The easiest way to do this is by using Workgroup Manager.
You can also edit the directory service record directly. It is stored in a text file at <code>/var/db/dslocal/nodes/Default/config/Kerberos\:HCODE.BE.plist</code>. The record itself is also a plist file so all the xml entities should be encoded. This is what the file should look like:

``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>XMLPlist</key>
      <array>
        <string>
          &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
          &lt;!DOCTYPE plist PUBLIC &quot;-//Apple//DTD PLIST 1.0//EN&quot; &quot;http://www.apple.com/DTDs/PropertyList-1.0.dtd&quot;&gt;
          &lt;plist version=&quot;1.0&quot;&gt;
            &lt;dict&gt;
              &lt;key&gt;KADM_List&lt;/key&gt;
              &lt;array&gt;
                &lt;string&gt;kadm.example.com&lt;/string&gt;
              &lt;/array&gt;
              &lt;key&gt;KDC_List&lt;/key&gt;
              &lt;array&gt;
                &lt;string&gt;kdc1.example.com&lt;/string&gt;
                &lt;string&gt;kcc2.example.com&lt;/string&gt;
              &lt;/array&gt;
            &lt;/dict&gt;
          &lt;/plist&gt;       
        </string>
      </array>
      <key>name</key>
      <array>
        <string>Kerberos:EXAMPLE.COM</string>
      </array>
    </dict>
    </plist>
```
