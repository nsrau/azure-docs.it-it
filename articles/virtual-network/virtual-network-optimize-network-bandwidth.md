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
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Ottimizzare la velocità effettiva di rete per le macchine virtuali di Azure

Le macchine virtuali di Azure hanno impostazioni di rete predefinite che possono essere ottimizzate ulteriormente per una migliore velocità effettiva di rete. Questo articolo illustra come ottimizzare la velocità effettiva di rete per macchine virtuali Windows e Linux di Microsoft Azure, incluse le distribuzioni principali, ad esempio Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>Macchina virtuale Windows

Una macchina virtuale che usa Receive-Side Scaling (RSS) può raggiungere una velocità effettiva massima superiore rispetto a una VM senza RSS. È possibile disabilitare RSS per impostazione predefinita in una macchina virtuale Windows. Completare la procedura seguente per determinare se RSS è abilitato e abilitarlo se è disabilitato.

1. Immettere il comando `Get-NetAdapterRss` di PowerShell per verificare se RSS è abilitato per una scheda di rete. Nell'output di esempio seguente restituito da `Get-NetAdapterRss` RSS non è abilitato.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
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
    Enabled                 : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS è sempre abilitato per impostazione predefinita nella macchina virtuale Linux di Azure. I kernel Linux rilasciati a partire da gennaio 2017 includono opzioni di ottimizzazione di rete che consentono a una macchina virtuale Linux di ottenere una velocità effettiva di rete superiore.

### <a name="ubuntu"></a>Ubuntu

Per ottenere l'ottimizzazione, aggiornare prima di tutto la versione supportata più recente, a partire da gennaio 2017, ovvero:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04.0-LTS",
"Version": "16.04.201609071"
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

### <a name="centos"></a>CentOS

Per ottenere l'ottimizzazione, aggiornare prima di tutto la versione supportata più recente, a partire da gennaio 2017, ovvero:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
Al termine dell'aggiornamento, installare la versione più recente di Linux Integration Services (LIS).
L'ottimizzazione della velocità effettiva è disponibile in LIS a partire dalla versione 4.1.3. Immettere i comandi seguenti per installare LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v-4.1.3 kmod-microsoft-hyper-v-4.1.3
```

### <a name="red-hat"></a>Red Hat

Per ottenere l'ottimizzazione, aggiornare prima di tutto la versione supportata più recente, a partire da gennaio 2017, ovvero:

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

Al termine dell'aggiornamento, installare la versione più recente di Linux Integration Services (LIS).
L'ottimizzazione della velocità effettiva è disponibile in LIS a partire dalla versione 4.1.3. Immettere i comandi seguenti per scaricare e installare LIS:

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh  #or upgrade.sh if previous LIS was previously installed
```
 
Per altre informazioni su Linux Integration Services versione 4.1 per Hyper-V, vedere la [pagina di download](https://www.microsoft.com/download/details.aspx?id=51612).



<!--HONumber=Feb17_HO3-->


