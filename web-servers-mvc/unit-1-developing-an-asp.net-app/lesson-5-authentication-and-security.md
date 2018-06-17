**Learning Objectives**
=======================
- Understand the primary security concerns in an HTTP application and use ASP.NET Identity to address them
- Accepting file uploads in your app
- Overview of advanced MVC features: Custom routing, OWIN

**Outline**
===========
- What is Auth?
  - In general auth means checking if a user can do something; in security we break it down into two steps
  - 1. **Authentication**: Verifying that a user is who he claims to be; this has nothing to do with what he can do, only who he is.
  - 2. **Authorization**: Verifying an authenticated user is permitted to do something.
  - _Careful_ - that means IsAuthenticated just means we have established that the user is who they claim to be, not that they're allowed to do something specific
- "Automated" Auth in ASP.NET
  - Remember, HTTP client/server projects are stateless; we cannot auth a user and keep them auth'ed for all future requests
  - Instead, we must auth _every single request_!
  - As your auth rules get more complex, that can become a tremendous burden
  - Microsoft created the ASP.NET Identity system to automate both stages of authorization; you only need to add in the auth logic; Identity takes care of the rest
- Side Note - Membership
  - ASP.NET Identity was released with MVC 4 in 2013
  - All versions of ASP.NET MVC (and other ASP.NET technologies) before this used a system called "Membership"
  - Membership had the same goal as Identity, but it worked very differently; it's also missing most modern features
  - You can find more on Membership here - https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-use-the-aspnet-membership-provider
- Adding ASP.NET Identity to a project
  - For new projects, you can easily add identity at the "add project" wizard; this deals with all the classes, packages, etc automatically
  - Great guide for adding to an exiting project: https://stackoverflow.com/a/31963828/794234
- Parts of Identity you need to setup
  - **Database** (use IdentityDbContext)- where you store usernames, profiles, etc; this does not have to be the same DB as your main product db

  - **SignInManager**, **UserManager** and **RoleManager** - provide "hooks" for you to write custom code for the auth process

  - **IdentityUser** - user model w/ all your custom profile fields

  - **OWIN App_Start.Startup** - connects identity to your MVC project; this is complex. In most cases you can "set it and forget it".
- In Class Demo - Basic Auth (Cookies)
- Authentication Req's are generally pretty simple
  - Something you have (often used w/ 2FA - two factor authentication)
    - Certificate (aka X.509 certificate)
    - Security dongle: RSA SecurID, Yubikey, proprietary
    - Cell phone or e-mail to receive code
    - IP address
    - NB: this is normally insecure as a whitelist b/c it can be faked; ok for a blacklist
  - Something you know
    - Username / Password
    - Security Questions (more on this later)
- Authorization Req's get far more complex
  - Role in company
  - Access type (e.g. VPN, web, LAN, server console)
  - Approval by supervisor (DB based or real-time)
  - Number of times resource has been accessed (by you or anyone else)
  - Date / time passed since resource was created
  - What mode server was started in (eg Release, Debug, Recovery, etc)
- [Authorize] and [AllowAnonymous]
  - You can decorate your actions and/or controllers in MVC with [Authorize] to prevent access to the action without authorization
  - Supports authorization by role name(s) or username(s)
  - Support more complex auth rules using custom auth attributes
  - Use [AllowAnonymous] to override an [Authorize] set at a higher level
- In Class Demo - [AuthorizeConnectionIsLocal] to check past scheduled release date
- Cookie vs Token
  - Remember from our lesson in requests that cookies have a bad reputation
  - Also - many mobile devices do not support cookies completely
  - In general cookies are not considered to be a good solution for auth anymore
  - "Token"'s are basically just specialized cookies
    - Cookies can store any number of key/value pairs
    - Tokens can only store a single encrypted session token
    - BOTH are headers; BOTH get sent w/ every request;
  - Token's are not normally automatically sent the way cookies are
- Federated Identity and OAuth
  - In today's online ecosystem most people authenticate with many many different web applications
  eg: Amazon, Gmail, Facebook, Github, YouTube, etc
  - Remember - the point of authentication is to establish that someone is who they claim to be
  - We can rely on a 3rd party to authenticate someone for us and eliminate the need to include complex authentication code in our app
  - This concept is called "federated identity" - ie, we federate our id from many different providers into a single online identity usable on any site
  - OAuth is a standard for making auth via federated identiy simple
  - Easy to use in MVC if you use them w/ tokens (don't support cookies)
  - Fully integrated w/ [Authorize] and [AllowAnonymous] with no extra code
  - Can be used in parallel w/ cookie auth
- In Class Demo - Adding OAuth server to MVC
- Security Sidebar - Bad Practices
  - Unfortunately there are many _very bad_ but _very common_ security practices
  - Bad Password Policies - Computers are good at breaking passwords, people are bad at remembering them; [https://xkcd.com/936/](https://xkcd.com/936/)
  - Security Questions - Effectively a surrogate for your password
  - 2FA - Often implemented that you choose the phone # / e-mail for verification on the spot
  - "Home Images" - If you don't let end user choose the image it's meaningless
  - Training users to ignore warnings - This is a huge one, especially with chareidi filters; Many companies will tell you to click past the big "you might be being hacked" warnings that happen when they don't implement security properly; that trains users to always ignore them....
- Encrypted Requests
  - MITM is a trivial attack on an unencrypted request (eg Fiddler)
  - Encyrpting means the data is unreadable while in transit btwn client and server (based on public key / RSA encryption)
  - Best standards online are to always use encryption
  - HTTPS, SSL, TLS
  - Most browsers "penalize" you for not using SSL; that includes warning messages if you use password fields, red warnings in the address bar, and sometimes "auto blocks";
  - SSL is easy with LetsEncrypt
- Other Advanced Concepts
  - Active Directory and/or Azure Auth integration
  - File uploads; code is pretty easy (use an HttpPostedFileBase as an action method arg); security is a big deal; you're allowing an end user to put arbitrary files on your server;
  - Session: you can store info besides the currently logged on user in a "session" on the server; most of the time this isn't needed.