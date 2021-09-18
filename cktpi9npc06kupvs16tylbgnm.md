## How to Map a Hashnode Blog to your Domain using AWS Route 53

Blogging on Hashnode is totally free and it is the easiest way to start a developer blog on your personal domain for free and connect with the readers through their global dev community! Everything from a custom domain mapping to faster edge caching to AMP and SSL is free forever.


![HashnodeDomainTab.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631879050800/oJr9bpwUp.png)

So, if you already had your own domain registered on AWS then in a couple minutes you can map your blog to your custom domain with AWS Route 53 following the next steps.

### Steps

- Register on Hashnode by going to [hashnode.com](https://hashnode.com), create a new account and after filling in your name, title, email and blog's name you will be asked, where do you want this blog to open? Choose **I will use my domain**

*If you already signed up before, log in then go to your "Blog Dashboard" then "Domain" from the sidemenu, then "Custom Domain" tab*

![HashnodeBlogDomainChoice.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631874413057/-BIKwQlg2.png)

- Enter your domain name:´ without `http://` or `https://` like "[blog.basimhennawi.com](https://blog.basimhennawi.com)" and click **Next**

![EnterYourDomain.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631878104465/cCmeZTH-SS.png)

You'll get this message to head over to your DNS provider which is AWS in your case

![MapDomainToHashnode.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631876608704/Dpj3RGKby.png)

- So you need to open a new tab and log in to your AWS Console account and navigate to **Route 53** service, under **Hosted zones** from the sidemenu, and select your domain name, in this case "[basimhennawi.com](https://basimhennawi.com)" 
![Route53CreateRecord.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631875560965/4tNWuTfvv.png)

- Then click **Create record**, in the wizard set **Record name** with the subdomain you want your blog to be, in this case `blog`, and:
   - "**Record type**" with `A` and value with `76.76.21.21` or
   - "**Record type**" with `CNAME` and value with `hashnode.network`(*Not recommended*)

> 
If you use a DNS provider other than Route 53 for your domain name (like GoDaddy, Cloudflare, ...), then you might be able to point the domain to services hosted on AWS. Check if your DNS provider supports CNAME records on a root domain using techniques such as CNAME flattening, ANAME records, or WebHops redirection.

- Then click **Create Records**

![CreateRecordWizard.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631876742476/l4hoUPm2Rn.png)

- Now go back to Hashnode tab, and hit **I've added the records** button.

That's it! You are all set!

Usually DNS propagation happens quickly. However, in the worst case, it may take up to 24 hours.

*Note: If you want to double-check or delete the domain mapping go to your "Blog Dashboard" then "Domain" tab from the sidemenu.*

![HashnodeDomainTab.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1631878933014/TXAET5cSs.png)

Now, happy writing!

I'd love for you to leave me a feedback below in the comments!



