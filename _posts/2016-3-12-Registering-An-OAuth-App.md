---
layout: post
title: Registering an OAuth App with Google and Twitter
category: Web
tags: [OAuth]
---

Giving your users the ability to login on a website via a trusted OAuth provider is a huge benefit.
As far as user experience goes, often one of the last things people want to do when trying to gain access to a website
is to register yet another email & password combo, being forced to remember another pair of website-specific login credentials.

Adding OAuth login support to your user driven website can comfort users in a myriad of ways. Below are a few that come to mind:

 - For one, `security` is implied as they will be doing the logging in through their trusted account provider with whom they've already registered with.

 - Giving users `control` over what personal information the login provider gives the target site lets the user feel safe about registering with the site.

 - And of course the obvious gain of `flexibility` is a huge win, giving the user the ability to seamlessly come and go as they please between
 sites, all while providing one place for them to see exactly which sites have access to what information.  [Google](https://accounts.google.com/issuedauthsubtokens), [Twitter](https://twitter.com/settings/applications).
 
Creating OAuth driven sites is often a lot easier than people think, however I'll be covering that in a later tutorial. Right now we're going to learn
how to do something even simpler, register OAuth apps with Google and Twitter. This is important because it defines what exactly the access limitations of
our website or service will be when we ask the user to grant us access to their information. Registering an application with a provider gives us a set of unique
credentials that the OAuth provider uses to identify our site as the application we will be creating in this tutorial. Let's get started.

## Registering an application with Google
In order to register our OAuth application with Google, we need to head over to the [Google Developers Console](https://console.developers.google.com/start). Upon arriving
you should see a screen like this:

![Google Developers Console]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/google-developers-console.png)

We then want to click the "Select a project" drop down and from there select "Create a project...".

![Select a project]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/select-a-project.png)

Fil out the form with a project name and a unique project ID, and click create.

![Project Form]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/project-form.png)

After the project has been created, you should then be routed to the dashboard for your project. From here we can click on the "Use Google APIs" box to finish
configuring our application.

![Use Google APIs]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/use-google-apis.png)

The API Manager consists of two main sections. In the "Overview" we can added Google product APIs to our project for more extensive access, and in the "Credentials" section
we can configure different parts of our application's identity. Click the Credentials entry on the left sidebar. Before we can create the necessary credentials for our application, we need to
customize our OAuth consent screen. Click the "OAuth consent screen" tab next to the default "Credentials" tab. Now we can fill out the information that Google will display to the
user when our application asks for access. After filling out the consent screen information, your form should look something like this:

![Consent screen form]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/consent-screen-form.png)

After clicking Save, you will be prompted to create credentials, Now that we have customized our OAuth consent screen, we can create an OAuth client ID
with Google by clicking the "OAuth client ID" entry under the "Create credentials" dropdown.

![Create-creds]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/create-creds.png)

We will then be prompted to select the type of our Application. We will be selecting "Web application". For the name of our credentials, we can put whatever we want.
This does not have to be the same name as our application or project. For example, we can make different credentials to specify different origins and callbacks. However
for more simple applications it will suffice to have one.

![application-type]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/app-type.png)

The next section displayed is the Restriction section. The "Authorized JavaScript origins" lets Google know that a request for user data access on behalf of your site or application
will come from a predetermined origin so nobody can spoof an access request from a bogus location on behalf of your application. The "Authorized redirect URIs" ensure that when
a user accepts or denies our application access, they will be redirected to the right URI with a temporary code that our application can use to exchange for an access token.

If someone were to try and change request on its way to Google, they could define their own redirect URI that could lead the user back to a possibly malicious site, with necessary information
on accessing their information (A code to exchange for an access token). Google checks the redirect URI in a given request against those that we input here to ensure the user is
being redirected to one of possibly several correct callback destinations. For a demo site your form might look like the following:

![restrictions]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/restrictions.png)
 
Once we hit "Create", we will be prompted with our credentials. It is important to save these, keep them safe, and never commit them to any public repository. Doing so could
let anyone build a web application that could pretend to be your own, asking users for their information on your behalf.

![client-credentials]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/client-creds.png)

That's it! Make sure you put the credentials you received into some configuration file that your application can use, and always make sure that configuration file is in your `.gitignore`
file to keep it from public eyes.

## Registering an application with Twitter

Registering an OAuth application with Twitter follows a very similar process. First navigate to the [Twitter Application Management](https://apps.twitter.com/) page. From here select the
"Create New App" button.

![createnewapp]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/createnewapp.png)

You'll see that the process for creating a Twitter OAuth application is very cut and dry. You'll need the following:

 - Application Name
 - Description
 - Website
 - Callback URL 
 
![twittercreateanapp]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/createanapptwitter.png)

Notice that I did not associate a callback originating from `localhost` in this form. This is due to the fact that Twitter will not allow you to supply a `localhost` address
for your application callback. However fret not! The following quote from [this page](https://twittercommunity.com/t/why-cant-i-use-localhost-as-my-oauth-callback/708/2) shows
that it is still possible to have your Twitter app redirect to a `localhost` address, or any address other than the specified callback URL for that matter:


> In OAuth 1.0a, you are required to send an oauth_callback parameter to the oauth/request_token
> every time, regardless of what you put in this field. At the oauth/request_token step, you can
> provide a http://localhost/blah/blah/blah151 callback if you like. You just can't associate
> localhost with your application record on dev.twitter.com.

Once your application is created you can roam through the setings and permissions section of your app to modify its information, security, and access.

![twitterpermissions]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/permissions.png)

Your consumer key and consumer secret can be found under the "Keys and Access Tokens" tab. These values are what your app will use to identify itself with Twitter on
API requests. As with the credentials we got from Google upon app registration, make sure these are kept secret and never committed to any public repository.

![twittertokens]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/twittertokens.png)

Note that in a production application it is wise to enable callback locking. This makes it impossible for a request to be tampered with in such a way that will allow
Twitter to redirect to URL other than the one specified in the application settings. The callback locking feature can be found under the settings tab.

![callbacklocking]({{ site.baseurl }}/images/2016-3-12-Registering-An-OAuth-App/callbacklocking.png)