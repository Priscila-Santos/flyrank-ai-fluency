# DNS Walkthrough

## DNS Walkthrough – Connecting My Website to a Custom Domain

The Domain Name System (DNS) works like the Internet’s phonebook. Instead of remembering the numeric IP address of a website, people type an easy-to-read name such as **priscilasantos.flyrank.ai**. DNS translates that name into the correct server address.

When FlyRank provides my subdomain, the first step will be adding the custom domain inside my hosting provider (Vercel). This tells Vercel that it should respond whenever someone visits that domain.

FlyRank’s Operations team will create the DNS record for my subdomain. In many hosting setups this is a **CNAME record**.

A **CNAME (Canonical Name) record** is a DNS record that points one domain or subdomain to another domain instead of directly to an IP address. Rather than storing the server address itself, it tells DNS to use another hostname. This makes it easier to manage infrastructure because the hosting provider can change server IPs without requiring changes to my DNS configuration.

For my portfolio, the CNAME record will point my FlyRank subdomain to the hostname provided by my hosting platform (Vercel). Once the DNS record is created, visitors using **priscilasantos.flyrank.ai** will automatically reach the same website currently available through the Vercel URL.

The process works like this:

1. A visitor types **priscilasantos.flyrank.ai** into a browser.
2. The browser asks a DNS resolver where that domain is located.
3. The resolver contacts the authoritative nameserver for the **flyrank.ai** domain.
4. The nameserver returns the CNAME record pointing to the Vercel hostname.
5. The resolver follows that record and obtains the correct server address.
6. The browser connects to Vercel using HTTPS.
7. Vercel serves my portfolio and automatically provides a valid SSL certificate, so the browser displays the secure padlock icon.

Because DNS changes are distributed across many servers worldwide, updates are not immediate. This delay is called **DNS propagation** and can take anywhere from a few minutes to several hours.

### My deployment checklist

* Portfolio deployed and working on Vercel.
* Add the FlyRank custom domain inside the Vercel project settings.
* Wait for FlyRank to create the DNS record.
* Verify that the CNAME record points to the hostname provided by Vercel.
* Wait for DNS propagation.
* Confirm that the custom domain loads correctly.
* Verify that HTTPS is enabled and the SSL certificate is active.
* Test the website in a private browser window to ensure everything works correctly.

