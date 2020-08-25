---
title: Panoramica delle macchine virtuali Windows in Azure
description: Panoramica delle macchine virtuali Windows in Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e955a51906f160264fc4e81f263fe7677be91de
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589623"
---
# <a name="windows-virtual-machines-in-azure"></a>Macchine virtuali Windows in Azure

Le macchine virtuali (VM) di Azure sono uno dei vari tipi di [risorse di calcolo scalabili e su richiesta](/azure/architecture/guide/technology-choices/compute-decision-tree) offerte da Azure. In genere, la scelta ricade su una VM se è necessario maggiore controllo dell'ambiente di calcolo rispetto a quanto offerto dalle altre soluzioni. Questo articolo fornisce informazioni sugli aspetti da tenere in considerazione prima di creare una VM e sulla relativa modalità di creazione e gestione.

Una VM di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale. È comunque necessario gestire la VM eseguendo determinate attività, ovvero configurazione, applicazione di patch e installazione del software in esecuzione sulla macchina virtuale.

È possibile usare le macchine virtuali di Azure in vari modi. Ad esempio:

* **Sviluppo e test**: le VM di Azure consentono di creare in modo semplice e rapido un computer con configurazioni specifiche necessarie per scrivere il codice e testare un'applicazione.
* **Applicazioni nel cloud**: dal momento che le richieste per l'applicazione possono variare, potrebbe essere conveniente eseguirla su una VM in Azure. È possibile pagare per VM aggiuntive quando sono necessarie e arrestarle quando non sono richieste.
* **Data center avanzato**: le macchine virtuali in una rete virtuale di Azure possono essere facilmente collegate alla rete dell'organizzazione.

È possibile aumentare o ridurre il numero di VM usate dall'applicazione in base alle proprie esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Aspetti da tenere in considerazione prima della creazione di una VM
Quando si compila l'infrastruttura di un'applicazione in Azure, ci sono sempre numerose [considerazioni di progettazione](/azure/architecture/reference-architectures/n-tier/windows-vm) di cui tener conto. Prima di iniziare, è quindi importante analizzare gli aspetti seguenti di una VM:

* Nomi delle risorse dell'applicazione
* Posizione in cui sono archiviate le risorse
* Dimensioni della VM
* Numero massimo di VM che è possibile creare
* Sistema operativo in esecuzione sulla VM
* Configurazione della VM dopo l'avvio
* Risorse correlate richieste dalla VM

### <a name="locations"></a>Percorsi
Le risorse create in Azure vengono distribuite tra più [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. In genere, quando si crea una VM l'area viene chiamata **località**. Per una VM, la località specifica dove sono archiviati i dischi rigidi virtuali.

Questa tabella illustra alcuni dei metodi con cui è possibile ottenere un elenco di località disponibili.

| Metodo | Descrizione |
| --- | --- |
| Portale di Azure |Quando si crea una VM, selezionare una località nell'elenco. |
| Azure PowerShell |Usare il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| API REST |Usare l'operazione [List locations](/rest/api/resources/subscriptions). |
| Interfaccia della riga di comando di Azure |Usare l'operazione [az account list-locations](/cli/azure/account?view=azure-cli-latest). |

### <a name="singapore-data-residency"></a>Residenza dei dati per Singapore

In Azure la funzionalità per abilitare l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) dell'area geografica Asia Pacifico. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo. Per altre informazioni, visitare il [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="availability"></a>Disponibilità
È stato annunciato un contratto di servizio leader del settore pari al 99,9% per una macchina virtuale a istanza singola, purché distribuita con Archiviazione Premium per tutti i dischi.  Perché la distribuzione si qualifichi per il contratto di servizio standard del 99,95% per le macchine virtuali, è comunque necessario distribuire due o più macchine virtuali che eseguono il carico di lavoro in un set di disponibilità. Un set di disponibilità assicura che le macchine virtuali vengano distribuite tra più domini di errore nei centri dati Azure e anche in host con finestre di manutenzione diverse. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.


## <a name="vm-size"></a>Dimensioni macchina virtuale
Le [dimensioni](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) della VM usata sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Azure offre una vasta gamma di dimensioni per supportare molti tipi di uso.

