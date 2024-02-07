# README

## Background

Devices like the Nintendo Switch have no way of selecting a custom server to connect to.
Enter packages like 'BedrockConnect' which allows you to connect to a Minecraft "server" that presents a dialog box asking for which MC server you'd like to connect to!

The `dockerfile` (when combined with the `custom_server.json` and `player_whitelist.txt`) build a custom BedrockConnect container that is locked down to a server of your choosing, with players of your choice.

You can then use the `docker-compose.yml` that's in this folder to stand up an instance of that container locally.

Once that's done, you will then want to follow the instructions in the [DNS_GUIDE.md](DNS_GUIDE.md) to update your local network to point to this container instead of the "Featured Servers" in the Minecraft server list.

It's a hack, but it works!

## Summary

> 1. Use the `docker-compose.yml` + `dockerfile` in this folder to stand up a BedrockConnect instance (or [follow these instructions](https://github.com/Pugmatt/BedrockConnect))
> 2. Point [these domains](#domains-list) at your new BedrockConnect instance via DNS
> 3. Update the DNS settings on the Switch to point at your customised DNS server (if your home network doesn't do this already)

### Domains List

The featured servers in bedrock do not use the standard domains, so I compiled a list of them here.

- The Hive uses geo.hivebedrock.network
- Lifeboat uses mco.lbsg.net
- mineplex uses mco.mineplex.com
- cubecraft uses mco.cubecraft.net
- mineville uses play.inpvp.net
- Pixel Paradise uses play.pixelparadise.gg
- Galaxite uses play.galaxite.net
