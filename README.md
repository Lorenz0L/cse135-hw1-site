# CSE 135 HW1

## Team
Lorenzo Lemus 

## Site
Live site: https://cse135lorenzo.site

## Server / Grader Access
- Grader username: `grader`
- Grader password: password123

## Password-Protected Team Site
- Username: `teamuser`
- Password: password321

## GitHub Auto-Deploy Setup
My public_html folder is a git clone of my repo on GitHub. To deploy changes I just push to main on my local machine, then on the server it pulls the latest changes so the live site updates. See github-deploy.gif for a demo of me changing a file, pushing it, and the site updating.

## Site Structure
- Homepage: https://cse135lorenzo.site
- Personal page: https://cse135lorenzo.site/members/lorenzo.html
- Favicon and robots.txt are both at the site root
- PHP test page: https://cse135lorenzo.site/hw1/hello.php
- GoAccess report: https://cse135lorenzo.site/hw1/report.html

## MySQL / LAMP Setup
Installed MySQL and PHP via apt (`php`, `libapache2-mod-php`, `php-mysql`, `mysql-server`). Secured MySQL with `mysql_secure_installation`: removed anonymous users, disabled remote root login, removed the test database. Root authentication uses the default Ubuntu `auth_socket` method (local sudo access only, no separate MySQL password was set).

## Compression
I turned on mod_deflate with `a2enmod deflate` and restarted Apache. Checked it in Chrome DevTools under the Network tab, and the response headers for the page now show Content-Encoding: gzip, so the HTML is actually being compressed before it gets sent. Screenshot in compression-verify.jpg.

## Removing the Server Header
This one took some digging. I installed mod_security2, turned on the security2 and headers modules, then made a new config file at /etc/apache2/conf-available/security-headers.conf with:
```
SecRuleEngine DetectionOnly
SecServerSignature "CSE135 Server"
ServerTokens Full
```
I initially had `SecRuleEngine On`, but that started blocking legitimate requests (like loading hello.php, which returned a 403), so I switched it to `DetectionOnly` — this keeps ModSecurity active and logging, but stops it from blocking real traffic. Enabled the config with a2enconf and restarted Apache. Ran `curl -I` on my site afterward and the Server header now says "CSE135 Server" instead of showing the real Apache version and OS. Screenshot in header-verify.jpg.

## Custom 404 Page
Made a 404.html file and added `ErrorDocument 404 /404.html` to my vhost config. Tested it by going to a fake URL that doesn't exist and my custom page shows up instead of the default Apache error. Screenshot in error-page.jpeg.

## Access Logs
Looked through my access log and filtered for 404s to see what was hitting my site. Most of it is bots scanning for random files that don't exist, like /bot-connect.js and /licensor.js, plus some bots checking for WordPress and ad-fraud stuff like /ads.txt and /sellers.json. None of it is real traffic, just normal scanning that any public server gets. Screenshot in log-verification.jpg.

## GoAccess Report
Installed GoAccess and ran it against my access log to generate a report, then copied that report into my hw1 folder so it's viewable live at the link above. Screenshot in report-verification.jpg.

## Screenshot / File Reference
| Filename | Description |
|---|---|
| initial-index.jpg | Default Apache2 page proving Apache is running |
| modified-index.jpg | First edit to index.html live |
| validator-initial.jpg | W3C validator result for index.html |
| vhosts-verify.jpg | All three domains (main, collector, reporting) working |
| ssl-verify.jpg | Site accessible over HTTPS |
| github-deploy.gif | GitHub push → auto-deploy to live site |
| php-verification.jpg | hello.php rendering with phpinfo() |
| compression-verify.jpg | DevTools showing Content-Encoding: gzip |
| header-verify.jpg | Server header changed to "CSE135 Server" |
| error-page.jpeg | Custom 404 page rendering |
| log-verification.jpg | Access log entries, including unexpected bot traffic |
| report-verification.jpg | GoAccess dashboard rendering live |
