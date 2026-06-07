---
title: TIL Cloudflare email routing
date: 2026-06-05
draft: false
tags:
  - cloudflare
  - homelab
  - automation
  - proxmox
  - selfhosted
  - today-i-learned
  - etsy
---


# TIL Cloudflare Routing Blew My Mind

If you have read my previous article about [[TIL Proxmox VE Helper Script]], you know that I am planning to install on my proxmox server the N8N community version to learn and create my own automation. 

The reason is that I want to automate the creation of my Etsy listings. The idea is simple: I prepare the content for each listing in a Google Sheet, and n8n — running quietly at home on my Proxmox — takes care of publishing one listing per day on Etsy while I focus on other things, or just live my life.

To make this work, n8n needs to be reachable from the internet. Etsy's API needs to be able to talk back to it, and if n8n is just sitting on my local network, that is not going to happen.

So I started going down the rabbit hole of how to expose n8n safely. And this is where it gets interesting.

### Tailscale or Cloudflare?

I was already planning to install Tailscale on my Proxmox, the reason being that I wanted to be able to access my server when I am away from home. In my head, maybe that could help with n8n too?

Turns out they solve very different problems. Tailscale creates a private network between _your_ devices. Great for me accessing my server from my parents house, but Etsy's servers are not on my Tailscale network. They cannot reach n8n through it.

Cloudflare Tunnel is a different thing. You install a small agent on your server, it dials out to Cloudflare's infrastructure, and from that moment your server has a real public URL. No ports to open on your router, no exposing your home IP. And because you need a domain to set this up, I decided to just buy one directly from Cloudflare. They sell domains at wholesale price — no markup — and DNS is managed automatically. Easy decision.

So I bought one. Done.

### Wait, now I have a domain name...

And this is where the conversation went somewhere I completely did not expect.

Once you own a domain, you start asking yourself: what else can I do with this? Hosting a website on Proxmox is technically possible, but risky — you are exposing your home server to the public internet, and one misconfiguration can have consequences I do not want to think about. For a public blog or portfolio, something like Cloudflare Pages is a much safer option, and it is free.

But then: what about email with my own domain?

I asked, and the answer was Cloudflare Email Routing. You can create email addresses on your domain and forward them to any inbox you already have. So `hello@yourdomain.com`, `sales@yourdomain.com`, `customerservice@yourdomain.com` — all landing in my one Gmail. For free.

And then you enable the catch-all.

### The catch-all is where my brain exploded a little

The catch-all means that _any_ address @yourdomain.com, even ones you never created, get forwarded to your Gmail.

Let me explain why this matters for me. I have two Etsy shops, and planning to open a third one. Each shop needs a unique email address. Until now I have been creating separate Google accounts for each one — separate Gmail, separate Google Drive, separate everything. It is a mess to manage and it just keeps growing.

With the catch-all? I go to Etsy, I type `randomstickers@yourdomain.com`, I sign up. The verification email lands in my Gmail. Done. No new Google account. No switching between inboxes. No mess.

Same for Instagram, TikTok, Facebook — each brand gets its own address, all landing in one place. Each service sees a different, real email address. I see one inbox.

And when I want to _reply_ from one of those addresses, Gmail has a "Send As" feature. You add the address, Gmail sends a verification to it, it arrives in your inbox via Cloudflare routing, you confirm. From that moment, when a customer emails `hello@yourdomain.com` and I reply, they see `hello@yourdomain.com` They have no idea it is all the same person, in the same inbox.

### The cost of all of this

The domain. About €10 a year. Everything else is free.

I have been creating separate Google accounts for years, thinking that was just how it had to work. It is not. One domain, one inbox, infinite addresses, infinite possibilities.

And just like that, one question about how to expose n8n to the internet turned into completely rethinking how I manage my online presence.

This is what I love about going down the rabbit hole. You never know where you are going to end up.

Today I learned.

---

