# Cloudflare Tunnel Configuration Guide

This guide describes how to configure Cloudflare Tunnels (formerly Argo Tunnel) in your Cloudflare account to securely expose the Sward Warden frontend and backend services hosted in your Kubernetes cluster, without opening any inbound firewall ports.

---

## Step 1: Access Cloudflare Zero Trust

1. Log in to the [Cloudflare Dashboard](https://dash.cloudflare.com/).
2. In the left navigation sidebar, click on **Zero Trust**.
   * *If you haven't activated Zero Trust before, you might be prompted to choose a plan. The free plan supports up to 50 users and includes Tunnels.*

---

## Step 2: Create a Cloudflare Tunnel

1. Inside the Zero Trust dashboard, navigate to **Networks** -> **Tunnels** in the left sidebar.
2. Click **Create a tunnel**.
3. Choose **Cloudflared** (default) and click **Next**.
4. Enter a name for your tunnel (e.g., `home-k8s-tunnel`) and click **Save tunnel**.

---

## Step 3: Retrieve the Tunnel Token

Once the tunnel is created, Cloudflare will present you with command-line examples for installing the agent. 

1. Look for the **"Choose your environment"** section. Select **Kubernetes**.
2. Cloudflare will display a box with a YAML installation block. Within that YAML code, look for the following environment variable declaration:
   ```yaml
   spec:
     containers:
     - name: cloudflared
       args:
       - tunnel
       - --no-autoupdate
       - run
       - --token
       - <VERY_LONG_STRING_HERE>
   ```
3. Copy that token string (`<VERY_LONG_STRING_HERE>`). This is your **Tunnel Token**.
4. Keep this token secure. You will need it to create the Kubernetes Secret in your cluster:
   ```bash
   kubectl create secret generic cloudflared-token \
     --namespace=istio-ingress \
     --from-literal=token=<YOUR_TUNNEL_TOKEN>
   ```

---

## Step 4: Configure Routing (Public Hostnames)

Now you must configure how Cloudflare directs incoming traffic down the tunnel.

1. Click on the **Public Hostname** tab in your tunnel settings.
2. Click **Add a public hostname**.
3. Configure the settings for your **Frontend** and **Backend**:

### A. Routing Configuration
* **Subdomain:** `sw-dev`
* **Domain:** Choose your registered domain from the dropdown (e.g., `polecatworks.com`).
* **Path:** Leave blank.
* **Service Type:** `HTTP`
* **URL:** `gateway.istio-ingress.svc.cluster.local:80`

> [!NOTE]
> Since we route to the Istio Ingress Gateway (`gateway.istio-ingress.svc.cluster.local:80`), Istio will use the `Host` header sent by the client (`sw-dev.polecatworks.com`) to route traffic to the appropriate service inside your cluster. The frontend matches `/` (all paths) and the backend matches `/sward`, so they both use the same public hostname.

---

## Step 5: Verify DNS Records

When you save the public hostnames, Cloudflare automatically creates `CNAME` records in your DNS zone pointing to your specific tunnel address (e.g., `<tunnel-id>.cfargotunnel.com`).
* You can verify these records under your domain's **DNS** -> **Records** page in the main Cloudflare Dashboard. Do not modify or delete these managed CNAME records.
