Cloudflare Zero Trust Setup Documentation
Based on my review of your Cloudflare One configuration, here's comprehensive documentation of your current setup:

Overview
This setup uses Cloudflare Zero Trust to secure access to self-hosted applications through Cloudflare Tunnel, with access controlled by policies that authenticate users via email and One-Time PIN.

1. Access Control Application
Application Name: lab-lockdown
Application Type: SELF-HOSTED
Application ID: ecd979dd-8693-422a-9c04-cdb3a4c25f9a
Configuration Details

Application URL: *.codeandcamera.me
Total Domains: 1
Policies Assigned: 1 (named "revised")
Date Created: May 31, 2025 • 12:45:56 AM
Last Edited: Jan 4, 2026 • 05:16:01 PM

This application protects all subdomains under *.codeandcamera.me with Zero Trust access policies.

2. Access Policy: "revised"
Policy ID: 51e5fb69-c781-4c09-b8cb-b2e1cd59c0d2
Policy Name: revised
Action: Allow
Session Duration: 24 hours
Policy Rules
Include Rule (OR logic)

Selector: Country
Value: United States
Users only need to be from the US to satisfy this requirement

Require Rules (AND logic - both must be met)

Login Method:

Selector: Login Methods
Value: One-time PIN
Users must authenticate using OTP sent to their email


Email Address:

Selector: Emails
Value: joelocklin@gmail.com
Only this specific email address is granted access



Policy Logic
To access protected applications, users must:

Be connecting from the United States (Include)
AND authenticate with One-time PIN (Require)
AND use the email address joelocklin@gmail.com (Require)


3. Cloudflare Tunnel: "homelab"
Tunnel Name: homelab
Tunnel ID: b25ce30f-f75b-433f-ba0e-a11f792c7cbc
Connector Type: cloudflared
Status: Healthy ✓
Uptime: 2 hours (at time of review)
Active Connectors
The tunnel has 2 active connector instances for redundancy:

Connector ID: e140ae77-08ce-4444-a70f-fb6476b15e51
Version: 2025.11.1
Connector ID: e3fe5624-0d9f-4418-a492-ef38095056e6
Version: 2025.11.1

Installation Command
bashcloudflared.exe service install ey7hijoiZD...

4. Published Application Routes
The tunnel exposes three applications through public hostnames:
Route 1: Grafana

Public Hostname: grafana.codeandcamera.me
Path: * (all paths)
Service: https://192.168.1.80:3000
Origin Configuration: noTLSVerify (disabled TLS verification to internal service)

Route 2: Prometheus

Public Hostname: prometheus.codeandcamera.me
Path: * (all paths)
Service: http://192.168.1.80:9090
Origin Configuration: noTLSVerify

Route 3: Dashboard

Public Hostname: dashboard.codeandcamera.me
Path: * (all paths)
Service: http://192.168.1.162:7576
Origin Configuration: None (standard HTTP)

Catch-all Rule
Rule: http_status:404
Returns a 404 error for any requests not matching the configured routes.

5. Architecture Overview
Traffic Flow

External users access *.codeandcamera.me domains
DNS resolves to Cloudflare's edge network
Cloudflare Access checks authentication (OTP + email verification)
If authenticated and authorized by policy "revised", traffic is forwarded
Cloudflare Tunnel routes traffic through secure connection to homelab
Traffic reaches internal services on private network (192.168.1.x)

Security Layers

Network Security: Cloudflare Tunnel (no open ports required)
Authentication: One-Time PIN via email
Authorization: Email-based access control + geographic restriction (US only)
Application Protection: Zero Trust policies on *.codeandcamera.me

Internal Network
All services run on private IP addresses in the 192.168.1.0/24 subnet:

192.168.1.80: Grafana (port 3000) and Prometheus (port 9090)
192.168.1.162: Dashboard (port 7576)


6. Key Features
Private Network Access
As documented in the Cloudflare docs, this setup enables private network access where:

Services don't need public IP addresses
Traffic is encrypted through Cloudflare's network
Both HTTP and non-HTTP resources can be exposed
End users connect via WARP client or browser-based access

Session Management

Sessions last 24 hours after successful authentication
Users must re-authenticate after session expiration
One-time PINs are sent to verified email addresses

High Availability

Two cloudflared connectors provide redundancy
If one connector fails, the other maintains connectivity
Both connectors are running the same version (2025.11.1)


Summary
This is a well-configured Zero Trust setup providing secure access to three homelab applications (Grafana, Prometheus, and a Dashboard) through Cloudflare's edge network. Access is restricted to a single authenticated user from the United States, with multi-factor authentication via One-Time PIN, all without exposing any ports on the home network.
