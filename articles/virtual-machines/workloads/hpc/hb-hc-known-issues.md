---
title: Risoluzione dei problemi noti con le VM HPC e GPU-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi noti relativi alle dimensioni di VM HPC e GPU in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 42a27092a87488e39d1195dba5fb64173cf52af7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004205"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemi noti delle VM serie H e serie N

Questo articolo descrive i problemi e le soluzioni più comuni quando si [usano le VM](../../sizes-hpc.md) HPC e GPU serie [N e N](../../sizes-gpu.md) .

## <a name="infiniband-driver-installation-on-n-series-vms"></a>Installazione del driver InfiniBand nelle macchine virtuali serie N

NC24r_v3 e ND40r_v2 sono abilitati SR-IOV mentre NC24r e NC24r_v2 non sono abilitati SR-IOV. Di [seguito](../../sizes-hpc.md#rdma-capable-instances)sono riportati alcuni dettagli sulla biforcazione.
È possibile configurare InfiniBand (IB) nelle dimensioni delle VM abilitate per SR-IOV con i driver OFED, mentre le dimensioni delle macchine virtuali non SR-IOV richiedono driver ND. Questo supporto IB è disponibile in modo appropriato in [CentOS-HPC VMIs](configure.md). Per Ubuntu, vedere l' [istruzione qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) per installare i driver OFED e ND come descritto in [docs](enable-infiniband.md#vm-images-with-infiniband-drivers).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicare MAC con cloud-init con Ubuntu sulle VM serie H e serie N

Si è verificato un problema noto con cloud-init sulle immagini di macchina virtuale Ubuntu durante il tentativo di visualizzare l'interfaccia IB. Questo problema può verificarsi al riavvio della macchina virtuale o quando si tenta di creare un'immagine di macchina virtuale dopo la generalizzazione. Nei log di avvio della macchina virtuale potrebbe essere visualizzato un errore simile al seguente: "avvio del servizio di rete... RuntimeError: trovato Mac duplicato. ' eth1' è Ib0' hanno Mac ".

Questo "MAC duplicato con cloud-init in Ubuntu" è un problema noto. La soluzione alternativa è la seguente:
1) Distribuire l'immagine di macchina virtuale del Marketplace (Ubuntu 18,04)
2) Installare i pacchetti software necessari per abilitare IB ([istruzioni qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Modificare waagent. conf per modificare EnableRDMA = y
4) Disabilitare la rete in cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Modificare il file di configurazione di rete di Netplan generato da cloud-init per rimuovere il MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
        ethernets:
        eth0:
            dhcp4: true
        version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>DRAM su serie HB

Al momento, le VM serie HB possono esporre solo 228 GB di RAM alle macchine virtuali guest. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure per impedire che le pagine vengano assegnate al DRAM locale di AMD CCX (domini NUMA) riservato per la VM guest.

## <a name="accelerated-networking"></a>Rete accelerata

In questo momento la rete accelerata di Azure nelle VM HPC e GPU abilitate per IB non è abilitata. I clienti riceveranno una notifica quando questa funzionalità è supportata.

## <a name="qp0-access-restriction"></a>Restrizione di accesso qp0

Per impedire l'accesso hardware di basso livello che può comportare vulnerabilità di sicurezza, la coppia di code 0 non è accessibile alle macchine virtuali guest. Questa operazione influisce solo sulle azioni generalmente associate all'amministrazione della scheda di interfaccia di rete ConnectX-5 e sull'esecuzione di una diagnostica InfiniBand come ibdiagnet, ma non sulle applicazioni dell'utente finale.

## <a name="gss-proxy"></a>Proxy GSS

Il proxy GSS presenta un bug noto in CentOS/RHEL 7,5 che può manifestarsi come una significativa riduzione delle prestazioni e della velocità di risposta quando viene usato con NFS. Questo problema può essere risolto con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Pulizia della cache

Nei sistemi HPC è spesso utile pulire la memoria al termine di un processo prima che all'utente successivo venga assegnato lo stesso nodo. Dopo l'esecuzione di applicazioni in Linux è possibile che la memoria disponibile si riduca mentre la memoria del buffer aumenta, nonostante non sia in esecuzione alcuna applicazione.

![Screenshot del prompt dei comandi prima della pulizia](./media/known-issues/cache-cleaning-1.png)

Con `numactl -H` verrà visualizzato il NUMAnode (probabilmente All) in cui è memorizzata la memoria. In Linux, gli utenti possono pulire le cache in tre modi per restituire la memoria memorizzata nel buffer o memorizzata nella cache su "Free". È necessario avere le autorizzazioni radice o sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot del prompt dei comandi dopo la pulizia](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi del kernel

È possibile ignorare i messaggi di avviso del kernel seguenti quando si avvia una macchina virtuale della serie HB in Linux. Ciò è dovuto a un limite noto dell'hypervisor di Azure che verrà risolto nel corso del tempo.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Passaggi successivi

- Esaminare la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md) per informazioni su come configurare in modo ottimale i carichi di lavoro ai fini delle prestazioni e della scalabilità.
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
