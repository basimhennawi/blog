## How to Preview Your Localhost on the Internet

Imagine you are building a cool website. To see it, you can visit `http://localhost:3000`.


![CoolProject.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1634556207805/EHQ3EeO3L.png)

The only problem is that the URL only works on your local computer. You could go out and buy a domain name, web server and SSL certificate then figure out how to get your code to work on the internet, but what if you want to share a quick preview of this website with a friend or maybe test a service worker over HTTPS.

![Overwhelming.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1634555878961/ouYLbgyj2.jpeg)

That's where tunnels come in. You can use free tools like `ngrok` and `cloudflare` tunnels to set up a secure but temporary connection between your machine/localhost and the internet. We will cover here Cloudflare tunnel as an example.

### Cloudflare Tunnel

Cloudflare Tunnel runs a lightweight daemon (cloudflared) in your infrastructure that establishes outbound connections (Tunnels) between your origin web server and the Cloudflare edge. In practical terms, you can use Cloudflare Tunnel to allow remote access to services running on your local machine.

### Installation

Just download the [Cloudflare Tunnel CLI tool](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation), confirm that `cloudflared` is installed correctly by running `cloudflared --version` in your command line:
```
cloudflared --version
```
### Start it

Run the tunnel command and point it to the host port you want to serve and it will spit out a URL. With a local development server running, a new Cloudflare Tunnel can be instantiated by running `cloudflared tunnel` in a new command line window, passing in the `--url` flag with your localhost URL and port. `cloudflared` will output logs to your command line, including a banner with a tunnel URL:

```
cloudflared tunnel --url http://localhost:3000
```
### Output

The result will be a randomly-generated URL like this `https://some-random-words.trycloudflare.com` that has been created and assigned to your tunnel instance. Visiting this URL in a browser will show the application running, with requests being securely forwarded through Cloudflare's edge network, through the tunnel running on your machine, to `localhost:3000`.

### That's it

Now anybody with the URL can preview your localhost on the internet.

More about configurations and security compromises, check out [Cloudflare docs](https://developers.cloudflare.com/pages/how-to/preview-with-cloudflare-tunnel).

I'd love for you to leave me a feedback below in the comments!