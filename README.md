# cloudflared-vpn
VPN home using K8s and Cloudflared

Following instructions from https://savjee.be/blog/securely-access-home-network-with-Cloudflare-Tunnel-and-WARP/#:~:text=Cloudflare%20WARP%20is%20an%20interesting,traffic%20flows%20through%20that%20server; but using K8s.

Notes:
- On step 1: Replace `192.168.69.0/24` with your home subnet \(should be /24 - /30 according to the docs\).
- On step 1: Replace `mytunnel-name` with the name of the tunnel you want to create \(pick something cool like, "matts-backend"\).
- On step 2: Update line 81 with your tunnel name.
- On step 4: The path to the credentials file was shown in the output of `cloudflared tunnel create mytunnel-name`. Update it accordingly.




## Steps
1. Create tunnel and add route\(Can be done on any device, I used my laptop\)
```
cloudflared login
cloudflared tunnel create mytunnel-name
cloudflared tunnel route ip add 192.168.69.0/24 mytunnel-name
```
- Note: the output of `cloudflared create mytunnel-name` will give you the full path to your credentials file. You'll need this in step 4.

2. Update line 81 of vpn.yaml with the name of your tunnel from step1
3. Create the infra
```
kubectl apply -f vpn.yaml
```
4. Create secret \(as noted on step 1, you'll replace `/path/to/<UUID>.json` with the path to your credentials file)
```
kubectl create secret generic tunnel-credentials --from-file=credentials.json=/path/to<UUID>.json -n cloudflare-vpn
```
5. Install the WARP client on your device: https://developers.cloudflare.com/warp-client/get-started/windows/


6. Configure WARP permissions as described here: https://savjee.be/blog/securely-access-home-network-with-Cloudflare-Tunnel-and-WARP/#:~:text=Cloudflare%20WARP%20is%20an%20interesting,traffic%20flows%20through%20that%20server
