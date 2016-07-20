<properties
   pageTitle="Panoramica su come creare e distribuire un'offerta in Marketplace | Microsoft Azure"
   description="Informazioni sui passaggi necessari a diventare uno sviluppatore Microsoft approvato e creare e distribuire un'immagine di macchina virtuale, un modello, un servizio dati o un servizio per sviluppatori in Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Come pubblicare e gestire un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare uno sviluppatore a creare e distribuire e gestire le proprie soluzioni disponibili in Azure Marketplace che altri clienti e partner di Azure possono acquistare e usare.

La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. L’Azure Marketplace supporta varie soluzioni e ognuna di esse richiede un set di lavoro leggermente diverso per la corretta pubblicazione in Marketplace.

## Tipi di offerte
|Tipo di offerta| Definizione |
|---|---|
|Immagine di macchina virtuale | Immagine di macchina virtuale (VM) preconfigurata con un sistema operativo completamente installato e una o più applicazioni. Le offerte Immagine di macchina virtuale possono includere una singola immagine o più immagini di macchina virtuale associate tra loro tramite un modello di soluzione. Un'immagine di macchina virtuale ("Immagine") fornisce le informazioni necessarie per creare e distribuire macchine virtuali nel servizio Macchine virtuali di Azure. Un'Immagine comprende un disco rigido virtuale di sistema operativo e zero o più dischi rigidi virtuali di dati. I Clienti possono distribuire un numero qualsiasi di macchine virtuali da una singola Immagine.|
|Servizio per gli sviluppatori| Servizi completamente gestiti per Information Worker, business analyst, sviluppatori o professionisti IT, da usare per lo sviluppo di applicazioni dei clienti o la gestione di sistemi. I servizi per gli sviluppatori offrono funzionalità che permettono ai clienti di sviluppare rapidamente applicazioni per il cloud in Azure. Per acquistare i servizi per gli sviluppatori, i clienti devono essere titolari di una sottoscrizione di Azure. Gli editori sono responsabili della misurazione dell'utilizzo da parte dei clienti dei servizi per gli sviluppatori e della comunicazione a Microsoft delle informazioni sull'utilizzo, ai sensi di quanto specificato nel Contratto per la pubblicazione in Microsoft Azure Marketplace.|
|Modello di soluzione|Un "modello di soluzione Azure Resource Manager" è una struttura di dati che può fare riferimento a una o più offerte distinte, incluse le offerte pubblicate da altri autori, per consentire ai clienti di Azure di distribuire una o più offerte in unico modo coordinato.|

Alcuni passaggi sono condivisi tra i diversi tipi di soluzioni. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

## 1\. Prerequisiti

> [AZURE.NOTE] Prima di iniziare le operazioni in Azure Marketplace, è necessario avere ottenuto la pre-approvazione. Questo non è applicabile per gli editori di servizi dati.

1. [Ottenere la pre-approvazione tramite il programma Microsoft Azure Certified](marketplace-publishing-azure-certification.md)
2. [Creare un account di Microsoft Developer](marketplace-publishing-accounts-creation-registration.md)
3. [Rispettare i prerequisiti non tecnici](marketplace-publishing-pre-requisites.md)

## 2\. Pubblicazione dell'offerta
### 2\.1 Completare i prerequisiti tecnici specifici per l'offerta
- [Prerequisiti tecnici per le macchine virtuali](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Prerequisiti tecnici per il modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 Creare l'offerta
1. Creare l'offerta usando le guide seguenti specifiche per l'offerta.
    - [Creare l'offerta per la macchina virtuale](marketplace-publishing-vm-image-creation.md)
    - [Creare l'offerta per il modello di soluzione](marketplace-publishing-solution-template-creation.md)
2. [Creare il contenuto marketing per l'offerta](marketplace-publishing-push-to-staging.md)

### 2\.3 Testare l'offerta VM nello staging
- [Testare l'offerta VM nello staging](marketplace-publishing-vm-image-test-in-staging.md)
- [Test dell'offerta di modello di soluzione nello staging](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Distribuire l'offerta nel Marketplace
- [Distribuire l'offerta in Azure Marketplace](marketplace-publishing-push-to-production.md)

### Informazioni specifiche per immagini di macchine virtuali ###
- [Creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [Risoluzione dei problemi di pubblicazione comuni in Marketplace](marketplace-publishing-support-common-issues.md)
- Per altre informazioni sui portali usati, vedere [Portali necessari](marketplace-publishing-portals.md)


## 3\. Gestione post-pubblicazione dell'offerta
- [Guida di post-produzione per le offerte di macchine virtuali](marketplace-publishing-vm-image-post-publishing.md)
- [Come aggiornare i dati non tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Come eliminare un'offerta o uno SKU attivo da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [Come modificare l'incentivo per i rivenditori per i Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
- [Descrizione del report sulle informazioni dettagliate del venditore](marketplace-publishing-report-seller-insights.md)
- [Informazioni sui report sui proventi di Azure Marketplace](marketplace-publishing-report-payout.md)
- [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)

## Risorse aggiuntive
- [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->