Azure addebita un [costo orario](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. I costi di archiviazione vengono determinati e addebitati separatamente.

## <a name="vm-limits"></a>Limiti della VM
Ogni sottoscrizione di Azure ha [limiti di quota](../../azure-resource-manager/management/azure-subscription-service-limits.md) predefiniti che possono influire sulla distribuzione di molte VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area. I limiti possono essere aumentati [creando un ticket di supporto in cui si richiede tale incremento](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

### <a name="operating-system-disks-and-images"></a>Immagini e dischi del sistema operativo
Le macchine virtuali usano [dischi rigidi virtuali (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per archiviare il sistema operativo e i dati. Tali dischi vengono usati anche per le immagini che è possibile scegliere per installare un sistema operativo. 

Azure fornisce molte [immagini Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) da usare con diverse versioni e differenti tipi di sistemi operativi Windows Server. Le immagini Marketplace sono identificate dall'editore di immagini, dall'offerta, dalla SKU e dalla versione (in genere la versione viene specificata alla fine). Sono supportati solo i sistemi operativi a 64 bit. Per altre informazioni sui sistemi operativi guest, i ruoli e le funzionalità supportati, vedere [Supporto di software server Microsoft per le macchine virtuali di Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Questa tabella illustra alcuni metodi per ottenere informazioni relative a un'immagine.

| Metodo | Descrizione |
| --- | --- |
| Portale di Azure |I valori vengono specificati automaticamente quando si seleziona un'immagine da usare. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *località*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *località* -Publisher *nomeEditore*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *località* -Publisher *nomeEditore* -Offer *nomeOfferta* |
| API REST |[List image publishers](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[List image offers](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[List image skus](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Interfaccia della riga di comando di Azure |[az vm image list-publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *località*<BR>[az vm image list-offers](/cli/azure/vm/image?view=azure-cli-latest) --location *località* --publisher *nomeEditore*<BR>[az vm image list-skus](/cli/azure/vm?view=azure-cli-latest) --location *località* --publisher *nomeEditore* --offer *nomeOfferta*|

È possibile scegliere di [caricare e usare la propria immagine](upload-generalized-managed.md). In questo caso, il nome dell'editore, l'offerta e la SKU non vengono usati.

### <a name="extensions"></a>Estensioni
Le [estensioni](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) offrono alla VM funzionalità aggiuntive grazie a una configurazione post-distribuzione e ad attività automatiche.

È possibile eseguire le seguenti attività comuni tramite le estensioni:

* **Eseguire script personalizzati**: l'[estensione script personalizzata](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) consente di configurare i carichi di lavoro della VM eseguendo lo script quando viene eseguito il provisioning della VM.
* **Distribuire e gestire le configurazioni**: l'[estensione PowerShell DSC (Desired State Configuration)](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) consente di impostare DSC in una VM per gestire configurazioni e ambienti.
* **Raccogliere i dati di diagnostica**: l'[estensione di Diagnostica di Microsoft Azure](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) consente di configurare la VM per raccogliere dati di diagnostica che consentono di monitorare l'integrità dell'applicazione.

### <a name="related-resources"></a>Risorse correlate
Le risorse in questa tabella vengono usate dalla VM e devono essere presenti o essere create quando si crea la VM.

| Risorsa | Obbligatoria | Descrizione |
| --- | --- | --- |
| [Gruppo di risorse](../../azure-resource-manager/management/overview.md) |Sì |La VM deve essere contenuta in un gruppo di risorse. |
| [Account di archiviazione](../../storage/common/storage-account-create.md) |Sì |La VM richiede l'account di archiviazione per archiviare i dischi rigidi virtuali. |
| [Rete virtuale](../../virtual-network/virtual-networks-overview.md) |Sì |La VM deve appartenere a una rete virtuale. |
| [Indirizzo IP pubblico](../../virtual-network/public-ip-addresses.md) |No |È possibile che la VM disponga di un indirizzo IP pubblico assegnato per accedervi in modalità remota. |
| [Interfaccia di rete](../../virtual-network/virtual-network-network-interface.md) |Sì |La VM richiede l'interfaccia di rete per comunicare in rete. |
| [Dischi dati](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |No |La VM può includere dischi dati per espandere le funzionalità di archiviazione. |

## <a name="next-steps"></a>Passaggi successivi

Creare la prima VM

- [Portale](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Interfaccia della riga di comando di Azure](quick-create-cli.md)
