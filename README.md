# dns-adblock

This repository contains the Ansible playbook that we use to generate the ad and tracker blocking files for our DNS over TLS / DNS over HTTPS, and VPN server __(also known as VPN relay)__ based DNS blocking.

This is imported to our VPN servers frequently. We use this repository to pre-generate the DNS zonefiles that we import onto our VPN servers. The zone files are verified with GPG.

# Lists

The following lists are what we import to our service. You can find these defined in `inventory/group_vars` for the server type you wish to view.

- `doh`: DNS over HTTPS / DNS over TLS
- `relay`: VPN servers (relays)

### Trackers

We currently use these tracker blocklists with our service:
- easylist-privacy: https://justdomains.github.io/blocklists/lists/easyprivacy-justdomains.txt
- windows-spy-blocker-spy: https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt

### Advertising

We currently use these advertising blocklists with our service:
- easylist-adblock: https://v.firebog.net/hosts/Easylist.txt
- adaway: https://adaway.org/hosts.txt
- AdguardDNS: https://v.firebog.net/hosts/AdguardDNS.txt
- Admiral: https://v.firebog.net/hosts/Admiral.txt
- anudeepND: https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
- yoyo: https://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext
- frellwits-swedish-hosts-file: https://raw.githubusercontent.com/lassekongo83/Frellwits-filter-lists/master/Frellwits-Swedish-Hosts-File.txt
- AdguardDNS-mobile-ads: https://raw.githubusercontent.com/r-a-y/mobile-hosts/master/AdguardMobileAds.txt

### Adult content 

We currently use this Adult content blocklist for our service:
- brijrajparmar27-pornography: https://raw.githubusercontent.com/brijrajparmar27/host-sources/master/Porn/hosts
- clefspeare13-pornhosts: https://raw.githubusercontent.com/StevenBlack/hosts/master/extensions/porn/clefspeare13/hosts
- sinfonietta-snuff-hosts: https://raw.githubusercontent.com/Sinfonietta/hostfiles/master/snuff-hosts
- sinfonietta-pornography-hosts: https://raw.githubusercontent.com/Sinfonietta/hostfiles/master/pornography-hosts
- tiuxo-hostlist-pornography: https://raw.githubusercontent.com/tiuxo/hosts/master/porn

### Gambling

We currently use these gambling blocklists with our service:
- blocklist-project: https://raw.githubusercontent.com/blocklistproject/Lists/master/alt-version/gambling-nl.txt

## Pull requests / Issues / Updating block lists

We really welcome your feedback for lists to use for blocking! We cannot action them all individually, but we will read them. This is an actively worked on project and we **will** take into consideration all of your requests, even if we do not reply to them.

# Custom DNS entries

The following is a list of all the IP addresses we use for our DNS based blocking.

These IPs can be used within custom DNS in our configuration files, or via our Apps.

To block _everything_ enter: `100.64.0.31`

### Ads and Tracker combinations
    100.64.0.1 - Ad blocking only
    100.64.0.2 - Trackers only
    100.64.0.3 - Ad blocking and tracker blocking

### Malware serving website combinations
    100.64.0.4 - Malware blocking only
    100.64.0.5 - Ad blocking and malware blocking
    100.64.0.6 - Tracker blocking and malware blocking
    100.64.0.7 - Ad blocking, tracker blocking and malware blocking

### Adult content blocking combinations
    100.64.0.8 - Adult content blocking only
    100.64.0.9 - Adult content and ad blocking
    100.64.0.10 - Adult content and tracker blocking
    100.64.0.11 - Adult content blocking, ad blocking and tracker blocking
    100.64.0.12 - Adult content blocking and malware blocking
    100.64.0.13 - Adult content blocking, ad blocking and malware blocking
    100.64.0.14 - Adult content blocking, tracker blocking and malware blocking
    100.64.0.15 - Adult content blocking, ad blocking, tracker blocking and malware blocking

### Gambling website combinations
    100.64.0.16 - Gambling blocking only
    100.64.0.17 - Gambling blocking and ad blocking
    100.64.0.18 - Gambling blocking and tracker blocking
    100.64.0.19 - Gambling blocking, ad blocking and tracker blocking
    100.64.0.20 - Gambling blocking and malware blocking
    100.64.0.21 - Gambling blocking ad blocking and malware blocking
    100.64.0.22 - Gambling blocking, malware blocking and tracking blocking
    100.64.0.23 - Gambling blocking, ad blocking, malware blocking and tracker blocking
    100.64.0.24 - Gambling blocking and adult blocking
    100.64.0.25 - Gambling blocking, ad blocking and adult content blocking
    100.64.0.26 - Gambling blocking, adult content blocking, and tracker blocking
    100.64.0.27 - Gambling blocking, ad blocking, adult content blocking and tracker blocking
    100.64.0.28 - Gambling blocking, adult content blocking and malware blocking
    100.64.0.29 - Gambling blocking, ad blocking, adult content blocking, and malware blocking
    100.64.0.30 - Gambling blocking, adult content blocking, malware blocking and tracker blocking
    100.64.0.31 - Ad blocking, adult content blocking, gambling blocking, malware blocking, tracker blocking ("Everything")

# Building

The following steps are useful only if you wish to build the lists yourself.

The output files located in `output/relay/` are what are imported onto our VPN servers.

## Requirements
- Ansible Core 2.12.x =<

## Step by step

  - Ensure the values in `group_vars/<group>.yml` are up to date with any block lists
  - Ensure you have added any 'custom' extra lists or websites to block
  - Run the playbook to generate the lists:
    - `ansible-playbook -i inventory/ playbook.yml`
    - `ansible-playbook -i inventory/ playbook.yml --tags=readme` can be used to generate the README on its own
  - View the output (once pushed) at `https://raw.githubusercontent.com/mullvad/dns-adblock/main/output/<group>.txt?raw=true`
  - Run test script: `cd scripts && ./check_zonedata.sh`
  - Sign the outputted relay zonefiles with your GPG code signing key, for example:
    - `for list in adblock adult privacy gambling; do gpg2 --detach-sign --armor output/relay/relay_${list}.zone > output/relay/relay_${list}.gpg; done`
    - `for list in adblock adult privacy gambling; do gpg2 --detach-sign --armor output/relay/relay_${list}.txt > output/relay/relay_${list}.txt.gpg; done`
  - Verify the outputted GPG signed files, for example:
    - `for list in adblock adult privacy gambling; do echo "Verify: ${list}" && gpg2 --verify output/relay/relay_${list}.gpg output/relay/relay_${list}.zone; done
`
