---
title: Introduzione alle macchine virtuali di Azure Stack
description: Informazioni sulle macchine virtuali di Azure Stack
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 2453f2449124cb4956797e0d9748f1ee3bf0d9ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introduzione alle macchine virtuali di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="overview"></a>Panoramica
Una macchina virtuale di Stack di Azure (VM) è un tipo di risorsa di calcolo su richiesta, scalabile che offre Azure Stack. In genere, la scelta ricade su una VM se è necessario maggiore controllo dell'ambiente di calcolo rispetto a quanto offerto dalle altre soluzioni. Questo articolo fornisce informazioni sugli aspetti da tenere in considerazione prima di creare una VM e sulla relativa modalità di creazione e gestione.

Una macchina virtuale di Azure Stack offre la flessibilità della virtualizzazione senza dover gestire singoli cluster o macchine. È comunque necessario gestire la VM eseguendo determinate attività, ovvero configurazione, applicazione di patch e installazione del software in esecuzione sulla macchina virtuale.

Macchine virtuali di Azure Stack può essere utilizzate in vari modi. Ad esempio: 

* **Sviluppo e test** : le macchine virtuali di Azure Stack offrono una rapida e necessarie per creare un computer con una configurazione specifica di codice e testare un'applicazione.

* **Le applicazioni nel cloud** : perché è richiesta per l'applicazione può variare, potrebbe essere utile economico per l'esecuzione in una macchina virtuale nello Stack di Azure. È possibile pagare per VM aggiuntive quando sono necessarie e arrestarle quando non sono richieste.

* **Estesi datacenter** – macchine virtuali in una rete virtuale di Azure Stack può facilmente essere connesso alla rete dell'organizzazione o Azure.

È possibile aumentare o ridurre il numero di VM usate dall'applicazione in base alle proprie esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Aspetti da tenere in considerazione prima della creazione di una VM

Sono sempre presenti numerose considerazioni di progettazione quando si compila un'infrastruttura dell'applicazione nello Stack di Azure. Prima di iniziare, è quindi importante analizzare gli aspetti seguenti di una VM:

- Nomi delle risorse dell'applicazione
- Dimensioni della VM
- Numero massimo di VM che è possibile creare
- Sistema operativo in esecuzione sulla VM
- Configurazione della VM dopo l'avvio 
- Risorse correlate richieste dalla VM

### <a name="naming"></a>Denominazione

Una macchina virtuale ha un nome assegnato e ha il nome di un computer configurato come parte del sistema operativo. Il nome di una VM può essere composto da un massimo di 15 caratteri.

Se si utilizza Azure Stack per creare il disco del sistema operativo, il nome del computer e il nome della macchina virtuale sono uguali. Se si carica e utilizza un'immagine personalizzata che contiene un sistema operativo configurato in precedenza e utilizzarlo per creare una macchina virtuale, i nomi siano diversi. Quando si carica un file di immagine, verificare il nome del computer nel sistema operativo e nome di macchina virtuale lo stesso come procedura consigliata.

### <a name="vm-size"></a>Dimensioni macchina virtuale

Le dimensioni della macchina virtuale in uso sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Stack di Azure offre un'ampia gamma di dimensioni per supportare molti tipi di utilizzo.

### <a name="vm-limits"></a>Limiti VM

La sottoscrizione dispone di limiti di quota predefiniti che possono influire sulla distribuzione di più macchine virtuali per il progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area.

### <a name="operating-system-disks-and-images"></a>Immagini e dischi del sistema operativo

Le macchine virtuali usano dischi rigidi virtuali (VHD) per archiviare il sistema operativo e i dati. Tali dischi vengono usati anche per le immagini che è possibile scegliere per installare un sistema operativo.
Stack di Azure fornisce un marketplace per l'utilizzo con varie versioni e i tipi di sistemi operativi. Le immagini Marketplace sono identificate dall'editore di immagini, dall'offerta, dalla SKU e dalla versione (in genere la versione viene specificata alla fine).

Nella tabella seguente vengono illustrati alcuni metodi che è possibile trovare le informazioni per un'immagine:


