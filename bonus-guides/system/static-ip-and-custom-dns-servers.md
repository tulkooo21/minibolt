---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Static IP & custom DNS servers

Set a static IP address and custom DNS nameservers for your MiniBolt.

{% hint style="success" %}
Difficulty: Easy
{% endhint %}

<figure><img src="../../.gitbook/assets/static-ip-custom-dns.PNG" alt=""><figcaption></figcaption></figure>

The router reserves the IP address of the device for a time after going out, but if the device goes out some time, the next time that the device starts, the router could assign a different IP and you could lose access to your node. To avoid this, you need to set a static IP to your node.

{% hint style="danger" %}
**Be careful by setting this!** If you change the router, could be the new router doesn't work in the same IP address range as the old router, the device won't be registered on the local network and will be completely out of the connection.

💡 To avoid this, you will need to **enable the "Automatic (DHCP)" mode before changing the router**, following the [Set the DCHP (automatic) configuration](static-ip-and-custom-dns-servers.md#set-the-automatic-dhcp-mode-configuration-by-command-line) to ensure that the DHCP server auto assigns an IP to the node in the range you are working on, and if you want, after router change, reconfigure the static IP address again following the [Option 2](static-ip-and-custom-dns-servers.md#option-2-after-ubuntu-server-installation-by-command-line) of this guide.

If you don't do this, you will have to attach the monitor screen or television and keyboard to fix this.
{% endhint %}

In addition, you can customize your DNS servers to improve your privacy, normally your ISP, gives you the router with its own DNS servers configured by default, and this does that you exposes all of your navigation trackings to your ISP, affecting seriously your privacy.

### Option 1: At the beginning, during the Ubuntu Server installation GUI

When you arrive at **step 5** of the [Ubuntu Server installation](../../index-1/operating-system.md#ubuntu-server-installation), you can want to choose set a static IP address and customize the DNS name server/s.

Wait for the router to automatically assign the IP address to your MiniBolt node to find out what IP range the node is in, you will be able to assign the same or a different IP in the next step.

<figure><img src="../../.gitbook/assets/static-ip-dns-gif.gif" alt=""><figcaption><p>GIF example of a Static IP &#x26; custom DNS server configuration</p></figcaption></figure>

> > **Subnet:** your router subnet, e.g 192.168.1.0/24
>
> > **Address**: your assigned local IP address, eg. 192.168.1.29
>
> > **Gateway:** you router IP, eg. 192.168.1.1
>
> > **Name servers:** DNS servers choosen (<mark style="color:red;">**important!**</mark>), eg. same of gateway (192.168.1.1,192.168.1.1) (your ISP DNS) or Custom DNS server (recommended): Cloudflare (1.1.1.1,1.0.0.1) or another, check the next annotations
>
> > **Search domains:** \<left blank>

{% hint style="info" %}
This is **only** an **example** if your local network IP range is `192.168.1.0-255`, because for this case, the router assigned to the device the IP address **192.168.1.29**, but it could be **192.168.0.29** (`192.168.0.0-255)`, then you will need to set this instead:

> **Subnet:** 192.168.0.0/24

> **Gateway:** 192.168.0.1
{% endhint %}

{% hint style="danger" %}
In this step, you must set DNS name servers too, otherwise, the host will be offline and will not be able to connect to the Internet
{% endhint %}

{% hint style="info" %}
For this example, we have configured [Cloudflare DNS name servers](https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/) (1.1.1.1,1.0.0.1) but you could put other privacy-focused DNS like:

* [Quad9](https://quad9.net/): 9.9.9.9,149.112.112.112
* [MullvadDNS](https://mullvad.net/en/help/dns-over-https-and-dns-over-tls): 194.242.2.2
{% endhint %}

### Option 2: After Ubuntu Server installation (by command line)

After doing the [1.3 Remote access](remote-access.md) section, you could want to set a static IP address to your MiniBolt by the command line.

#### Preparations

* Stay login with user **admin**, and check your current data network interface by doing

```bash
ip address
```

Check your configuration, the next output is **only** an **example** of a concrete case, but in your case, it could be different:

<pre><code>1: lo: &#x3C;LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: <a data-footnote-ref href="#user-content-fn-1">eno1</a>: &#x3C;BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 40:a8:f0:ac:6a:37 brd ff:ff:ff:ff:ff:ff
    altname enp0s25
    inet 192.168.1.147/24 metric 100 brd 192.168.1.255 scope global dynamic eno1
       valid_lft 76855sec preferred_lft 76855sec
    inet6 fe80::42a8:f0ff:feac:6a37/64 scope link
       valid_lft forever preferred_lft forever
</code></pre>

Definitions in the case of before:

> > ```
> > <interface> = e.g eno1
> > ```
>
> > ```
> > <ipaddress> = e.g 192.168.1.147
> > ```
>
> > ```
> > <gateway> = 192.168.1.1 (this case) -> case 192.168.0.X, choose 192.168.0.1
> > ```

{% hint style="info" %}
Take note of your case data, you will need it later
{% endhint %}

* Check the current DNS server set, typing the next command

```bash
resolvectl status
```

**Example** of expected output (more common):

<pre><code>Global
       Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
resolv.conf mode: stub

Link 2 (eno1)
    Current Scopes: DNS
         Protocols: +DefaultRoute +LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: <a data-footnote-ref href="#user-content-fn-2">192.168.1.1</a>
       DNS Servers: <a data-footnote-ref href="#user-content-fn-3">192.168.1.1</a>
        DNS Domain: home
</code></pre>

In the case of before:

> ```
> <nameserver1> = 192.168.1.1 ( = <gateway>, the DNS servers of your ISP)
> ```
>
> ```
> <nameserver2> = (secondary DNS server, not setted in this case)
> ```

#### Configuration

{% hint style="warning" %}
Remember, always back up your current network configuration before making changes. This ensures you can restore the previous configuration if something goes wrong
{% endhint %}

We will use Netplan with the `systemd-networkd` renderer backend

* Edit the content of the next file

<pre class="language-bash"><code class="lang-bash"><strong>sudo nano /etc/netplan/00-installer-config.yaml
</strong></code></pre>

* Replace the content to match this template

```
# This is the network config written by 'subiquity'
network:
  ethernets:
    <interface>:
      addresses:
      - <ipaddress>/24
      nameservers:
        addresses:
        - <nameserver1>
        - <nameserver2>
        search: []
      routes:
      - to: default
        via: <gateway>
  version: 2
```

{% hint style="info" %}
Replace **\<interface>**, **\<ipaddress>**, **\<nameserver1>, \<nameserver2>** <- (name server optional but recommended)**,** and **\<gateway>** to your own data.\
\
You can choose the DNS server (**\<nameserver1> +** **\<nameserver2>**) whatever you want, including the default one set in the DNS server of your router (normally DNS servers of your ISP), in this last case, you should type the **\<gateway>** address in **\<nameserver1>** and the same in **\<nameserver2>**.

For this example, we have configured [Cloudflare DNS name servers](https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/) (1.1.1.1,1.0.0.1) <- (recommended), but you could set others privacy-focused servers:

* [Quad9](https://quad9.net/): 9.9.9.9,149.112.112.112
* [MullvadDNS](https://mullvad.net/en/help/dns-over-https-and-dns-over-tls): 194.242.2.2
{% endhint %}

<details>

<summary>Example ⬇️</summary>

```
# This is the network config written by 'subiquity'
network:
  ethernets:
    eno1:
      addresses:
      - 192.168.1.87/24
      nameservers:
        addresses:
        - 1.1.1.1
        - 1.0.0.1
        search: []
      routes:
      - to: default
        via: 192.168.1.1
  version: 2
```

</details>

* Apply the configuration temporarily

```bash
sudo netplan try
```

{% hint style="info" %}
This will apply the configuration and start a 120-second timer
{% endhint %}

* If the network works as expected, you need to confirm the changes. If the network is not working or you don't confirm, the changes will be rolled back after 120 seconds. To confirm, type

```bash
sudo netplan apply
```

{% hint style="success" %}
Now you have set your static IP address and custom DNS servers
{% endhint %}

{% hint style="info" %}
If you chose a different IP address than the router assigned you at first and has currently, this step could break the current SSH connection, reconnect using the new and chosen IP address
{% endhint %}

#### Check changes

* To check the successful IP address change, type the next command

```bash
ip address
```

{% hint style="info" %}
The output of this command **may not change**, depending on whether you chose the same IP that the router originally assigned you at the beginning, or whether you chose to change to another
{% endhint %}

* To check the successful DNS server change, type the next command

```bash
resolvectl status
```

<details>

<summary>Example of expected output ⬇️</summary>

<pre><code>Global
       Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
resolv.conf mode: stub

Link 2 (eno1)
    Current Scopes: DNS
         Protocols: +DefaultRoute +LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: <a data-footnote-ref href="#user-content-fn-4">1.1.1.1</a>
       DNS Servers: <a data-footnote-ref href="#user-content-fn-5">1.1.1.1</a> <a data-footnote-ref href="#user-content-fn-6">1.0.0.1</a>
[...]
</code></pre>

</details>

Or if you prefer:

```bash
networkctl status
```

<details>

<summary>Example of expected output ⬇️</summary>

<pre><code>●        State: routable
  Online state: online
       Address: 192.168.1.71 on eno1
                10.0.1.1 on wg0
                fe80::1e69:7aff:feac:8129 on eno1
       Gateway: 192.168.1.1 on eno1
           DNS: <a data-footnote-ref href="#user-content-fn-7">1.1.1.1</a>
                <a data-footnote-ref href="#user-content-fn-8">1.0.0.1</a>
           NTP: 10.28.64.1
</code></pre>

</details>

### Set the A**utomatic (DHCP) mode** configuration (by command line)

If you go to change the router you could want to enable or check if you have enabled the a**utomatic (DHCP) mode** configuration to avoid problems

We will use Netplan with the `systemd-networkd` renderer backend (default on Ubuntu Server)

* Edit the content of the next file

<pre class="language-bash"><code class="lang-bash"><strong>sudo nano /etc/netplan/00-installer-config.yaml
</strong></code></pre>

* Replace the content to match this template, replacing **\<interface>** with your data obtained in the [Preparations](static-ip-and-custom-dns-servers.md#preparations) section before

<pre><code><strong># This is the network config written by 'subiquity'
</strong>network:
  ethernets:
    &#x3C;interface>:
      dhcp4: true
  version: 2
</code></pre>

* Apply the configuration temporarily

```bash
sudo netplan try
```

{% hint style="info" %}
This will apply the configuration and start a 120-second timer
{% endhint %}

* If the network works as expected, you need to confirm the changes. If the network is not working or you don't confirm, the changes will be rolled back after 120 seconds. To confirm, type

```bash
sudo netplan apply
```

{% hint style="info" %}
Check changes are applied correctly following the [same steps](static-ip-and-custom-dns-servers.md#check-changes) as before
{% endhint %}

{% hint style="success" %}
You have gone back to the **automatic (DHCP) mode** configuration successfully
{% endhint %}

[^1]: \<interface>

[^2]: Check this

[^3]: Check this

[^4]: Check this

[^5]: Check this

[^6]: Check this

[^7]: Check this

[^8]: Check this
