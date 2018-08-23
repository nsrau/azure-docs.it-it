---
title: DPDK in una macchina virtuale Linux di Azure | Microsoft Docs
description: Informazioni sulle modalità di configurazione di DPDK in una macchina virtuale Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 205a1e399eadd268ffaa390a7ebb4397fda9feff
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444654"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Configurare DPDK in una macchina virtuale Linux

Data Plane Development Kit (DPDK) in Azure offre un framework di elaborazione dei pacchetti dello spazio utente più rapido per le applicazioni con utilizzo intensivo delle prestazioni che ignorano lo stack di rete della macchina virtuale del kernel.

La tipica elaborazione del pacchetto mediante lo stack di rete kernel è gestita da interrupt. Ogni volta che l'interfaccia di rete riceve i pacchetti in entrata, un interrupt del kernel elabora il pacchetto e il cambio di contesto dallo spazio kernel allo spazio utente. DPDK elimina il cambio di contesto e il metodo di gestito da interrupt a favore di un'implementazione dello spazio utente tramite i driver in modalità sondaggio per l'elaborazione rapida dei pacchetti.

DPDK è costituito da set di librerie dello spazio utente che forniscono accesso alle risorse di basso livello, ad esempio hardware, core logici, gestione della memoria e driver in modalità sondaggio per schede di interfaccia di rete.

DPDK può essere eseguito nelle macchine virtuali di Azure supportando più distribuzioni del sistema operativo. DPDK fornisce una differenziazione chiave delle performance nel gestire le implementazioni di virtualizzazione della funzione di rete, sotto forma di appliance virtuali di rete (NVA), quali router virtuali, firewall, VPN, servizio di bilanciamento del carico, pacchetti core evoluti e applicazioni di tipo denial of service (applicazioni di DDoS).

## <a name="benefit"></a>Vantaggi

**Più pacchetti al secondo (PPS)**: ignorando il kernel e acquisendo il controllo di pacchetti nello spazio utente viene ridotto il numero di cicli tramte l'eliminazione del cambio di contesto e viene migliorata la frequenza dei pacchetti elaborati al secondo nelle macchine virtuali Linux di Azure.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Sono supportate le distribuzioni seguenti della raccolta di Azure:

| Sistema operativo Linux     | Versione del kernel        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Supporto per kernel personalizzati**

