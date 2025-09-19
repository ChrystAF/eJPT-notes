# Reconnaissance Tools

This folder contains notes about the tools and commands I used or learn about while studying for the eJPT exam.
eJPT Reconnaissance Tools

## **host {CMD}**
- **Purpose**: A command-line tool for performing DNS lookups.
- **How to use**: Run `host <domain-name>` in your terminal.
- **When to use**: Use this for quick lookups to find a domain's IPv4 and IPv6 addresses, as well as its mail servers.

## **WebsiteURL/robots.txt {URL}**
- **Purpose**: A file on a web server that tells search engine crawlers which parts of the site they should and should not index.
- **How to use**: Simply append `/robots.txt` to the end of a website's URL in your browser.
- **When to use**: This can reveal folders and files that the site owner wants to hide from search engines, which may contain sensitive information.

## **WebsiteURL/sitemap.xml {URL}**
- **Purpose**: An XML file that lists all the publicly available pages on a website.
- **How to use**: Append `/sitemap.xml` to a website's URL in your browser.
- **When to use**: Use this to get a comprehensive map of a website's structure and find pages you might have otherwise missed during manual browsing.

## **Wappalyzer {Browser Add-On}**
- **Purpose**: A browser extension that identifies the technology stack of a website.
- **How to use**: Install the add-on in your browser. When you visit a website, click the extension icon to see a list of technologies used.
- **When to use**: Use this for a quick and easy way to identify a website's underlying technologies, such as its content management system (CMS), web server, and programming languages.

## **whatweb {CMD}**
- **Purpose**: A command-line tool that identifies what a website is running.
- **How to use**: Run `whatweb <website-url>` in your terminal.
- **When to use**: This is a powerful alternative to browser extensions for fingerprinting a website and identifying its technologies, including versions and server-side details.

## **httrack {CMD}**
- **Purpose**: A command-line tool that downloads an entire website to your local machine.
- **How to use**: Use a command like `httrack <website-url>` to create a local copy of a site.
- **When to use**: Use this to analyze a website's source code offline for hidden directories, sensitive information in comments, or other clues that might not be visible on the live site.

## **whois {CMD}**
- **Purpose**: A command-line tool for querying domain registration information.
- **How to use**: Run `whois <domain-name>` in your terminal.
- **When to use**: Use it to find out who a domain is registered to, their contact information, and the domain's registration and expiration dates.

## **netcraft.com {Website}**
- **Purpose**: A website that provides a comprehensive report on a given domain.
- **How to use**: Visit the website and enter a domain name into the search bar.
- **When to use**: Use this to get an all-in-one report on a website, including its hosting provider, operating system, and security details.

## **dnsdumpster.com {Website}**
- **Purpose**: A website that provides detailed DNS records and a visual map of a domain's infrastructure.
- **How to use**: Go to the website and enter a domain name.
- **When to use**: Use it for more advanced DNS enumeration to discover host records, subdomains, and other related domains in a clear, graphical format.

## **sublist3r {CMD}**
- **Purpose**: A command-line tool for subdomain enumeration.
- **How to use**: Run `sublist3r -d <domain-name>` in your terminal.
- **When to use**: Use this to find all publicly available subdomains of a target domain, which can significantly expand your attack surface.

## **Google Dorks {Search Engine}**
- **Purpose**: Advanced search queries that leverage Google's search engine to find specific information.
- **How to use**: Use special operators like `site:`, `filetype:`, and `intitle:` in your Google search queries.
- **When to use**: Use this to uncover sensitive data or accidental leaks, such as exposed configuration files, open directories, or specific file types that are not linked from the main website.

## **Google Hacking DB {Website}**
- **Purpose**: A database of common Google Dorks.
- **How to use**: Visit the website and browse the categorized list of dorks.
- **When to use**: This is a great resource for finding pre-written queries to look for specific types of information and vulnerabilities.

## **theHarvester {CMD}**
- **Purpose**: A command-line tool for gathering public information about a target.
- **How to use**: Use a command like `theHarvester -d <domain> -b <source>` to gather emails, subdomains, and hostnames.
- **When to use**: Use it to collect open-source intelligence (OSINT) from various public sources, which is useful for social engineering, password attacks, and further enumeration.

## **haveibeenpwned {Website}**
- **Purpose**: A website that checks if an email address or password has been compromised in a data breach.
- **How to use**: Go to the website and enter an email address or password.
- **When to use**: Use this to verify if any credentials for a target have been leaked online, which can be useful for validating potential passwords found through other means.
