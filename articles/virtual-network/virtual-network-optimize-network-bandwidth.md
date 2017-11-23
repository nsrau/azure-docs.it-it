---
title: "Ottimizzare la velocità effettiva di rete della macchina virtuale | Documentazione Microsoft"
description: "Informazioni su come ottimizzare la velocità effettiva di rete della macchina virtuale di Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: d77440fe62bbd0e720e5ae60b15574dacc4180c0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Ottimizzare la velocità effettiva di rete per le macchine virtuali di Azure

Le macchine virtuali di Azure hanno impostazioni di rete predefinite che possono essere ottimizzate ulteriormente per una migliore velocità effettiva di rete. Questo articolo illustra come ottimizzare la velocità effettiva di rete per macchine virtuali di Microsoft Azure Windows e Linux, incluse le distribuzioni principali, ad esempio Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>Macchina virtuale Windows

Se la macchina virtuale di Windows è supportata da una [Rete accelerata](virtual-network-create-vm-accelerated-networking.md), abilitare tale funzionalità costituisce la configurazione ottimale per la velocità effettiva. Per tutte le altre macchine virtuali di Windows, tramite Receive-Side Scaling (RSS) esse possono raggiungere una velocità effettiva massima superiore rispetto a una VM senza RSS. È possibile disabilitare RSS per impostazione predefinita in una macchina virtuale Windows. Completare la procedura seguente per determinare se RSS è abilitato e abilitarlo se è disabilitato.

1. Immettere il comando `Get-NetAdapterRss` di PowerShell per verificare se RSS è abilitato per una scheda di rete. Nell'output di esempio seguente restituito da `Get-NetAdapterRss` RSS non è abilitato.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Immettere il comando seguente per abilitare RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Il comando precedente non restituisce alcun output. Il comando ha modificato le impostazioni della scheda di interfaccia di rete, provocando la perdita di connettività temporanea per circa un minuto. Durante la perdita di connettività viene visualizzata una finestra di dialogo di riconnessione. La connettività viene in genere ripristinata dopo il terzo tentativo.
3. Verificare che RSS sia abilitato nella macchina virtuale immettendo di nuovo il comando `Get-NetAdapterRss`. Se l'esito è positivo, viene restituito l'output di esempio seguente:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS è sempre abilitato per impostazione predefinita nella macchina virtuale Linux di Azure. I kernel Linux rilasciati a partire da ottobre 2017 includono opzioni di ottimizzazione di rete che consentono a una macchina virtuale Linux di ottenere una velocità effettiva di rete superiore.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu per nuove distribuzioni

Per ottenere l'ottimizzazione, installare prima di tutto la versione supportata più recente, 16.04-LTS, in questo modo:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Al termine dell'aggiornamento, immettere i comandi seguenti per ottenere il kernel più recente:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Comando facoltativo:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Aggiornamento del kernel Azure Ubuntu per le macchine virtuali esistenti

È possibile ottenere prestazioni significative per la velocità effettiva effettuando l'aggiornamento al kernel Azure Linux proposto. Per verificare se si ha già questo kernel, controllare la versione del kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Eseguire quindi questi comandi come radice.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Per ottenere le ottimizzazioni più recenti, effettuare prima di tutto l'aggiornamento alla versione supportata più recente, in questo modo:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Al termine dell'aggiornamento, installare la versione più recente di Linux Integration Services (LIS).
L'ottimizzazione della velocità effettiva è presente in LIS a partire dalla versione 4.2.2-2, anche se le versioni successive contengono ulteriori miglioramenti. Per installare la versione di LIS più recente, immettere i comandi seguenti:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Per ottenere l'ottimizzazione, effettuare prima di tutto l'aggiornamento alla versione supportata più recente, in questo modo:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Al termine dell'aggiornamento, installare la versione più recente di Linux Integration Services (LIS).
L'ottimizzazione della velocità effettiva è disponibile in LIS a partire dalla versione 4.2. Immettere i comandi seguenti per scaricare e installare LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Per altre informazioni su Linux Integration Services versione 4.2 per Hyper-V, vedere la [pagina di download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passaggi successivi
* Dopo avere ottimizzato la VM, verificare il risultato con [Test della larghezza di banda/velocità effettiva della VM di Azure](virtual-network-bandwidth-testing.md) per lo scenario.
* Altre informazioni sono disponibili nell'articolo [Domande frequenti sulla rete virtuale di Azure](virtual-networks-faq.md).
