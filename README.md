# Automated Server
The goal of this guide is to provide a basic list of steps for creating a fresh cloud server setup. The setup is geared for the purpose of automatic deployment for APIs and Webapps using Github Actions. I am not a profesional at server configuration and my guide could potentially lead to sercurity risks that I'm unaware of. However, this infomation is mostly gathered from varies other guides which I assume was written by server configuration experts.
### Linode specific manual steps [Jun 2022]
1. Login to https://login.linode.com/login
2. On linodes page (accessed by left menu at top or https://cloud.linode.com/linodes) click “Create Linode” button located in the top right
3. For “Choose a Distribution” select latest version of Ubuntu LTS
4. For “Region” select Dallas, TX (or what makes the most sense)
5. For “Linode Plan” choose “Shared CPU” tab and select “Linode 2 GB” option (or what you need accordingly)
6. Type give a Label for your instance under “Linode Label”
7. (optional) create or select a tag(s) under “Add Tags”
8. Create root password under “Root Password”
9. (Should I add a SSH Key at this step?)
10. (Skip “Attach a VLAN”)
11. (Skip “Add-ons”)
12. Click Create Linode
13. Wait for “Running” indicator at top left of https://cloud.linode.com/linodes/<linode-id>
