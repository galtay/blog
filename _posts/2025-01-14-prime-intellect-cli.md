---
title: "[til] cloud gpus with the prime intellect cli"
date: 2025-01-14
tags: til cli prime-intellect cloud-gpu
---

Today I spun up a pair of A100 80GB gpus for $3.25 / hour using the
[prime-intellect-cli](https://github.com/PrimeIntellect-ai/prime-cli).
I wrote briefly about
[prime intellect]({% link _posts/2025-01-06-prime-intellect-intro.md %})
earlier and had spun up some GPUs using their website before, but their CLI is nice.

First I checked that I had public and private ssh keys from them.
These can be downloaded from the [profile page](https://app.primeintellect.ai/dashboard/profile).
I saved my keys in my `.ssh` directory, named them `prime-int-1-pub.pem` (public) and `prime-int-1.pem` (private), and gave them both the correct permissions,

```bash
chmod 400 ~/.ssh/prime-int-1-pub.pem
chmod 400 ~/.ssh/prime-int-1.pem
```

I also created and saved a prime-intellect API key from [here](https://app.primeintellect.ai/dashboard/tokens).
Next I created a quick uv application, added the prime-cli depedency, and activated the environment  with,

```bash
uv init prime-cli
cd prime-cli
uv add prime-cli
source .venv/bin/activate
```

To configure the prime-cli I did,

```bash
prime config set-api-key --api-key <prime-api-key>
prime config set-ssh-key-path --path ~/.ssh/prime-int-1.pem
```

Next I listed the available pods using,

```bash
prime availability list --gpu-type A100_80GB --gpu-count 2
```

I then requested that a pod be created using an `ID` from the previous command,

```bash
prime pods create --id 07bfe3 --image cuda_12_4_pytorch_2_5
```

If an image is not specified, the CLI will offer you a list of choices.
When I ran the command the possible choices were,

```
Available Images:
1. ubuntu_22_cuda_12
2. cuda_12_1_pytorch_2_2
3. cuda_11_8_pytorch_2_1
4. cuda_12_1_pytorch_2_3
5. cuda_12_1_pytorch_2_4
6. cuda_12_4_pytorch_2_4
7. cuda_12_4_pytorch_2_5
8. stable_diffusion
9. axolotl
10. bittensor
11. vllm_llama_8b
12. vllm_llama_70b
13. vllm_llama_405b
```

You can also setup custom images and templates but I haven't figured that out yet.
More info here,
* [templates page](https://app.primeintellect.ai/dashboard/templates)
* [custom templates youtube](https://www.youtube.com/watch?v=dk1HOXD6MvY)

Before the pod is created, the CLI will give you more details and ask if you want to proceed,

```
Pod Configuration Summary:
name: pipod
cloudId: 7de483e8-55e2-41de-a8d7-75916f4f9ca0_2
gpuType: A100_80GB
socket: SXM4
gpuCount: 2
diskSize: 120
vcpus: 58
memory: 200
image: cuda_12_4_pytorch_2_5
autoRestart: False
provider: tensordock
team: None

Do you want to create this pod? [Y/n]:
```

If you agree, then it will return a pod_id that you can use in command like,

```bash
prime pods status <pod_id>
```

```
      Pod Status: <pod_id>
┏━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ Property              ┃ Value                        ┃
┡━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ Status                │ ACTIVE                       │
│ Name                  │ aa                           │
│ Team                  │ Personal                     │
│ Provider              │ tensordock                   │
│ GPU                   │ A100_80GB x2                 │
│ Image                 │ cuda_12_4_pytorch_2_5        │
│ Created               │ 2025-01-14 04:49:01 UTC      │
│ IP                    │ 185.32.162.180               │
│ SSH                   │ root@185.32.162.180 -p 47904 │
│ Installation Status   │ FINISHED                     │
│ Installation Progress │ 100%                         │
└───────────────────────┴──────────────────────────────┘
```

If you want to see what pods are running or being provisioned you can use,

```bash
prime pods list
```

```
                                   Compute Pods (Total: 1)
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ ID                               ┃ Name ┃ GPU          ┃ Status ┃ Created                 ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ 9d270e166f76402598aefa758802daca │ aa   │ A100_80GB x2 │ ACTIVE │ 2025-01-14 04:49:01 UTC │
└──────────────────────────────────┴──────┴──────────────┴────────┴─────────────────────────┘
```

Once the pod Status is Active you can use the CLI to ssh in,

```bash
prime pods ssh <pod_id>
```

Once you are done using the pod you can terminate it with,

```bash
prime pods terminate <pod_id>
```

At the time of writing the cost per hour for (non spot) H100_80GB GPUs ranged from
$1.90 for 1 to
$17.94 for 8 to
$652.80 for a cluster with 256.

```
                                                             Available GPU Resources
┏━━━━━━━━┳━━━━━━━━━━━━━━━━━━┳━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━━┳━━━━━━━━━━┳━━━━━━━━━━━┳━━━━━━━━━━┳━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━━┓
┃ ID     ┃ GPU Type         ┃ GPUs ┃ Socket ┃ Provider      ┃ Location ┃ Stock     ┃ Price/Hr ┃ Memory (GB) ┃ Security     ┃ vCPUs   ┃ RAM (GB)  ┃
┡━━━━━━━━╇━━━━━━━━━━━━━━━━━━╇━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━━╇━━━━━━━━━━╇━━━━━━━━━━━╇━━━━━━━━━━╇━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━━┩
│ ccc676 │ H100_80GB        │ 1    │ PCIe   │ hyperstack    │ CA       │ Available │ $1.90    │ 80          │ secure_cloud │ 28      │ 180       │
│ 4258a6 │ H100_80GB        │ 1    │ PCIe   │ lambdalabs    │ US       │ Available │ $2.49    │ 80          │ secure_cloud │ 26      │ 200       │
│ 52e15c │ H100_80GB        │ 1    │ SXM5   │ datacrunch    │ FI       │ Available │ $2.65    │ 80          │ secure_cloud │ 30      │ 120       │
│ f287ef │ H100_80GB        │ 1    │ PCIe   │ runpod        │ N/A      │ Medium    │ $2.71    │ 80          │ secure_cloud │ 16-16   │ 188-188   │
│ 6bd7c8 │ H100_80GB        │ 1    │ PCIe   │ massedcompute │ US       │ Available │ $2.99    │ 80          │ secure_cloud │ 20      │ 128       │
│ 64d8f2 │ H100_80GB        │ 1    │ SXM5   │ lambdalabs    │ US       │ Available │ $3.29    │ 80          │ secure_cloud │ 26      │ 225       │
│ ecaddf │ H100_80GB        │ 2    │ PCIe   │ hyperstack    │ CA       │ Available │ $3.80    │ 160         │ secure_cloud │ 60      │ 360       │
│ 5f2993 │ H100_80GB        │ 2    │ PCIe   │ runpod        │ N/A      │ Medium    │ $5.40    │ 160         │ secure_cloud │ 64-64   │ 500-500   │
│ 0a5a30 │ H100_80GB        │ 4    │ PCIe   │ hyperstack    │ CA       │ Available │ $7.60    │ 320         │ secure_cloud │ 124     │ 720       │
│ 6972a6 │ H100_80GB (Spot) │ 8    │ SXM5   │ primecompute  │ US       │ Available │ $8.00    │ 640         │ secure_cloud │ 104     │ 752       │
│ e1226f │ H100_80GB        │ 3    │ PCIe   │ runpod        │ N/A      │ Medium    │ $8.09    │ 240         │ secure_cloud │ 96-96   │ 750-750   │
│ ac3e39 │ H100_80GB        │ 4    │ PCIe   │ runpod        │ N/A      │ Medium    │ $10.78   │ 320         │ secure_cloud │ 128-128 │ 1000-1000 │
│ 2d81ca │ H100_80GB        │ 5    │ PCIe   │ runpod        │ N/A      │ Medium    │ $13.47   │ 400         │ secure_cloud │ 160-160 │ 1250-1250 │
│ 484134 │ H100_80GB        │ 6    │ PCIe   │ runpod        │ N/A      │ Medium    │ $16.16   │ 480         │ secure_cloud │ 192-192 │ 1500-1500 │
│ 0797ed │ H100_80GB        │ 8    │ PCIe   │ latitude      │ US       │ Available │ $17.94   │ 640         │ secure_cloud │ 128     │ 1536      │
│ 70a39b │ H100_80GB        │ 7    │ PCIe   │ runpod        │ N/A      │ Medium    │ $18.85   │ 560         │ secure_cloud │ 224-224 │ 1750-1750 │
│ d576c2 │ H100_80GB        │ 8    │ SXM5   │ primecompute  │ US       │ Available │ $20.00   │ 640         │ secure_cloud │ 104     │ 752       │
│ b0acc4 │ H100_80GB        │ 8    │ SXM5   │ dc_roan       │ US       │ High      │ $20.40   │ 640         │ secure_cloud │ 104-104 │ 1024-1024 │
│ bc9236 │ H100_80GB        │ 8    │ SXM5   │ datacrunch    │ IS       │ Available │ $21.20   │ 640         │ secure_cloud │ 176     │ 1480      │
│ bfa6b3 │ H100_80GB        │ 8    │ PCIe   │ runpod        │ N/A      │ Low       │ $21.54   │ 640         │ secure_cloud │ 256-256 │ 2000-2000 │
│ 6b5e65 │ H100_80GB        │ 8    │ SXM5   │ lambdalabs    │ US       │ Available │ $23.92   │ 640         │ secure_cloud │ 208     │ 1800      │
│ 7372ff │ H100_80GB        │ 16   │ SXM5   │ dc_roan       │ US       │ Medium    │ $40.80   │ 1280        │ secure_cloud │ 104     │ 1024      │
│ 6606ce │ H100_80GB        │ 16   │ SXM5   │ dc_roan       │ US       │ Medium    │ $52.80   │ 1280        │ secure_cloud │ 104     │ 1024      │
│ f9e8c1 │ H100_80GB        │ 24   │ SXM5   │ dc_roan       │ US       │ Medium    │ $61.20   │ 1920        │ secure_cloud │ 104     │ 1024      │
│ 33df56 │ H100_80GB        │ 24   │ SXM5   │ dc_roan       │ US       │ Medium    │ $79.20   │ 1920        │ secure_cloud │ 104     │ 1024      │
│ 022ccf │ H100_80GB        │ 32   │ SXM5   │ dc_roan       │ US       │ Medium    │ $81.60   │ 2560        │ secure_cloud │ 104     │ 1024      │
│ 026256 │ H100_80GB        │ 40   │ SXM5   │ dc_roan       │ US       │ Low       │ $102.00  │ 3200        │ secure_cloud │ 104     │ 1024      │
│ e13ae3 │ H100_80GB        │ 32   │ SXM5   │ dc_roan       │ US       │ Low       │ $105.60  │ 2560        │ secure_cloud │ 104     │ 1024      │
│ d47067 │ H100_80GB        │ 48   │ SXM5   │ dc_roan       │ US       │ Low       │ $122.40  │ 3840        │ secure_cloud │ 104     │ 1024      │
│ d7ec2f │ H100_80GB        │ 40   │ SXM5   │ dc_roan       │ US       │ Low       │ $132.00  │ 3200        │ secure_cloud │ 104     │ 1024      │
│ ff57ce │ H100_80GB        │ 56   │ SXM5   │ dc_roan       │ US       │ Low       │ $142.80  │ 4480        │ secure_cloud │ 104     │ 1024      │
│ 11f0d0 │ H100_80GB        │ 48   │ SXM5   │ dc_roan       │ US       │ Low       │ $158.40  │ 3840        │ secure_cloud │ 104     │ 1024      │
│ b82d06 │ H100_80GB        │ 64   │ SXM5   │ dc_roan       │ US       │ Low       │ $163.20  │ 5120        │ secure_cloud │ 104     │ 1024      │
│ de4da4 │ H100_80GB        │ 72   │ SXM5   │ dc_roan       │ US       │ Low       │ $183.60  │ 5760        │ secure_cloud │ 104     │ 1024      │
│ e66cfa │ H100_80GB        │ 56   │ SXM5   │ dc_roan       │ US       │ Low       │ $184.80  │ 4480        │ secure_cloud │ 104     │ 1024      │
│ 45f246 │ H100_80GB        │ 80   │ SXM5   │ dc_roan       │ US       │ Low       │ $204.00  │ 6400        │ secure_cloud │ 104     │ 1024      │
│ 7e576e │ H100_80GB        │ 64   │ SXM5   │ dc_roan       │ US       │ Low       │ $211.20  │ 5120        │ secure_cloud │ 104     │ 1024      │
│ e1d178 │ H100_80GB        │ 88   │ SXM5   │ dc_roan       │ US       │ Low       │ $224.40  │ 7040        │ secure_cloud │ 104     │ 1024      │
│ 0b58f1 │ H100_80GB        │ 72   │ SXM5   │ dc_roan       │ US       │ Low       │ $237.60  │ 5760        │ secure_cloud │ 104     │ 1024      │
│ a6dd29 │ H100_80GB        │ 96   │ SXM5   │ dc_roan       │ US       │ Low       │ $244.80  │ 7680        │ secure_cloud │ 104     │ 1024      │
│ 26b8d5 │ H100_80GB        │ 80   │ SXM5   │ dc_roan       │ US       │ Low       │ $264.00  │ 6400        │ secure_cloud │ 104     │ 1024      │
│ 110caa │ H100_80GB        │ 104  │ SXM5   │ dc_roan       │ US       │ Low       │ $265.20  │ 8320        │ secure_cloud │ 104     │ 1024      │
│ 17e45a │ H100_80GB        │ 88   │ SXM5   │ dc_roan       │ US       │ Low       │ $290.40  │ 7040        │ secure_cloud │ 104     │ 1024      │
│ 1c625a │ H100_80GB        │ 120  │ SXM5   │ dc_roan       │ US       │ Low       │ $306.00  │ 9600        │ secure_cloud │ 104     │ 1024      │
│ 2524b0 │ H100_80GB        │ 96   │ SXM5   │ dc_roan       │ US       │ Low       │ $316.80  │ 7680        │ secure_cloud │ 104     │ 1024      │
│ 5d32e1 │ H100_80GB        │ 128  │ SXM5   │ dc_roan       │ US       │ Low       │ $326.40  │ 10240       │ secure_cloud │ 104     │ 1024      │
│ c1ab3f │ H100_80GB        │ 104  │ SXM5   │ dc_roan       │ US       │ Low       │ $343.20  │ 8320        │ secure_cloud │ 104     │ 1024      │
│ e122a3 │ H100_80GB        │ 120  │ SXM5   │ dc_roan       │ US       │ Low       │ $396.00  │ 9600        │ secure_cloud │ 104     │ 1024      │
│ 65a1e0 │ H100_80GB        │ 128  │ SXM5   │ dc_roan       │ US       │ Low       │ $422.40  │ 10240       │ secure_cloud │ 104     │ 1024      │
│ b1527b │ H100_80GB        │ 192  │ SXM5   │ dc_roan       │ US       │ Low       │ $489.60  │ 15360       │ secure_cloud │ 104     │ 1024      │
│ 0f7410 │ H100_80GB        │ 192  │ SXM5   │ dc_roan       │ US       │ Low       │ $633.60  │ 15360       │ secure_cloud │ 104     │ 1024      │
│ 7a3b71 │ H100_80GB        │ 256  │ SXM5   │ dc_roan       │ US       │ Low       │ $652.80  │ 20480       │ secure_cloud │ 104     │ 1024      │
└────────┴──────────────────┴──────┴────────┴───────────────┴──────────┴───────────┴──────────┴─────────────┴──────────────┴─────────┴───────────┘
```


