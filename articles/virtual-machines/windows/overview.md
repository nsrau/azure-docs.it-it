---
title: Panoramica delle macchine virtuali Windows | Microsoft Docs
description: Informazioni sulla creazione e gestione di macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 7ddd9244558479f1fc77c0a9f3d02d0d3b95ca9f
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Panoramica delle macchine virtuali Windows in Azure
Le macchine virtuali (VM) di Azure sono uno dei vari tipi di [risorse di calcolo scalabili e su richiesta](../../app-service-web/choose-web-site-cloud-service-vm.md) offerte da Azure. In genere, la scelta ricade su una VM se è necessario maggiore controllo dell'ambiente di calcolo rispetto a quanto offerto dalle altre soluzioni. Questo articolo fornisce informazioni sugli aspetti da tenere in considerazione prima di creare una VM e sulla relativa modalità di creazione e gestione.

Una VM di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale. È comunque necessario gestire la VM eseguendo determinate attività, ovvero configurazione, applicazione di patch e installazione del software in esecuzione sulla macchina virtuale.

È possibile usare le macchine virtuali di Azure in vari modi. Di seguito sono riportati alcuni esempi:

* **Sviluppo e test**: le VM di Azure consentono di creare in modo semplice e rapido un computer con configurazioni specifiche necessarie per scrivere il codice e testare un'applicazione.
* **Applicazioni nel cloud**: dal momento che le richieste per l'applicazione possono variare, potrebbe essere conveniente eseguirla su una VM in Azure. È possibile pagare per VM aggiuntive quando sono necessarie e arrestarle quando non sono richieste.
* **Data center avanzato**: le macchine virtuali in una rete virtuale di Azure possono essere facilmente collegate alla rete dell'organizzazione.

È possibile aumentare o ridurre il numero di VM usate dall'applicazione in base alle proprie esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Aspetti da tenere in considerazione prima della creazione di una VM
Quando si compila l'infrastruttura di un'applicazione in Azure, ci sono sempre numerose [considerazioni di progettazione](infrastructure-virtual-machine-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) di cui tener conto. Prima di iniziare, è quindi importante analizzare gli aspetti seguenti di una VM:

* Nomi delle risorse dell'applicazione
* Posizione in cui sono archiviate le risorse
* Dimensioni della VM
* Numero massimo di VM che è possibile creare
* Sistema operativo in esecuzione sulla VM
* Configurazione della VM dopo l'avvio
* Risorse correlate richieste dalla VM

### <a name="naming"></a>Denominazione
Una macchina virtuale dispone di un [nome](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) assegnato e di un nome computer configurato come parte del sistema operativo. Il nome di una VM può essere composto da un massimo di 15 caratteri.

Se si usa Azure per creare il disco del sistema operativo, il nome del computer e il nome della macchina virtuale sono uguali. Se si [carica la propria immagine](upload-generalized-managed.md) contenente un sistema operativo configurato in precedenza e la si usa per creare una macchina virtuale, i nomi possono differire. Quando si carica il proprio file di immagine, è consigliabile far coincidere il nome computer nel sistema operativo con il nome della macchina virtuale.

