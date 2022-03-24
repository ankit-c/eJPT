# Information Gathering

This page contains all the useful command and tools used for information gathering.

## Open-Source Intelligence

  * Social Networks
  * Websites
  * Company Websites
  * CrunchBase

## Whois

  whois can provide useful information such as
  
  * Owner name
  * Street address
  * Email address
  * Technical details

  ```
  whois example.com
  ```
## Subdomain Enumeration

  Subdomain enumeration is the technique to discover all the existing subdomains for the given host (same top-level domain). This is done by the pentester to widen   the attack surface area, which increases the possibility of finding the potential vulnerabilites.

  ```
  Host: example.com

  subdomain.host.com

  Possible examples of subdomains would be:

  app.example.com
  mail.example.com
  ```
  **Subdomain enumeration using Google Dorks**
   ```
   site: example.com
   
   ```
   * [Google Dorks Cheat Sheet](https://sansorg.egnyte.com/dl/f4TCYNMgN6)
## Tools

 * [dnsdumpster.com](https://dnsdumpster.com/)
 * [crt.sh](https://crt.sh)
 * [sublist3r](https://github.com/aboul3la/Sublist3r)
  ```
  sublist3r -d example.com
 ```
   where flag **-d** : searches for subdomain
 * [virustotal.com](https://www.virustotal.com/gui/)
