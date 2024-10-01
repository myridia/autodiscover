# autodicover.xml and autoconfig.xml 

The feature in many MUAs(Mail User Agents) is the email servers autodiscovering. When you type your e-mail address in their setup window, some MUAs are trying to determine what IMAP/POP3 servers to use.To do this, they assume, that your email contains a server domain name. They use this domain name to access its website to get an xml config setup file like autoconfig.xml
,config-v1.1.xml, autodiscover.xml ....

There are at the moment 2 main xml structured files:

Thunderbird's
autoconfig.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<clientConfig version="1.1">
    <emailProvider id="mydomain.com">
        <domain>mydomain.com</domain>
        <displayName>Mail</displayName>
        <displayShortName>Mail</displayShortName>
        <incomingServer type="pop3">
            <hostname>mail.mydomain.com</hostname>
            <port>995</port>
            <socketType>SSL</socketType>
            <authentication>password-cleartext</authentication>
            <username>%EMAILADDRESS%</username>
            <pop3>
                <leaveMessagesOnServer>true</leaveMessagesOnServer>
                <downloadOnBiff>true</downloadOnBiff>
                <daysToLeaveMessagesOnServer>10</daysToLeaveMessagesOnServer>
            </pop3>
        </incomingServer>
        <incomingServer type="imap">
            <hostname>mail.mydomain.com</hostname>
            <port>993</port>
            <socketType>SSL</socketType>
            <authentication>password-cleartext</authentication>
            <username>%EMAILADDRESS%</username>
        </incomingServer>
        <outgoingServer type="smtp">
            <hostname>mail.mydomain.com</hostname>
            <port>587</port>
            <socketType>STARTTLS</socketType>
            <authentication>password-cleartext</authentication>
            <username>%EMAILADDRESS%</username>
        </outgoingServer>
    </emailProvider>
</clientConfig>
```
and Microsoft Outlook and Co 
autodiscover.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<Autodiscover xmlns="http://schemas.microsoft.com/exchange/autodiscover/responseschema/2006">
    <Response xmlns="http://schemas.microsoft.com/exchange/autodiscover/outlook/responseschema/2006a">
        <Account>
            <AccountType>email</AccountType>
            <Action>settings</Action>
            <Protocol>
                <Type>POP3</Type>
                <Server>mail.mydomain.com</Server>
                <Port>995</Port>
                <DomainRequired>on</DomainRequired>
                <SPA>off</SPA>
                <SSL>on</SSL>
                <AuthRequired>on</AuthRequired>
            </Protocol>
            <Protocol>
                <Type>IMAP</Type>
                <Server>mail.mydomain.com</Server>
                <Port>993</Port>
                <DomainRequired>on</DomainRequired>
                <SPA>off</SPA>
                <SSL>on</SSL>
                <AuthRequired>on</AuthRequired>
            </Protocol>
            <Protocol>
                <Type>SMTP</Type>
                <Server>mail.mydomain.com</Server>
                <Port>587</Port>
                <DomainRequired>on</DomainRequired>
                <SPA>off</SPA>
                <SSL>on</SSL>
                <AuthRequired>on</AuthRequired>
                <UsePOPAuth>off</UsePOPAuth>
                <SMTPLast>off</SMTPLast>
            </Protocol>
        </Account>
    </Response>
</Autodiscover>
```

Outlook on Windows 11 lookups:

```
"GET /.well-known/autoconfig/mail/config-v1.1.xml HTTP/1.1" 404 439 "-" "-"
"POST /autodiscover/autodiscover.xml HTTP/1.1" 404 4280 "-" "OutlookMobileCloudService-Autodetect/1.0.0"
"POST /autodiscover/autodiscover.xml HTTP/1.1" 404 4280 "-" "OutlookMobileCloudService-Autodetect/1.0.0

```

K-9 Android Mail lookups:
```
/.well-known/autoconfig/mail/config-v1.1.xml HTTP/1.1" 404 495 "-" "okhttp/4.12.0"
/.well-known/autoconfig/mail/config-v1.1.xml HTTP/1.1" 404 4367 "-" "okhttp/4.12.0"
```


Example for nginx proxy 
/etc/nginx.conf
```
location /autodiscover/autodiscover.xml {
    proxy_pass https://ams.ddit-server.com/autodiscover.xml;
}
location /AutoDiscover/AutoDiscover.xml {
    proxy_pass https://ams.ddit-server.com/autodiscover.xml;
}
location /.well-known/autoconfig/mail/config-v1.1.xml {
 proxy_pass https://ams.ddit-server.com/autoconfig.xml;
}
```

