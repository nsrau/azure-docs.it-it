---
title: Introduzione alle macchine virtuali in Azure Stack
description: Informazioni su macchine virtuali di Azure Stack
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: a0d75b13369cff4e99bef6f57a3b01f3d8eee6e4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227164"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introduzione alle macchine virtuali in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Azure Stack offre macchine virtuali (VM) come un tipo di una risorsa di calcolo on demand e scalabile. È possibile scegliere una macchina virtuale quando è necessario un maggiore controllo sull'ambiente informatico rispetto alle altre opzioni. Questo articolo fornisce informazioni dettagliate, prima di creare la macchina virtuale.

Una macchina virtuale di Azure Stack ti offre la flessibilità della virtualizzazione senza la necessità di gestire i cluster o singoli computer. Tuttavia, è ancora necessario mantenere la macchina virtuale eseguendo attività come la configurazione, l'applicazione di patch e installazione del software che viene eseguita su di esso.

È possibile usare le macchine virtuali di Azure Stack in vari modi. Ad esempio: 

- **Sviluppo e test**  
    Macchine virtuali di Azure Stack offre un modo semplice e rapido per creare un computer con una configurazione specifica necessaria al codice e testare un'applicazione.

- **Applicazioni nel cloud**  
    Poiché può variare della domanda per l'applicazione, potrebbe essere conveniente eseguirla su una macchina virtuale in Azure Stack. È possibile pagare per VM aggiuntive quando sono necessarie e arrestarle quando non sono richieste.

- **Data Center estesa**  
    Le macchine virtuali in una rete virtuale di Azure Stack possono essere facilmente collegate alla rete dell'organizzazione o in Azure.

Le macchine virtuali usate dall'applicazione possono aumentare o scalare orizzontalmente per qualsiasi elemento è obbligatorio in base alle esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Aspetti da tenere in considerazione prima della creazione di una VM

Sono sempre presenti numerose considerazioni di progettazione quando si compila un'infrastruttura di applicazione in Azure Stack. Questi aspetti di una macchina virtuale sono importanti da considerare prima di iniziare a creare l'infrastruttura:

- I nomi delle risorse dell'applicazione.
- Le dimensioni della macchina virtuale.
- Il numero massimo di macchine virtuali che possono essere creati.
- Il sistema operativo che esegue la macchina virtuale.
- La configurazione della macchina virtuale dopo l'avvio.
- Le risorse correlate richieste dalla VM.

### <a name="naming"></a>Denominazione

Una macchina virtuale ha un nome assegnato a esso e include il nome di un computer configurato come parte del sistema operativo. Il nome di una VM può essere composto da un massimo di 15 caratteri.

Se si usa Azure Stack per creare il disco del sistema operativo, il nome del computer e il nome della macchina virtuale sono uguali. Se si carica e usare la propria immagine che contiene un sistema operativo configurato in precedenza e usarlo per creare una macchina virtuale, i nomi possono essere diversi. Quando si carica il proprio file di immagine, verificare il nome del computer nel sistema operativo e la macchina virtuale denominare lo stesso come procedura consigliata.

### <a name="vm-size"></a>Dimensioni macchina virtuale

Le dimensioni della macchina virtuale che usa sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Azure Stack offre un'ampia gamma di dimensioni per supportare molti tipi di uso.

### <a name="vm-limits"></a>Limiti delle macchine Virtuali

La sottoscrizione ha limiti di quota predefiniti che possono influire sulla distribuzione di più macchine virtuali per il progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area.

### <a name="operating-system-disks-and-images"></a>Immagini e dischi del sistema operativo

Le macchine virtuali usano dischi rigidi virtuali (VHD) per archiviare il sistema operativo e i dati. Tali dischi vengono usati anche per le immagini che è possibile scegliere per installare un sistema operativo.
Azure Stack offre un marketplace da usare con diverse versioni e differenti tipi di sistemi operativi. Le immagini del Marketplace sono identificate dall'editore di immagini, offerta, sku e versione (in genere versione viene specificata come versione più recente).

Nella tabella seguente vengono illustrati alcuni metodi che è possibile trovare le informazioni per un'immagine:

