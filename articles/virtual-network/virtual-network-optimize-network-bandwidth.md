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
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: b604782f917584d1ecec432c20de75f427176ed1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Ottimizzare la velocità effettiva di rete per le macchine virtuali di Azure

Le macchine virtuali di Azure hanno impostazioni di rete predefinite che possono essere ottimizzate ulteriormente per una migliore velocità effettiva di rete. Questo articolo illustra come ottimizzare la velocità effettiva di rete per macchine virtuali di Microsoft Azure Windows e Linux, incluse le distribuzioni principali, ad esempio Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>Macchina virtuale Windows

Se la macchina virtuale di Windows supporta la funzione [Rete accelerata](create-vm-accelerated-networking-powershell.md), la velocità effettiva è configurata in modo ottimale se questa funzione è abilitata. Per tutte le altre macchine virtuali di Windows, tramite Receive-Side Scaling (RSS) esse possono raggiungere una velocità effettiva massima superiore rispetto a una VM senza RSS. È possibile disabilitare RSS per impostazione predefinita in una macchina virtuale Windows. Per determinare se RSS è abilitato e abilitarlo se è disabilitato, eseguire la procedura seguente:

1. Con il comando `Get-NetAdapterRss` di PowerShell verificare se RSS è abilitato per una scheda di rete. Nell'output di esempio seguente restituito da `Get-NetAdapterRss` RSS non è abilitato.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Per abilitare RSS, immettere il comando seguente:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Il comando precedente non restituisce alcun output. Il comando ha modificato le impostazioni della scheda di interfaccia di rete, provocando la perdita di connettività temporanea per circa un minuto. Durante la perdita di connettività viene visualizzata una finestra di dialogo di riconnessione. La connettività viene in genere ripristinata dopo il terzo tentativo.
3. Verificare che RSS sia abilitato nella macchina virtuale immettendo di nuovo il comando `Get-NetAdapterRss`. Se l'esito è positivo, viene restituito l'output di esempio seguente:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS è sempre abilitato per impostazione predefinita nella macchina virtuale Linux di Azure. I kernel Linux rilasciati a partire da ottobre 2017 includono opzioni di ottimizzazione di rete che consentono a una macchina virtuale Linux di ottenere una velocità effettiva di rete superiore.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu per nuove distribuzioni

Il kernel Azure Ubuntu offre le migliori prestazioni di rete in Azure ed è il kernel predefinito dal 21 settembre 2017. Per ottenere il kernel, installare prima la versione supportata più recente, 16.04-LTS, come illustrato di seguito:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Al termine della creazione, immettere i comandi seguenti per ottenere gli aggiornamenti più recenti. Questi passaggi funzionano anche per le VM che attualmente eseguono il kernel Azure Ubuntu.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Il set di comandi facoltativi seguenti può essere utile per le distribuzioni di Ubuntu esistenti che hanno già il kernel Azure, ma per cui si sono verificati errori durante gli aggiornamenti.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Aggiornamento del kernel Azure Ubuntu per le macchine virtuali esistenti

È possibile ottenere prestazioni significative per la velocità effettiva effettuando l'aggiornamento al kernel Azure Linux proposto. Per verificare se si ha già questo kernel, controllare la versione del kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se la macchina virtuale non ha il kernel di Azure, il numero di versione inizia in genere con "4.4". Se la macchina virtuale non ha il kernel di Azure, eseguire i comandi seguenti come radice:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Per ottenere le ottimizzazioni più recenti, l'ideale è creare una VM con la versione supportata più recente specificando i parametri seguenti:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Le VM nuove ed esistenti possono trarre vantaggio dall'installazione della versione più recente di Linux Integration Services (LIS). L'ottimizzazione della velocità effettiva è presente in LIS a partire dalla versione 4.2.2-2, anche se le versioni successive contengono ulteriori miglioramenti. Per installare la versione di LIS più recente, immettere i comandi seguenti:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Per ottenere le ottimizzazioni, l'ideale è creare una VM con la versione supportata più recente specificando i parametri seguenti:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Le VM nuove ed esistenti possono trarre vantaggio dall'installazione della versione più recente di Linux Integration Services (LIS). L'ottimizzazione della velocità effettiva è disponibile in LIS a partire dalla versione 4.2. Immettere i comandi seguenti per scaricare e installare LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Per altre informazioni su Linux Integration Services versione 4.2 per Hyper-V, vedere la [pagina di download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passaggi successivi
* Verificare i risultati dell'ottimizzazione dello scenario in [Bandwidth/Throughput testing Azure VM](virtual-network-bandwidth-testing.md) (Test della larghezza di banda/velocità effettiva della macchina virtuale di Azure).
* Sono disponibili informazioni su come [allocare la larghezza di banda alle macchine virtuali] (virtual-machine-network-throughput.md).
* Altre informazioni sono disponibili nell'articolo [Domande frequenti sulla rete virtuale di Azure](virtual-networks-faq.md).
