# homelab
Example:
You create one tunnel called "homelab"
You run cloudflared on your desktop → that's connector #1
You run cloudflared on your Raspberry Pi → that's connector #2
Both connectors connect to the same "homelab" tunnel
You add a route: 192.168.1.0/24 → homelab tunnel
Traffic is load-balanced across both connectors automatically

Why multiple connectors?

High availability: If one dies, traffic flows through the other
Load balancing: Spread traffic across multiple machines
Performance: More bandwidth
