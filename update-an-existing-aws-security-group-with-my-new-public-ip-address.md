During Corona times, I'm doing home-office very often my home country, *Egypt*, where setting up and connecting to a VPN to my company's office, *Germany*, was gruelling at times. On the other hand, I want to access resources protected by **AWS security groups**.

Hmm, easy-peasy I could just add a **Security Group Rule** whitelisting my IP, right?

Correct! but I shouldn't get too attached because most likely, my Internet Service Provider (ISP) issues me a **dynamic IP address**, which means it’s subject to change on me, like when:

- I unplug my router
- I suffer a blackout 

If it weren’t a dynamic IP address, it would be referred to as a static IP address…unchanging - then tbh you should haven't landed here for this blog ;).

So to cut it short, I could do that with a lot of clicking on the **AWS console**, but it's even harder for me cause I usually have more than one AWS account opened, that means jumping between those accounts is wasted effort.


![SecurityGroupsConsoleScreenshot.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1629497299431/ayFJF02jH.png)

So what about running a script from my local machine doing this for me...!


Here’s how the **Bash script** would look:


```
#!/bin/bash
curl v4.ifconfig.co > ip.txt
awk '{ print $0 "/32" }' < ip.txt > ipfull.txt
export foo=$(cat ipfull.txt)
aws ec2 authorize-security-group-ingress --region=eu-central-1 \
    --group-id sg-12345678 \
    --ip-permissions IpProtocol=tcp,FromPort=27017,ToPort=27017,IpRanges="[{CidrIp=${foo},Description='tmp'}]"
 ```
 
Now let's go through the script line by line:
 
1. I fetch my IP address from v4.ifconfig.co page and write to a .txt file I'll name `ip.txt` and since I’m using only one `>` rather than two, this operation will either create the file or, if it already exists, overwrite any text that might currently live there.
2. Since a security group requires a full address in CIDR format, I’ll use `awk` to read `ip.txt`, append the characters `/32`, and output it into a new file I'll name `ipfull.txt`. That file will now contain my full IP in CIDR format.
3. Now I’ll export the contents of the `ipfull.txt` file to a shell variable I’ll name `$foo`.
4. Finally, we arrive at the money line. The EC2 subset of the general AWS CLI includes a command called `authorize-security-group-ingress` which can be used to add a new rule to the inbound policies of an existing security group. This example assumes the group I want to edit of Id `sg-12345678`. I specify the protocol `tcp` and port number `27017` (as I need it to access MongoDb but the SSH default, `22`) and then pass the value of `$foo` to the — cidr argument. Also, you could set Description for it, like `tmp` or so.
 
Assuming that you named the script `whitelist-ip.sh`, you’ll need to make the file executable and then run it like this:
```
chmod +x whitelist-ip.sh
./whitelist-ip.sh
```
Done.

I'd love for you to leave me a feedback below in the comments!