|Metodo|DESCRIZIONE|
|---------|---------|
|Portale di Azure Stack|I valori vengono specificati automaticamente quando si seleziona un'immagine da usare.|
|PowerShell per Azure Stack|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|API REST     |[List image publishers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[List image offers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Elencare gli SKU immagine](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

È possibile scegliere di caricare e usare la propria immagine. Se esegue l'operazione, il nome dell'editore, offerta e lo sku non vengono usate.

### <a name="extensions"></a>Estensioni

Le estensioni VM offrono alla VM funzionalità aggiuntive tramite configurazione post-distribuzione e le attività automatizzate.
È possibile eseguire le seguenti attività comuni tramite le estensioni:

- **Eseguire script personalizzati**  
    L'estensione Script personalizzata consente di configurare i carichi di lavoro della macchina virtuale eseguendo lo script quando viene eseguito il provisioning della macchina virtuale.

- **Distribuire e gestire le configurazioni**  
    L'estensione PowerShell Desired State Configuration (DSC) consente di impostare DSC in una macchina virtuale per gestire le configurazioni e ambienti.

- **Raccogliere dati di diagnostica**  
    L'estensione diagnostica di Azure consente di configurare la VM per raccogliere dati di diagnostica che possono essere utilizzati per monitorare l'integrità dell'applicazione.

### <a name="related-resources"></a>Risorse correlate

Le risorse nella tabella seguente vengono usate dalla macchina virtuale e devono presenti o essere create quando viene creata la macchina virtuale.


|Risorsa|Obbligatorio|DESCRIZIONE|
|---------|---------|---------|
|Gruppo di risorse|Sì|La VM deve essere contenuta in un gruppo di risorse.|
|Account di archiviazione|No |La macchina virtuale non è necessario l'account di archiviazione per archiviare i dischi rigidi virtuali, se si usa Managed Disks. <br>La macchina virtuale è necessario che l'account di archiviazione per archiviare i dischi rigidi virtuali tramite dischi non gestiti.|
|Rete virtuale|Sì|La VM deve appartenere a una rete virtuale.|
|Indirizzo IP pubblico|No |È possibile che la VM disponga di un indirizzo IP pubblico assegnato per accedervi in modalità remota.|
|interfaccia di rete|Sì|La VM richiede l'interfaccia di rete per comunicare in rete.|
|Dischi dati|No |La VM può includere dischi dati per espandere le funzionalità di archiviazione.|

## <a name="create-your-first-vm"></a>Creare la prima macchina virtuale

Sono disponibili diverse opzioni per creare una macchina virtuale. La scelta dipende dall'ambiente.
Nella tabella seguente fornisce informazioni per iniziare a creare la macchina virtuale.


|Metodo|Articolo|
|---------|---------|
|Portale di Azure Stack|Creare una macchina virtuale Windows con il portale di Azure Stack<br>[Creare una macchina virtuale Linux usando il portale di Azure Stack](azure-stack-quick-linux-portal.md)|
|Modelli|I modelli di Azure Stack Quickstart si trovano in:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Creare una macchina virtuale Windows usando PowerShell nello Stack di Azure](azure-stack-quick-create-vm-windows-powershell.md)<br>[Creare una macchina virtuale Linux usando PowerShell nello Stack di Azure](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Creare una macchina virtuale Windows tramite CLI in Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Creare una macchina virtuale Linux con CLI di Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Gestire la macchina virtuale

È possibile gestire le macchine virtuali tramite un portale basato sul browser, gli strumenti da riga di comando con il supporto per la creazione di script o direttamente tramite le API. Alcune attività di gestione comuni che è possibile eseguire sono:

- Recupero di informazioni su una macchina virtuale
- La connessione a una macchina virtuale
- La gestione della disponibilità
- Le operazioni di backup

### <a name="get-information-about-your-vm"></a>Ottenere informazioni sulla VM

Nella tabella seguente illustra alcuni dei metodi che è possibile ottenere informazioni su una macchina virtuale.


|Metodo|DESCRIZIONE|
|---------|---------|
|Portale di Azure Stack|Nel menu hub fare clic su macchine virtuali e quindi selezionare la macchina virtuale dall'elenco. Nella pagina per la macchina virtuale, hai accesso alle informazioni generali, impostare valori e le metriche di monitoraggio.|
|Azure PowerShell|Gestire le macchine virtuali è simile in Azure e Azure Stack. Per altre informazioni sull'uso di PowerShell, vedere l'argomento di Azure seguente:<br>[Creare e gestire VM Windows con il modulo Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Client SDK|Con c# per gestire le macchine virtuali è simile in Azure e Azure Stack. Per altre informazioni, vedere l'argomento di Azure seguente:<br>[Creare e gestire macchine virtuali Windows in Azure tramite c#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

È possibile usare la **Connect** pulsante nel portale di Azure Stack per connettersi alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- [Considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md)
