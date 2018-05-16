---
title: Le differenze e le considerazioni per le macchine virtuali nello Stack di Azure | Documenti Microsoft
description: Informazioni sulle considerazioni e le differenze quando si lavora con le macchine virtuali nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.openlocfilehash: 83a0b8ff040425ac30cff96936f2f639fd1b5643
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Considerazioni sull'utilizzo di macchine virtuali nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Macchine virtuali di Azure Stack fornire risorse di elaborazione su richiesta e scalabili. Prima di distribuire le macchine virtuali (VM), è necessario comprendere le differenze tra le funzionalità di macchina virtuale disponibili nello Stack di Azure e Microsoft Azure. In questo articolo vengono descritte queste differenze e identifica le considerazioni chiave per la pianificazione della distribuzione delle macchine virtuali. Per ulteriori informazioni sulle differenze generali tra Stack di Azure e Azure, vedere il [considerazioni chiave](azure-stack-considerations.md) articolo.

## <a name="cheat-sheet-virtual-machine-differences"></a>Schede di riferimento rapido: macchina virtuale differenze

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
| Immagini di macchine virtuali | Azure Marketplace contiene le immagini che è possibile utilizzare per creare una macchina virtuale. Vedere il [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) pagina per visualizzare l'elenco delle immagini che sono disponibili in Azure Marketplace. | Per impostazione predefinita, sono non sono presenti immagini disponibili in marketplace dello Stack di Azure. L'amministratore del cloud di Azure Stack deve pubblicare o scaricare immagini marketplace Azure Stack prima che gli utenti possono usare tali. |
| Dimensioni delle macchine virtuali | Azure supporta un'ampia gamma di dimensioni per le macchine virtuali. Per conoscere le dimensioni disponibili e le opzioni, vedere il [dimensioni delle macchine virtuali di Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [dimensioni delle macchine virtuali Linux](../../virtual-machines/linux/sizes.md) argomenti. | Stack di Azure supporta un subset di dimensioni delle macchine virtuali che sono disponibili in Azure. Per visualizzare l'elenco dei formati supportati, vedere il [dimensioni delle macchine virtuali](#virtual-machine-sizes) sezione di questo articolo. |
| Quote macchina virtuale | [I limiti di quota](../../azure-subscription-service-limits.md#service-specific-limits) impostati da Microsoft | L'amministratore del cloud di Azure Stack deve assegnare quote prima di macchine virtuali che offrono agli utenti. |
| Estensioni macchina virtuale |Azure supporta un'ampia gamma di estensioni di macchina virtuale. Per ulteriori informazioni sulle estensioni disponibili, consultare il [estensioni delle macchine virtuali e le funzionalità](../../virtual-machines/windows/extensions-features.md) articolo.| Stack di Azure supporta un subset di estensioni che sono disponibili in Azure e dell'estensione hanno versioni specifiche. L'amministratore del cloud di Azure Stack possa scegliere le estensioni da messe a disposizione per i propri utenti. Per visualizzare l'elenco di estensioni supportate, consultare il [estensioni delle macchine virtuali](#virtual-machine-extensions) sezione di questo articolo. |
| Rete della macchina virtuale | Indirizzi IP pubblici assegnati alle macchine virtuali tenant sono accessibili tramite Internet.<br><br><br>Macchine virtuali di Azure è un nome DNS predefinito | Indirizzi IP pubblici assegnati a una macchina virtuale tenant sono accessibili solo l'ambiente Azure Stack Development Kit. Un utente deve avere accesso al Kit di sviluppo dello Stack di Azure tramite [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) per connettersi a una macchina virtuale che viene creata nello Stack di Azure.<br><br>Macchine virtuali create all'interno di un'istanza specifica di Stack di Azure è un nome DNS in base al valore configurato dall'amministratore del cloud. |
| Archiviazione della macchina virtuale | Supporta [dischi gestiti.](../../virtual-machines/windows/managed-disks-overview.md) | Dischi gestiti non sono ancora supportati nello Stack di Azure. |
| Versioni dell'API | Azure offre sempre le versioni più recenti di API per tutte le funzionalità di macchina virtuale. | Stack di Azure supporta versioni specifiche di API e servizi di Azure specifici per questi servizi. Per visualizzare l'elenco delle versioni API supportate, consultare il [versioni API](#api-versions) sezione di questo articolo. |
|Set di disponibilità della macchina virtuale|Più domini di errore (2 o 3 regione)<br>Più domini di aggiornamento<br>Supporto disco gestito|Più domini di errore (2 o 3 regione)<br>Più domini di aggiornamento (fino a 20)<br>Nessun supporto per dischi gestiti|
|set di scalabilità di macchine virtuali|Scalabilità automatica è supportata|Scalabilità automatica non è supportato.<br>Aggiungere più istanze di una scala impostata utilizzando il portale, i modelli di gestione risorse o PowerShell.

## <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Stack di Azure impone i limiti delle risorse per evitare di consumo delle risorse per server locale e a livello di servizio. Questi limiti migliorano l'esperienza di tenant, riducendo l'impatto dell'utilizzo delle risorse da altri tenant.

- Rete in uscita dalla macchina virtuale, siano estremità della larghezza di banda in uso. Estremità nello Stack di Azure sono le stesse i delimitatori in Azure.
- Per le risorse di archiviazione, Stack di Azure implementa i limiti di IOPS di archiviazione per evitare base consumo eccessivo di risorse da parte dei tenant per l'accesso all'archiviazione.
- Per le macchine virtuali con più dischi dati collegati, la velocità effettiva massima di ogni disco dati è 500 operazioni di IOPS per HHDs e 2300 IOPS per unità SSD.

Nella tabella seguente sono elencate le macchine virtuali che sono supportate nello Stack di Azure con la relativa configurazione:

| type           | Dimensione          | Intervallo di dimensioni supportate |
| ---------------| ------------- | ------------------------ |
|Scopo generico |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Scopo generico |A standard     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Scopo generico |Serie D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Scopo generico |Serie Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Scopo generico |Serie DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Scopo generico |Serie DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Ottimizzate per la memoria|Serie D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Ottimizzate per la memoria|Serie DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Ottimizzate per la memoria|Serie Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Ottimizzate per la memoria|DSv2-series-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Dimensioni delle macchine virtuali e le relative quantità di risorse associati sono coerenti tra Stack di Azure e Azure. Ciò include la quantità di memoria, il numero di core e le dimensioni o un numero di dischi dati che possono essere creati. Prestazioni delle macchine virtuali con la stessa dimensione dipendono tuttavia le caratteristiche di un particolare ambiente dello Stack di Azure sottostante.

## <a name="virtual-machine-extensions"></a>Estensioni macchina virtuale

 Stack di Azure include un piccolo set di estensioni. Gli aggiornamenti ed estensioni aggiuntive sono disponibili tramite diffusione Marketplace.

Utilizzare lo script di PowerShell seguente per ottenere l'elenco di estensioni di macchine virtuali che sono disponibili nell'ambiente dello Stack di Azure:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versioni dell'API

Funzionalità delle macchine virtuali nello Stack di Azure supporta le versioni API seguenti:

![Tipi di risorsa di macchina virtuale](media/azure-stack-vm-considerations/vm-resoource-types.png)

Per ottenere le versioni di API per la funzionalità delle macchine virtuali che sono disponibili nell'ambiente dello Stack di Azure, è possibile utilizzare lo script di PowerShell seguente:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

L'elenco dei tipi di risorse supportati e le versioni dell'API può variare se l'operatore cloud Aggiorna ambiente dello Stack di Azure a una versione più recente.

## <a name="windows-activation"></a>Attivazione di Windows

In base ai diritti di utilizzo del prodotto e condizioni di licenza Microsoft, è necessario utilizzare prodotti Windows. Usa Azure Stack [attivazione automatica della macchina virtuale](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (l'attivazione automatica della) per attivare le macchine virtuali di Windows Server (VM).

- Host di Stack Azure attiva Windows con le chiavi di attivazione automatica per Windows Server 2016. Tutte le macchine virtuali che eseguono Windows Server 2012 o versioni successive vengono attivate automaticamente.
- Le macchine virtuali che esecuzione Windows Server 2008 R2 non vengono attivati automaticamente e deve essere attivato utilizzando [attivazione MAK](https://technet.microsoft.com/library/ff793438.aspx).

Microsoft Azure utilizza l'attivazione di gestione delle CHIAVI per attivare le macchine virtuali di Windows. Se si sposta una macchina virtuale dallo Stack di Azure in Azure e di reclami attivare problemi, vedere [problemi di attivazione di macchina virtuale Windows Azure di risolvere i problemi](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Informazioni aggiuntive sono reperibile nel [gli errori di attivazione di risoluzione dei problemi di Windows nelle macchine virtuali di Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) post di blog del Team di supporto di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale Windows con PowerShell nello Stack di Azure](azure-stack-quick-create-vm-windows-powershell.md)