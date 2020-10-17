---
title: "XML is not the right Configuration Format for the Cloud Era."
date: 2020-10-17T09:20:50+02:00
draft: false
---
Recently, Nicolas Fränkel published an [article highlighting the advantages of XML as a configuration language
for server-side (Java) software](https://blog.frankel.ch/defense-xml/). Being an XML-veteran like Nicolas I totally understand all of his arguments. However, there is one argument missing which is IMHO the strongest argument why XML is avoided (especially) in cloud software scenarios.

One of the big advantages of XML, the possiblity to express a schema for your XML format is at the same time the biggest weakness. The schema (the xsd file) is not part of the configuration file but specified as an external document that is usually loaded from the network (the Internet!). 

```
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
...
    </servlet>
    <servlet-mapping>
...
    </servlet-mapping>
</web-app>
```

Basically document could contain any kind of malformed, malicious code which is a potential danger for your infrastructure or to be more precise: the XML parsers contained. Before the cloud era you basically ran your servers inside your network for development teams that you basically trusted to produce no harmful code. Of course this assumption is a false friend but back in the days this was how we rolled. ;)

If you have a look at the above sample web.xml code you might spot the non-encrypted http:// URLs. This means that even if that were trusted sites people with access to infastructure could modify the content of the documents to be malicious.

Once services like Google Clould Platform, Heroku and AWS appeared virtually anybody could deploy applications with configuration files to your infrastructure. The risk of attacks via malformed XML/XSD files got much bigger. [OWASP is actually listing that attack vector](https://owasp.org/www-project-top-ten/2017/A4_2017-XML_External_Entities_(XXE)) as part of it's well-known TOP 10 catalogue.

IMHO the very obvious security risk in addition to the points Nicolas mentioned in his post lead to the downfall of XML as a configuration language. But don't get me wrong - **I hate YAML and it's useless quirks from the bottom of my heart**. Somehow it was chosen as an alternative language for structured configuration and things got out of control. Currently I don't know any good alternative. JSON and TOML are too verbose IMHO, Java property files are not well suited for structured configuration. Maybe the hate energy can be transfered into a working group for some cross-programming-language configuration DSL. We'll see :)
