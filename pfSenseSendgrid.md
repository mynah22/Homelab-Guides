# pfSense email notifications viaSendgrid

- Article type: reference
  
    This guide describes the process of setting up email notifications in pfSense via a free Sendgrid account

## contents

here

## Sendgrid setup

This guide requires a free Sendgrid account. There are no gotchas, and it allows for 100 emails to be sent per day

1. Create account
    - Browse to [sendgrid.com](https://sendgrid.com)
    - Click [Start for free](https://signup.sendgrid.com/) (or current sign up link)
    - Enter your email and password, perform captcha actions, and accept the TOS. Then click 'create account'
2. Domain validation
3. Add authenticated address
4. Create API key
    - retain key!

## pfSense setup

1. system > advanced > notifications
2. enable daily expired cert notifications
3. make sure 'Disable SMTP notifications' is unchecked
4. email server: smtp.sendgrid.net
   - *note: sendgrid.**NET**, not .com*
5. SMTP port: 465 (smtps)
6. Secure SMTP connection: check "enable SMTP over SSL/TLS"
7. From email address: the email address you authenticated in sendgrid
8. Notification email address: where you want the emails delivered
9. Notification E-Mail auth username:
    - NOT your username!!!
    - `apikey`
10. Notification Email auth password: your **API key**
11. Notification Email auth mechanism: LOGIN
12. scroll all the way to the bottom and SAVE
13. click 'test email'
