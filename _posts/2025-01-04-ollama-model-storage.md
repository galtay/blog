---
title: "changing default ollama storage location on linux"
date: 2025-01-04
tags: ollama systemd
---

I was downloading some large [ollama](https://ollama.com/){:target="_blank"} models and ran out of space on my linux machine.
I then realized that the default model storage location is `/usr/share`.
This path is mounted on my OS disk and there is not much storage there so I wanted the downloaded models to be stored elsewhere.
It turns out this is a [common request](https://github.com/ollama/ollama/issues/680){:target="_blank"}
and it can be done using an `OLLAMA_MODELS` environment variable.
There are some [docs](https://github.com/ollama/ollama/blob/main/docs/faq.md#how-do-i-configure-ollama-server){:target="_blank"} on how to do it, but it took me a few tries.

I have a 2TB data disk mounted at `/mnt/sdb`. I made an `ollama` directory there, gave it 755 permissions and made `ollama` the owner. 

``` bash
sudo chmod o+x /mnt/sdb
sudo mkdir /mnt/sdb/ollama
sudo chmod -R 755 /mnt/sdb/ollama
sudo chown -R ollama:ollama /mnt/sdb/ollama
```

I edited the ollama service config with,

``` bash
sudo systemctl edit ollama
```

and added the following lines (new lines are between the commented out lines)


```
### Editing /etc/systemd/system/ollama.service.d/override.conf
### Anything between here and the comment below will become the contents of the drop-in file

[Service]
Environment="OLLAMA_MODELS=/mnt/sdb/ollama"

### Edits below this comment will be discarded
```

After that I restarted things, 

``` bash
sudo systemctl daemon-reload 
sudo systemctl restart ollama
```

Now new `ollama run` or `ollama pull` commands will use the `/mnd/sdb/ollama` directory for storage.

Also very helpful, you can check out the ollama service logs using, 

``` bash
sudo journalctl -u ollama.service
```