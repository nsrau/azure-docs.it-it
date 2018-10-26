---
title: Differenze e considerazioni per le macchine virtuali in Azure Stack | Microsoft Docs
description: Informazioni sulle differenze e considerazioni quando si lavora con le macchine virtuali in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: cb02ecb06728f5f36a0d51a3ec22cc8ba5cb44e7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094756"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Considerazioni sull'utilizzo delle macchine virtuali in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Macchine virtuali di Azure Stack forniscono le risorse di calcolo on demand e scalabili. Prima di distribuire macchine virtuali (VM), è necessario comprendere le differenze tra le funzionalità di macchina virtuale disponibili in Azure Stack e Microsoft Azure. Questo articolo vengono descritte queste differenze e identifica le considerazioni fondamentali per la pianificazione della distribuzione delle macchine virtuali. Per altre informazioni sulle differenze generali tra Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

## <a name="cheat-sheet-virtual-machine-differences"></a>Foglio informativo: macchina virtuale differenze

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
| Immagini di macchine virtuali | Azure Marketplace contiene le immagini che è possibile usare per creare una macchina virtuale. Vedere le [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) pagina per visualizzare l'elenco delle immagini disponibili in Azure Marketplace. | Per impostazione predefinita, non sono presenti immagini disponibili nel marketplace di Azure Stack. L'amministratore del cloud Azure Stack debba pubblicare o scaricare le immagini nel Marketplace di Azure Stack prima di consentirne l'uso. |
| Dimensioni delle macchine virtuali | Azure supporta un'ampia gamma di dimensioni per le macchine virtuali. Per altre informazioni sulle dimensioni disponibili e le opzioni, vedere il [dimensioni delle macchine virtuali Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [dimensioni delle macchine virtuali Linux](../../virtual-machines/linux/sizes.md) argomenti. | Azure Stack supporta un subset di dimensioni delle macchine virtuali che sono disponibili in Azure. Per visualizzare l'elenco dei formati supportati, vedere la [dimensioni delle macchine virtuali](#virtual-machine-sizes) sezione di questo articolo. |
| Quote macchina virtuale | [I limiti di quota](../../azure-subscription-service-limits.md#service-specific-limits) impostati da Microsoft | L'amministratore del cloud Azure Stack è necessario assegnare quote prima che le macchine virtuali offrono agli utenti. |
| Estensioni macchina virtuale |Azure supporta un'ampia gamma di estensioni della macchina virtuale. Per informazioni sulle estensioni disponibili, vedere la [estensioni di macchina virtuale e funzionalità](../../virtual-machines/windows/extensions-features.md) articolo.| Azure Stack supporta un subset delle estensioni disponibili in Azure e dell'estensione presentano versioni specifiche. L'amministratore del cloud Azure Stack può scegliere le estensioni da messe a disposizione per gli utenti. Per visualizzare l'elenco di estensioni supportate, vedere la [estensioni della macchina virtuale](#virtual-machine-extensions) sezione di questo articolo. |
| Rete della macchina virtuale | Indirizzi IP pubblici assegnati alla macchina virtuale del tenant sono accessibili tramite Internet.<br><br><br>Macchine virtuali di Azure con un nome DNS predefinito | Indirizzi IP pubblici assegnati a una macchina virtuale tenant siano accessibili all'interno dell'ambiente Azure Stack Development Kit solo. Un utente deve avere accesso a Azure Stack Development Kit tramite [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) oppure [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) per connettersi a una macchina virtuale creata in Azure Stack.<br><br>Le macchine virtuali create all'interno di una specifica istanza di Azure Stack ha un nome DNS in base al valore configurato dall'amministratore del cloud. |
| Archiviazione della macchina virtuale | Supporta [dischi gestiti.](../../virtual-machines/windows/managed-disks-overview.md) | I dischi gestiti sono supportati in Azure Stack con 1808 e versioni successive. |
| Prestazioni di dischi di macchina virtuale | Varia a seconda delle dimensioni e tipo di disco. | Dipende dalle dimensioni della macchina virtuale della VM che i dischi sono collegati a, fare riferimento al [le dimensioni di macchina virtuale supportate in Azure Stack](azure-stack-vm-sizes.md) articolo.
| Versioni dell'API | Azure ha sempre le versioni dell'API più recente per tutte le funzionalità di macchina virtuale. | Azure Stack supporta specifiche versioni di API e servizi di Azure specifici per questi servizi. Per visualizzare l'elenco delle versioni API supportate, vedere la [le versioni API](#api-versions) sezione di questo articolo. |
|Set di disponibilità della macchina virtuale|Più domini di errore (2 o 3 per ogni area)<br>Più domini di aggiornamento<br>Supporto di dischi gestiti|Più domini di errore (2 o 3 per ogni area)<br>Più domini di aggiornamento (fino a 20)<br>Nessun supporto del disco gestito|
|set di scalabilità di macchine virtuali|È supportata la scalabilità automatica|Scalabilità automatica non è supportata.<br>Aggiungere altre istanze di un set di scalabilità tramite il portale, modelli di Resource Manager o PowerShell.

## <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Stack di Azure impone limiti delle risorse per evitare il consumo di risorse per server locali e a livello di servizio. Questi limiti migliorano l'esperienza di tenant per ridurre l'impatto del consumo di risorse da altri tenant.

- Rete in uscita dalla macchina virtuale, esistono limiti di larghezza di banda posto. Caps nello Stack di Azure sono le stesse i limiti di Azure.
- Per le risorse di archiviazione di Azure Stack implementa limiti di IOPS di archiviazione per evitare base uso eccessivo delle risorse dai tenant per l'accesso di archiviazione.
- Per le macchine virtuali con più dischi dati collegati, la velocità effettiva massima di ciascun disco dati è di 500 IOPS per unità disco rigido e 2300 IOPS per unità SSD.

La tabella seguente elenca le macchine virtuali che sono supportate in Azure Stack con la relativa configurazione:

| type           | Dimensione          | Intervallo di dimensioni supportate |
| ---------------| ------------- | ------------------------ |
|Scopo generico |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Scopo generico |Oggetto standard     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Scopo generico |Serie D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Scopo generico |Serie Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Scopo generico |Serie DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Scopo generico |Serie DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Ottimizzate per la memoria|Serie D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Ottimizzate per la memoria|Serie DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Ottimizzate per la memoria|Serie Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Ottimizzate per la memoria|DSv2-series-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Dimensioni delle macchine virtuali e le relative quantità di risorse associati sono coerenti tra Azure e Azure Stack. Ciò include la quantità di memoria, il numero di core e il numero o le dimensioni dei dischi dati che possono essere creati. Prestazioni delle macchine virtuali con le stesse dimensioni dipendono tuttavia le caratteristiche di un determinato ambiente di Azure Stack sottostante.

## <a name="virtual-machine-extensions"></a>Estensioni macchina virtuale

 Lo Stack di Azure include un piccolo set di estensioni. Gli aggiornamenti e le estensioni aggiuntive sono disponibili tramite diffusione di Marketplace.

Usare lo script di PowerShell seguente per ottenere l'elenco di estensioni di macchina virtuale che sono disponibili nell'ambiente Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versioni dell'API

Funzionalità delle macchine virtuali in Azure Stack supporta le versioni dell'API seguente:

![Tipi di risorse della macchina virtuale](media/azure-stack-vm-considerations/vm-resoource-types.png)

È possibile usare lo script di PowerShell seguente per ottenere le versioni dell'API per la funzionalità delle macchine virtuali che sono disponibili nell'ambiente Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

L'elenco dei tipi di risorse supportati e le versioni dell'API può variare se l'operatore cloud Aggiorna l'ambiente Azure Stack per una versione più recente.

## <a name="windows-activation"></a>Attivazione di Windows

È necessario utilizzare i prodotti Windows in base alle condizioni di licenza Microsoft e diritti di utilizzo del prodotto. Usa Azure Stack [attivazione automatica della macchina virtuale](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) per attivare le macchine virtuali di Windows Server (VM).

- Host di Azure Stack attiva Windows con le chiavi di questa funzionalità per Windows Server 2016. Tutte le macchine virtuali che eseguono Windows Server 2012 o versioni successive vengono attivate automaticamente.
- Le macchine virtuali che eseguire Windows Server 2008 R2 non sono attivati automaticamente e deve essere attivato mediante [attivazione MAK](https://technet.microsoft.com/library/ff793438.aspx). Per utilizzare l'attivazione MAK, è necessario fornire il proprio codice product key.

Microsoft Azure utilizza l'attivazione del KMS per attivare le macchine virtuali Windows. Se si sposta una macchina virtuale da Azure Stack in Azure e possono rilevare problemi di attivazione, vedere [problemi di attivazione della macchina virtuale Windows Azure di risolvere i problemi](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Ulteriori informazioni vedere la [gli errori di attivazione di risoluzione dei problemi di Windows nelle macchine virtuali di Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) post di blog del Team di supporto di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale Windows con PowerShell in Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
