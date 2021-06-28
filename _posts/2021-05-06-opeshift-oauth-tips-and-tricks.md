---
layout: post
title:  "Tips and Tricks for Oauth on Openshift 4"
tags: openshift ui
category: openshift
---

# Tips and Tricks for Openshift 4 UI

As I find out different issues with implementing oAuth on openshift I will document them here.


## Issues with forwarding redirect url

The general flow of oAuth 2 is to redirect the user to the server that does the authentication (i.e. Google, Facebook, Keycloak), and then have that server do the authentication and forward back to the original application server using a Redirect url that was sent as part of the uri.

For example if you try to login to [Apicurio](https://studio.apicur.io/) using Google and look at the url you will see something similar to what you see below (I removed a lot of extra request params that are not relevant to the issue)

https://accounts.google.com/o/oauth2/auth/oauthchooseaccount?redirect_uri=https%3A%2F%2Fstudio-auth.apicur.io%2Fauth%2Frealms%2Fapicurio%2Fbroker%2Fgoogle%2Fendpoint

So here we can see that we are no longer on the Apicurio webpage but on Google's server. And our url includes a redirect_uri so Google can redirect us back to apicuro with our token once we have authenticated.

The example above is working fine, but it is possible that the redirect url may not be correct, especially when behind an external load balancer. Specifically I have seen issues in which the redirect url removes ssl (meaning it tries to redirect to `http` rather than `https`). This can be solved by simply setting the `X-Forwarded-Proto` header. 

My most recent exposer to this was with dotNet, you just need to add the following line of code in order to set up the Forwarded header options`

```dtd
app.UseForwardedHeaders(new ForwardedHeadersOptions
        {
            ForwardedHeaders = ForwardedHeaders.XForwardedProto
        });
```

More info on this issue can be found here:
* [Microsoft Article](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer?view=aspnetcore-3.1)
* [Stackoverflow Solution](https://stackoverflow.com/questions/50468033/redirect-uri-sent-as-http-and-not-https-in-app-running-https/50505373#50505373)