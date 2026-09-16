- Try to explore the application in all the possible ways including Functionalities, Privileges, every button and every link which gives you a rough idea about attack surface.
### Google Dorking
- site : Tells Google to show you results from a certain site only.
- inurl : Searches for pages with a URL that match the search string.
- intitle : Finds specific strings in a page’s title.
- link : Searches for web pages that contain links to a specified URL.
- filetype : Searches for pages with a specific file extension.
- Wildcard(\*) : You can use the wildcard operator (\*) within searches to mean any character or series of characters.
- Quotes("") : Adding quotation marks around your search terms forces an exact match.
- Or : The or operator is denoted with the pipe character (|) and can be used to search for one search term or the other, or both at the same time.
- Minus (-) : The minus operator (-) excludes certain search results.
- FInding subdomains : `site:*.example.com`
- Kibana is a data visualization tool that displays server operation data such as server logs, debug messages, and server status : `site:example.com inurl:app/kibana`
- Look for special extensions that could indicate a sensitive file : `site:example.com ext:php`
  `site:example.com ext:log`
- In addition to constructing your own queries, check out the Google Hacking Database (https://www.exploit-db.com/google-hacking-database/).
### WHOIS & Reverse WHOIS
- this gives information about the details like: owner of the domain, name, email, phone, address.
- `cmd : whois domain_name`
- Reverse WHOIS works where you know an entity like phone number or email so that you get to know about domain name : (https://viewdns.info/reversewhois/)
### Nslookup
- Tool that uses to find ip address of a domain
- `cmd : nslookup domain_name`
