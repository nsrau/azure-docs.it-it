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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993875"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurare DPDK in una macchina virtuale Linux

Data Plane Development Kit (DPDK) in Azure offre un framework più veloce per l'elaborazione di pacchetti dello spazio utente per applicazioni che richiedono prestazioni elevate. Questo framework ignora lo stack di rete del kernel della macchina virtuale.

La tipica elaborazione dei pacchetti mediante lo stack di rete del kernel è gestita da interrupt. Quando l'interfaccia di rete riceve i pacchetti in entrata, un interrupt del kernel elabora il pacchetto e il cambio di contesto dallo spazio del kernel allo spazio utente. DPDK elimina il cambio di contesto e il metodo gestito da interrupt privilegiando un'implementazione dello spazio utente tramite driver in modalità poll per l'elaborazione rapida dei pacchetti.

DPDK è costituito da set di librerie dello spazio utente che consentono l'accesso a risorse di livello inferiore. Queste risorse possono includere hardware, core logici, gestione della memoria e driver in modalità poll per le schede dell'interfaccia di rete.

DPDK può essere eseguito nelle macchine virtuali di Azure che supportano più distribuzioni del sistema operativo. DPDK fornisce una differenziazione delle prestazioni chiave nel gestire le implementazioni di virtualizzazione delle funzioni di rete. Queste implementazioni possono presentarsi sotto forma di appliance virtuali di rete (NVA), ad esempio router virtuali, firewall, VPN, servizi di bilanciamento del carico, framework Evolved Packet Core e applicazioni di tipo Denial of Service (DoS).

## <a name="benefit"></a>Vantaggi

**Più pacchetti al secondo (PPS)**: Ignorando il kernel e controllo di acquisizione di pacchetti nello spazio utente riduce il conteggio di cicli, eliminando i cambi di contesto. Viene anche migliorata la frequenza dei pacchetti elaborati al secondo nelle macchine virtuali Linux di Azure.


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

Per qualsiasi versione del kernel Linux non elencata, vedere [Patches for building an Azure-tuned Linux kernel](https://github.com/microsoft/azure-linux-kernel) (Patch per la compilazione di un kernel Linux ottimizzato per Azure). Per altre informazioni, è anche possibile contattare [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com). 

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

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurare l'ambiente delle macchine virtuali (una sola volta)

1. [Scaricare il DPDK più recente](https://core.dpdk.org/download). Per Azure è richiesta la versione 18.02 o superiore.
2. Generare la configurazione predefinita con `make config T=x86_64-native-linuxapp-gcc`.
3. Abilitare Mellanox PMDs nel file di configurazione generato con `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Eseguire la compilazione con `make`.
5. Installare con `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Configurare l'ambiente di runtime

Dopo il riavvio, eseguire i comandi seguenti una sola volta:

1. Hugepage

   * Configurare le hugepage eseguendo il comando seguente, una volta per tutte le numanode:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Creare una directory per il montaggio con `mkdir /mnt/huge`.
   * Montare le hugepage con `mount -t hugetlbfs nodev /mnt/huge`.
   * Verificare che le hugepage siano riservate con `grep Huge /proc/meminfo`.

     > [!NOTE]
     > È possibile modificare il file di grub in modo che le hugepage vengano riservate in fase di avvio seguendo le [istruzioni](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) per DPDK. Le istruzioni sono disponibili in fondo alla pagina. Quando si usa una macchina virtuale Linux di Azure, modificare invece i file in **/etc/config/grub.d** per riservare le hugepage dopo i riavvii.

2. Indirizzi MAC e IP: Usare `ifconfig –a` per visualizzare l'indirizzo IP e MAC delle interfacce di rete. Le interfacce di rete *VF* e *NETVSC* hanno lo stesso indirizzo MAC, ma solo *NETVSC* ha un indirizzo IP. Le interfacce VF sono in esecuzione come subordinate delle interfacce NETVSC.

3. Indirizzi PCI

   * Eseguire il comando `ethtool -i <vf interface name>` per visualizzare l'indirizzo PCI da usare per *VF*.
   * Se per *eth0* è abilitata la funzionalità di rete accelerata, verificare che testpmd non acquisisca per errore il dispositivo PCI VF per *eth0*. Se l'applicazione DPDK ha accidentalmente acquisito l'interfaccia di rete di gestione che causa la perdita della connessione SSH, usare la console seriale per terminare l'applicazione DPDK. È anche possibile usare la console seriale per arrestare o avviare la macchina virtuale.

4. Caricare *ibuverbs* a ogni riavvio con `modprobe -a ib_uverbs`. Solo per SLES 15, caricare anche *mlx4_ib* con `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Operatore alternativo PMD

Le applicazioni DPDK devono eseguite tramite l'operatore alternativo PMD esposto in Azure. Se l'applicazione viene eseguita direttamente tramite PMD VF, non riceverà **tutti** i pacchetti destinati alla VM, perché alcuni verranno visualizzati tramite l'interfaccia sintetica. 

L'esecuzione di un'applicazione DPDK tramite l'operatore alternativo PMD garantisce che tale applicazione riceverà tutti i pacchetti ad essa destinati. Assicura inoltre che l'applicazione rimarrà in esecuzione in modalità DPDK, anche se VF viene revocato quando l'host viene servito. Per altre informazioni sull'operatore alternativo PMD, vedere [Fail-safe poll mode driver library](https://doc.dpdk.org/guides/nics/fail_safe.html) (Libreria di driver in modalità poll alternativa).

## <a name="run-testpmd"></a>Eseguire testpmd

Per eseguire testpmd in modalità root, usare `sudo` prima del comando *testpmd*.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Verifica dell'integrità, inizializzazione dell'adapter di tipo operatore alternativo

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

   Se testpmd viene eseguito con più di due schede di interfaccia di rete, l'argomento `--vdev` segue questo modello: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Dopo l'avvio, eseguire `show port info all` per controllare le informazioni relative alle porte. Verranno visualizzate una o due porte DPDK net_failsafe (non *net_mlx4*).
4.  Usare `start <port> /stop <port>` per avviare il traffico.

I comandi precedenti avviano *testpmd* in modalità interattiva, consigliata per provare i comandi testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Ricevitore singolo mittente/single

I seguenti comandi stampano periodicamente i pacchetti per statistiche al secondo:

1. Sul lato TX, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Quando si eseguono i comandi precedenti in una macchina virtuale, modificare *IP_SRC_ADDR* e *IP_DST_ADDR* in `app/test-pmd/txonly.c` per la corrispondenza all'indirizzo IP effettivo delle macchine virtuali prima della compilazione. In caso contrario, i pacchetti vengono eliminati prima di raggiungere il destinatario.

### <a name="advanced-single-sendersingle-forwarder"></a>Avanzate: Server d'inoltro singolo mittente/single
I seguenti comandi stampano periodicamente i pacchetti per statistiche al secondo:

1. Sul lato TX, eseguire il comando seguente:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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

Quando si eseguono i comandi precedenti in una macchina virtuale, modificare *IP_SRC_ADDR* e *IP_DST_ADDR* in `app/test-pmd/txonly.c` per la corrispondenza all'indirizzo IP effettivo delle macchine virtuali prima della compilazione. In caso contrario, i pacchetti vengono eliminati prima di raggiungere il server d'inoltro. Non sarà possibile avere una terza macchina virtuale che riceve il traffico inoltrato, perché il server d'inoltro di *testpmd* non modifica gli indirizzi di livello 3, a meno che non vengano apportate alcune modifiche al codice.

## <a name="references"></a>Riferimenti

* [Opzioni EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandi testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
