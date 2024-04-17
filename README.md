Credit to the original repo goes to:  https://github.com/DASD-Panthers/Fortigate-Threat-Feeds

This can be applied as an object for policies on interface pairs (inside > outside, outside > inside.) 

It can be used for SSL VPN as well using the below guides:

Moving SSLVPN to a loopback interface:
- https://www.reddit.com/r/fortinet/comments/1b2xmj7/move_sslvpn_to_loopback_interface/
- https://community.fortinet.com/t5/FortiGate/Technical-Tip-Prevent-TOR-IP-addresses-from-accessing-SSL-VPN/ta-p/269785

Threat feeds blocking ASN numbers: 
- https://www.reddit.com/r/fortinet/comments/1b2ewwo/using_sslvpn_reduce_your_security_footprint_block/

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

This Python script scrapes the URLs of many common hosting providers and known malicious ASN networks that attackers use for USA-based attacks. So even if you are geoblocking everything but the USA for access, you are likely still getting attackers attempting to connect to your on-site services. If you receive threat feeds, malicious IP lists, etc; it may be hard to manage all of them and do something with this data. Many attackers rotate their connections across many IPs so blocking individual public IPs can be like whack-a-mole.  If you find the common IP range they are attacking you from you can look up the ASN number and block their whole IP range to prevent further issues.  As different ASNs get used for attacks we can add them to the master list which will then run daily and be automatically updated in the firewall threat feed. 

The source of all the data to scrape is the "MasterASN-List.txt" file.  In there you add a name description of the ASN if you want and the URL of the ASN provider.  The URL we like to use is from ipinfo.app - if you use the same base URL and just update the ASN at the end of it you will get all the networks currently allocated to that ASN in the webpage it points to.  Note: Make sure you add # to the beginning of the name description, the Python script knows to ignore this value and not try to bring the data into the output file.  

We have the action script set to run at 8 am CST every day via an action .yml file.  We then point my firewall to the raw data version of the output file "SSLVPN-ASN-Blocks.txt" and set it to update daily after my GitHub script runs its update. 

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

General setup- if copied:

**Note**- You can fork off the original if you don't want to maintain your repo. If you want to maintain your own see below.
- Download all files from this repo.
- Create your repo.
- Create your personal access token (follow best practice to expire this after X amount of days.) Copy this code, see details here: https://docs.github.com/en/enterprise-server@3.9/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens
- Set permissions for token up for Repo, admin:org read, and user.
- Open Actions in repo > then click New Workflow > Type "Simple Workflow." Click Configure> name ASN-Scrape.yml as the file name. Copy and paste the .yml code into the edit box that you got for the original repo. This will create .github/workflows folder with .yml file in it.
- Go to repo setting > Actions > General > Work flow permissions > select "read and write permissions." Next, go to Secrets and Variables> Actions > New Repository secret. Provide whatever name you want, and paste in the personal token code you created earlier. (You will have to rotate this as the personal token expires.)
- Finally test by going to Actions > Workflows > Run workflow.
- Go to SSLVPN-ASN-Blocks.txt and then click "Raw" to get the URL.
- Open FortiGate > Security Fabric > Create New > Threat Feeds > IP address. Paste in the raw GitHub URL. Turn off HTTP basic authentication. Then click OK.
- This will create an object on the firewall that can be used for policies to apply however you see fit (ingress/egress.) 

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

How to fix the GitHub workflow error:

1.0 - Change workflow permissions - otherwise it will error out on the actions process.  
1.1 - https://stackoverflow.com/questions/72851548/permission-denied-to-github-actionsbot
1.2 - Personal tokens must be generated and rotated out as a general rule of thumb. Use this token as a repo secret under settings.