### <a name="locations"></a>Località
Le risorse create in Azure vengono distribuite tra più [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. In genere, quando si crea una VM l'area viene chiamata **località**. Per una VM, la località specifica dove sono archiviati i dischi rigidi virtuali.

Questa tabella illustra alcuni dei metodi con cui è possibile ottenere un elenco di località disponibili.

| Metodo | Description |
| --- | --- |
| Portale di Azure |Quando si crea una VM, selezionare una località nell'elenco. |
| Azure PowerShell |Usare il comando [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation). |
| API REST |Usare l'operazione [List locations](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations). |

### <a name="vm-size"></a>Dimensioni macchina virtuale
Le [dimensioni](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) della VM usata sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Azure offre una vasta gamma di dimensioni per supportare molti tipi di uso.

Azure addebita un [costo orario](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. I costi di archiviazione vengono determinati e addebitati separatamente.

### <a name="vm-limits"></a>Limiti della VM
Ogni sottoscrizione di Azure ha [limiti di quota](../../azure-subscription-service-limits.md) predefiniti che possono influire sulla distribuzione di molte VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area. I limiti possono essere aumentati creando un ticket di supporto in cui si richiede tale incremento.

### <a name="operating-system-disks-and-images"></a>Immagini e dischi del sistema operativo
Le macchine virtuali usano [dischi rigidi virtuali (VHD)](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per archiviare il sistema operativo e i dati. Tali dischi vengono usati anche per le immagini che è possibile scegliere per installare un sistema operativo. 

Azure fornisce molte [immagini Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) da usare con diverse versioni e differenti tipi di sistemi operativi Windows Server. Le immagini Marketplace sono identificate dall'editore di immagini, dall'offerta, dalla SKU e dalla versione (in genere la versione viene specificata alla fine). 

Questa tabella illustra alcuni metodi per ottenere informazioni relative a un'immagine.

| Metodo | Description |
| --- | --- |
| Portale di Azure |I valori vengono specificati automaticamente quando si seleziona un'immagine da usare. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "location"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "location" -Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location "location" -Publisher "publisherName" -Offer "offerName" |
| API REST |[List image publishers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[List image offers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[List image skus](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

È possibile scegliere di [caricare e usare la propria immagine](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account). In questo caso, il nome dell'editore, l'offerta e la SKU non vengono usati.

### <a name="extensions"></a>Estensioni
Le [estensioni](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) offrono alla VM funzionalità aggiuntive grazie a una configurazione post-distribuzione e ad attività automatiche.

È possibile eseguire le seguenti attività comuni tramite le estensioni:

* **Eseguire script personalizzati**: l'[estensione script personalizzata](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) consente di configurare i carichi di lavoro della VM eseguendo lo script quando viene eseguito il provisioning della VM.
* **Distribuire e gestire le configurazioni**: l'[estensione PowerShell DSC (Desired State Configuration)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) consente di impostare DSC in una VM per gestire configurazioni e ambienti.
* **Raccogliere i dati di diagnostica**: l'[estensione di Diagnostica di Microsoft Azure](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) consente di configurare la VM per raccogliere dati di diagnostica che consentono di monitorare l'integrità dell'applicazione.

### <a name="related-resources"></a>Risorse correlate
Le risorse in questa tabella vengono usate dalla VM e devono essere presenti o essere create quando si crea la VM.

| Risorsa | Obbligatorio | Description |
| --- | --- | --- |
| [Gruppo di risorse](../../azure-resource-manager/resource-group-overview.md) |Sì |La VM deve essere contenuta in un gruppo di risorse. |
| [Account di archiviazione](../../storage/storage-create-storage-account.md) |Sì |La VM richiede l'account di archiviazione per archiviare i dischi rigidi virtuali. |
| [Rete virtuale](../../virtual-network/virtual-networks-overview.md) |Sì |La VM deve appartenere a una rete virtuale. |
| [Indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |No |È possibile che la VM disponga di un indirizzo IP pubblico assegnato per accedervi in modalità remota. |
| [Interfaccia di rete](../../virtual-network/virtual-network-network-interface.md) |Sì |La VM richiede l'interfaccia di rete per comunicare in rete. |
| [Dischi dati](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |No |La VM può includere dischi dati per espandere le funzionalità di archiviazione. |

## <a name="how-do-i-create-my-first-vm"></a>Come creare la prima VM
Sono disponibili diverse opzioni per creare una VM. La scelta dipende dall'ambiente interessato. 

Questa tabella include le informazioni necessarie per la creazione di una VM.

| Metodo | Articolo |
| --- | --- |
| Portale di Azure |[Creare una macchina virtuale con Windows tramite il portale](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Modelli |[Creare una macchina virtuale Windows con un modello di Gestione risorse](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Creare una VM Windows tramite PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Client SDK |[Distribuire le risorse di Azure tramite C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| API REST |[Create or update a VM](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) (Creare o aggiornare una VM) |

Sebbene ci si auguri che non accada mai nulla, talvolta potrebbero verificarsi dei problemi. In tal caso esaminare le informazioni contenute nell'articolo [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Modalità di gestione della VM creata
Le VM possono essere gestite mediante un portale basato su browser, gli strumenti da riga di comando con il supporto per gli script o direttamente tramite l'API. Alcune attività di gestione tipiche consentono di visualizzare informazioni su una VM, accedere a una VM, gestire la disponibilità ed eseguire backup.

### <a name="get-information-about-a-vm"></a>Visualizzare informazioni su una macchina virtuale
Questa tabella illustra alcuni dei metodi attraverso i quali è possibile ottenere informazioni su una VM.

| Metodo | Description |
| --- | --- |
| Portale di Azure |Nel menu Hub fare clic su **Macchine virtuali** e quindi selezionare una VM dall'elenco. Nel pannello della VM è possibile accedere alle informazioni generali, ai valori di impostazione e alle metriche di monitoraggio. |
| Azure PowerShell |Per informazioni sull'uso di PowerShell per gestire le macchine virtuali, vedere [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| API REST |Usare l'operazione [Get VM information](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) per ottenere le informazioni su una VM. |
| Client SDK |Per informazioni sull'uso di C# per gestire le VM, vedere [Gestire le macchine virtuali di Azure con Azure Resource Manager e C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |

### <a name="log-on-to-the-vm"></a>Accedere alla VM
Per [avviare una sessione di Desktop remoto (RDP)](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), si usa il pulsante Connetti nel portale di Azure. Quando si tenta di usare una connessione remota, potrebbero talvolta verificarsi dei problemi. In tal caso verificare le informazioni di supporto nell'articolo [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="manage-availability"></a>Gestire la disponibilità
È importante comprendere come [garantire disponibilità elevata](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per un'applicazione. Questa configurazione prevede la creazione di più VM per assicurarsi che almeno una sia in esecuzione.

Per qualificarsi per la garanzia del contratto di servizio del 99,95% per le VM, devono essere distribuite due o più VM che eseguono il carico di lavoro in un [set di disponibilità](infrastructure-availability-sets-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In questo modo le VM vengono distribuite tra più domini di errore e anche in host con finestre di manutenzione diverse. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) descrive la disponibilità garantita di Azure nel suo complesso.

### <a name="back-up-the-vm"></a>Eseguire il backup della VM
Un [ insieme di credenziali di Servizi di ripristino](../../backup/backup-introduction-to-azure-backup.md) è usato per proteggere dati e asset nei servizi di Backup di Azure e di Azure Site Recovery. È possibile usare tale insieme per [distribuire e gestire i backup per le macchine virtuali distribuite con Resource Manager utilizzando PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Passaggi successivi
* Se si desidera lavorare con VM Linux, vedere [Azure e Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per altre informazioni sulle linee guida per la configurazione dell'infrastruttura vedere [Procedura dettagliata per un'infrastruttura di esempio di Azure](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Accertarsi di seguire le [Procedure consigliate per l'esecuzione di una VM Windows in Azure](guidance-compute-single-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