Fare riferimento alle [Patch per la compilazione di un kernel Linux ottimizzato per Azure](https://github.com/microsoft/azure-linux-kernel) per qualsiasi versione del kernel Linux non elencata o per altre informazioni, contattare [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Supporto di area

Tutte le aree di Azure supportano DPDK.

## <a name="prerequisites"></a>Prerequisiti

La rete accelerata deve essere abilitata su una macchina virtuale Linux. La macchina virtuale deve avere almeno due interfacce di rete, con un'interfaccia per la gestione. Informazioni sulle modalità per [Creare una macchina virtuale Linux con rete accelerata abilitata](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Installare le dipendenze DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Kernel di Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Kernel predefinito**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>Configurare l'ambiente per le macchine virtuali (una volta)

1. [Scaricare il DPDK più recente](https://core.dpdk.org/download). Per Azure è richiesta la versione 18.02 o superiore.
2. Compilare innanzitutto la configurazione predefinita con `make config T=x86_64-native-linuxapp-gcc`.
3. Abilitare Mellanox PMDs nel file di configurazione generato con `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Eseguire la compilazione con `make`.
5. Installare con `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Configurare l'ambiente di runtime

Eseguire i comandi seguenti una volta, dopo il riavvio:

1. Hugepage

   * Configurare le hugepage eseguendo il comando seguente, una volta per tutte le numanode:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Creare una directory per il montaggio con `mkdir /mnt/huge`.
   *  Montare le hugepage con `mount -t hugetlbfs nodev /mnt/huge`.
   *  Verificare che le hugepage siano riservate con `grep Huge /proc/meminfo`.

     > [!NOTE]
     > È possibile modificare il file di grub in modo che le hugepage siano riservate in fase di avvio seguendo le [istruzioni](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) per DPDK. Le istruzioni si trovano nella parte inferiore della pagina. Quando è in esecuzione una macchina virtuale Linux di Azure, modificare invece i file in /etc/config/grub.d per riservare le hugepage dopo i riavvii.

2. Indirizzi MAC e IP: usare `ifconfig –a` per visualizzare l'indirizzo IP e MAC delle interfacce di rete. Le interfacce di rete *VF* e *NETVSC* hanno lo stesso indirizzo MAC, ma solo *NETVSC* ha un indirizzo IP. Le interfacce VF vengono eseguite come interfacce slave delle interfacce NETVSC.

3. Indirizzi PCI

   * Determinare l'indirizzo PCI da usare per *VF* con `ethtool -i <vf interface name>`.
   * Verificare che testpmd non porti accidentalmente al dispositivo VF pci per *eth0*, se *eth0* ha funzionalità di rete accelerate abilitate. Se l'applicazione DPDK ha accidentalmente preso il posto dell'interfaccia di rete di gestione e causa la perdita della connessione SSH, usare la console seriale per terminare l'applicazione DPDK o per arrestare o avviare la macchina virtuale.

4. Caricare *ibuverbs* a ogni riavvio con `modprobe -a ib_uverbs`. Solo per SLES 15, caricare anche *mlx4_ib* con `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Operatore alternativo PMD

Le applicazioni DPDK devono eseguite tramite l'operatore alternativo PMD esposto in Azure. Se l'applicazione viene eseguita direttamente tramite il PMD VF, non riceverà **tutti** i pacchetti destinati alla macchina virtuale, poiché verranno visualizzati alcuni pacchetti tramite l'interfaccia sintetica. L'esecuzione tramite l'operatore alternativo PMD garantisce che l'applicazione riceva tutti i pacchetti destinati a esso e garantisce anche che l'applicazione continuerà l'esecuzione in modalità DPDK, anche se VF viene revocato quando l'host viene elaborato. Per altre informazioni sull'operatore alternativo PMD, consultare [Libreria del driver in modalità sondaggio alternativa](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Eseguire testpmd

Usare `sudo` prima del comando *testpmd* da eseguire in modalità principale.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: verifica dell'integrità, inizializzazione dell'adattatore di tipo operatore alternativo

1. Eseguire i comandi seguenti per avviare un'applicazione testpmd a porta singola:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Eseguire i comandi seguenti per avviare un'applicazione testpmd dual port:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se in esecuzione con più di 2 interfacce di rete, l'argomento `--vdev` segue questo modello: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Una volta avviato, eseguire `show port info all` per controllare le informazioni della porta. Verranno visualizzate una o due porte DPDK net_failsafe (non *net_mlx4*).
4.  Usare `start <port> /stop <port>` per avviare il traffico.

I comandi precedenti avviano *testpmd* in modalità interattiva, consigliata per provare alcuni comandi testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Basic: ricevitore singolo/mittente singolo

I seguenti comandi stampano periodicamente i pacchetti per statistiche al secondo:

1. Sul lato TX, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Sul lato RX, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Quando si eseguono i comandi precedenti in una macchina virtuale, modificare *IP_SRC_ADDR* e *IP_DST_ADDR* in `app/test-pmd/txonly.c` per la corrispondenza all'indirizzo IP effettivo delle macchine virtuali prima della compilazione. In caso contrario, i pacchetti vengono eliminati prima di raggiungere il destinatario.

### <a name="advanced-single-sendersingle-forwarder"></a>Avanzata: mittente singolo/server d'inoltro singolo
I seguenti comandi stampano periodicamente i pacchetti per statistiche al secondo:

1. Sul lato TX, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Sul lato FWD, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Quando si eseguono i comandi precedenti in una macchina virtuale, modificare *IP_SRC_ADDR* e *IP_DST_ADDR* in `app/test-pmd/txonly.c` per la corrispondenza all'indirizzo IP effettivo delle macchine virtuali prima della compilazione. In caso contrario, i pacchetti vengono eliminati prima di raggiungere il server d'inoltro. Non sarà possibile disporre di una terza macchina virtuale che riceve il traffico inoltrato, perché il server d'inoltro *testpmd* non modifica gli indirizzi di livello 3, a meno che non vengano apportate alcune modifiche al codice.

## <a name="references"></a>Riferimenti

* [Opzioni EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandi testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
