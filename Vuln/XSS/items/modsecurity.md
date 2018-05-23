# WAF ModSecurity 
**ModSecurity** is an open source, cross-platform **web application firewall (WAF) module**. Known as the "Swiss Army Knife" of WAFs, it 
enables web application defenders to gain visibility into HTTP(S) traffic and provides a power rules language and API to implement 
advanced protections.
ModSecurity is a toolkit for real-time web application **monitoring, logging, and access control**. I like to think about it as an 
enabler: there are no hard rules telling you what to do; instead, it is up to you to choose your own path through the available 
features. That's why the title of this section asks what ModSecurity can do, not what it does.

The freedom to choose what to do is an essential part of ModSecurity's identity and goes very well with its open source nature. 
With full access to the source code, your freedom to choose extends to the ability to customize and extend the tool itself to make 
it fit your needs. It's not a matter of ideology, but of practicality. I simply don't want my tools to restrict what I can do.

Back on the topic of what ModSecurity can do, the following is a list of the most important usage scenarios:
* Real-time application security monitoring and access control
* Full HTTP traffic logging
* Continuous passive security assessment
* Web application hardening
* Something small, yet very important to you

Guiding Principles: 
* Flexibility
* Passiveness
* Predictability
* Quality over quantity

ModSecurity supports two deployment options: 
* **Embedded**
Because **ModSecurity** is an Apache module, you can add it to any compatible version of Apache. At the moment that means a reasonably recent `Apache version from the 2.0.x branch, although a newer 2.2.x version is recommended`. The embedded option is a great choice for those who already have their architecture laid out and don't want to change it. Embedded deployment is also the only option if you need to protect hundreds of web servers. In such situations, it is impractical to build a separate proxybased security layer. Embedded ModSecurity not only does not introduce new points of failure, but it scales seamlessly as the underlying web infrastructure scales. The main challenge with embedded deployment is that server resources are shared between the web server and ModSecurity.

* **Reverse proxy**
Reverse proxies are effectively HTTP routers, designed to stand between web servers and their clients. When you install a dedicated Apache reverse proxy and add ModSecurity to it, you get a â€œproperâ€ network web application firewall, which you can use to protect any number of web servers on the same network. Many security practitioners prefer having a separate security layer. With it you get complete isolation from the systems you are protecting. On the performance front, a `standalone ModSecurity` will have resources dedicated to it, which means that you will be able to do more (i.e., have more complex rules). The `main disadvantage of this approach` is the new point of failure, which will need to be addressed with a high-availability setup of two or more reverse proxies. 

Note: It works with **Apache, NNGINX and IIS**.

## Reference
[Home page](https://www.modsecurity.org)