|Metodo|DESCRIZIONE|
|---------|---------|
|Portale di Azure Stack|I valori vengono specificati automaticamente quando si seleziona un'immagine da usare.|
|PowerShell per Azure Stack|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|API REST     |[List image publishers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[List image offers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Immagine di elenco SKU](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

È possibile scegliere di caricare e utilizzare la propria immagine. In caso contrario, il nome del server di pubblicazione, l'offerta e sku non utilizzate.

### <a name="extensions"></a>Estensioni

Le estensioni VM offrono le funzionalità aggiuntive di macchina virtuale tramite post-configurazione di distribuzione e attività automatizzate.
È possibile eseguire le seguenti attività comuni tramite le estensioni:

* Eseguire gli script personalizzati: l'estensione Script personalizzata consente di configurare i carichi di lavoro della macchina virtuale eseguendo lo script quando viene eseguito il provisioning della macchina virtuale.
* Distribuire e gestire le configurazioni: estensione di PowerShell DSC Desired State Configuration () consente di impostare DSC in una macchina virtuale per gestire le configurazioni e ambienti.
* I dati di diagnostica raccolta: l'estensione di diagnostica di Azure consentono di configurare la macchina virtuale per raccogliere dati di diagnostica che possono essere utilizzati per monitorare l'integrità dell'applicazione.

### <a name="related-resources"></a>Risorse correlate

Le risorse nella tabella seguente vengono utilizzate dalla macchina virtuale e devono esistere o essere creata quando viene creata la macchina virtuale.


|Risorsa|Obbligatoria|DESCRIZIONE|
|---------|---------|---------|
|Gruppo di risorse|Sì|La VM deve essere contenuta in un gruppo di risorse.|
|Account di archiviazione|Sì|La VM richiede l'account di archiviazione per archiviare i dischi rigidi virtuali.|
|Rete virtuale|Sì|La VM deve appartenere a una rete virtuale.|
|Indirizzo IP pubblico|No |È possibile che la VM disponga di un indirizzo IP pubblico assegnato per accedervi in modalità remota.|
|interfaccia di rete|Sì|La VM richiede l'interfaccia di rete per comunicare in rete.|
|Dischi dati|No |La VM può includere dischi dati per espandere le funzionalità di archiviazione.|

## <a name="how-do-i-create-my-first-vm"></a>Come creare la prima VM

Sono disponibili diverse opzioni per creare una macchina virtuale. La scelta dipende dall'ambiente.
Nella tabella seguente fornisce informazioni per iniziare a creare la macchina virtuale.


|Metodo|Articolo|
|---------|---------|
|Portale di Azure Stack|Creare una macchina virtuale Windows con il portale di Azure Stack<br>[Creare una macchina virtuale Linux tramite il portale di Azure Stack](azure-stack-quick-linux-portal.md)|
|Modelli|Modelli di avvio rapido di Stack Azure si trovano in:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Creare una macchina virtuale Windows usando PowerShell nello Stack di Azure](azure-stack-quick-create-vm-windows-powershell.md)<br>[Creare una macchina virtuale Linux usando PowerShell nello Stack di Azure](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Creare una macchina virtuale Windows usando l'interfaccia CLI nello Stack di Azure](azure-stack-quick-create-vm-windows-cli.md)<br>[Creare una macchina virtuale Linux usando l'interfaccia CLI nello Stack di Azure](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Modalità di gestione della VM creata

Le VM possono essere gestite mediante un portale basato su browser, gli strumenti da riga di comando con il supporto per gli script o direttamente tramite l'API. Alcune attività di gestione tipiche consentono di visualizzare informazioni su una VM, accedere a una VM, gestire la disponibilità ed eseguire backup.

### <a name="get-information-about-a-vm"></a>Visualizzare informazioni su una macchina virtuale

La tabella seguente illustra alcuni modi per ottenere informazioni su una macchina virtuale.


|Metodo|DESCRIZIONE|
|---------|---------|
|Portale di Azure Stack|Nel menu hub fare clic su macchine virtuali e quindi selezionare la macchina virtuale dall'elenco. Nella pagina per la macchina virtuale, è possibile accedere a informazioni generali, impostare valori e metriche di monitoraggio.|
|Azure PowerShell|La gestione di macchine virtuali è simile in Azure e Azure Stack. Per ulteriori informazioni sull'utilizzo di PowerShell, vedere l'argomento di Azure seguente:<br>[Creare e gestire macchine virtuali di Windows con il modulo PowerShell di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Client SDK|Usa c# per gestire le macchine virtuali è simile in Azure e Azure Stack. Per ulteriori informazioni, vedere l'argomento di Azure seguente:<br>[Creare e gestire macchine virtuali di Windows in Azure utilizzando il linguaggio c#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Connettersi alla VM

È possibile utilizzare il **Connetti** pulsante nel portale di Azure Stack per connettersi alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
* [Considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md)

