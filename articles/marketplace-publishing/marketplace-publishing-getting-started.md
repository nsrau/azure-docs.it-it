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
   ms.date="08/22/2016"
   ms.author="hascipio" />

# Come pubblicare e gestire un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare uno sviluppatore a creare e distribuire e gestire le proprie soluzioni disponibili in Azure Marketplace che altri clienti e partner di Azure possono acquistare e usare.

## Cos'è Azure Marketplace?
Azure Marketplace è dove un sottoscrittore di Azure può trovare servizi per semplificare lo sviluppo di soluzioni e applicazioni locali o su cloud. E usare i servizi [Azure Certified](http://azure.com/certified) come blocchi predefiniti per lo sviluppo di un'applicazione o un servizio innovativo per la propria linea di business e altri sottoscrittori di Azure.

In quanto server di pubblicazione di Azure, Azure Marketplace consente di distribuire e vendere soluzioni o servizi innovativi ad altri sviluppatori, ISV e professionisti IT che vogliono sviluppare rapidamente applicazioni basate su cloud e soluzioni per dispositivi mobili.

## Tipi di offerte supportate
La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. Azure Marketplace supporta tre tipi di offerte:

- Le **immagini di macchina virtuale** sono immagini preconfigurate con installato un sistema operativo e una o più applicazioni. Un'immagine di macchina virtuale fornisce le informazioni necessarie per creare e distribuire macchine virtuali nel servizio Macchine virtuali di Azure.

    >[AZURE.NOTE] **Ad esempio**, come autori di Azure, potreste aver creato e convalidato una VM con un servizio di database innovativo e interessante al punto che altri sottoscrittori di Azure sono disposti a procurarsi e distribuire questa VM nei loro ambienti di servizio cloud.

- I **servizi per gli sviluppatori** sono servizi completamente gestiti da usare per lo sviluppo di applicazioni o per la gestione di sistema. Offrono funzionalità che consentono il rapido sviluppo di applicazioni di livello cloud in Azure.

    >[AZURE.NOTE] **Ad esempio**, come autori di Azure, potreste aver sviluppato un servizio accessibile mediante API (ospitato su Azure o altrove) che fornisce previsioni in base ai dati storici. Si tratta di un servizio che potrebbe risultare utile ad altri sottoscrittori di Azure che creano soluzioni. È possibile distribuire questo servizio in Azure Marketplace in modo che altri possano trovarlo, ottenerlo e usarlo nei loro servizi.

- Un **modello di soluzione** è una struttura di dati che può fare riferimento a uno o più servizi di Azure distinti, inclusi quelli pubblicati da altri venditori, per consentire ai sottoscrittori di Azure di distribuire una o più offerte in un unico modo coordinato.

    >[AZURE.NOTE] **Ad esempio**, come autori di Azure, potreste aver creato un pacchetto contenente un set di servizi, provenienti da vari ambiti di Azure, che velocizza la distribuzione di un servizio cloud sicuro e ad alta disponibilità con bilanciamento del carico mediante pochi clic. Altri sottoscrittori di Azure potrebbero voler ottenere questo modello di soluzione per risparmiare tempo, anziché identificare e configurare manualmente gli stessi servizi di Azure o servizi di Azure simili.

Alcuni passaggi sono condivisi tra i diversi tipi di soluzioni. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

## 1\. Prerequisiti

> [AZURE.NOTE] Prima di iniziare qualsiasi operazione in Azure Marketplace, è necessario avere ottenuto la [pre-approvazione](http://azure.com/certified).

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
- [Come aggiornare i dati tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
- [Come aggiungere un nuovo SKU in un'offerta elencata](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
- [Come modificare il numero dei dischi dati per uno SKU elencato](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
- [Come eliminare un'offerta elencata da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#5-how-to-delete-a-listed-offer-from-the-azure-marketplace)
- [Come eliminare uno SKU elencato da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
- [Come eliminare la versione corrente di uno SKU elencato da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
- [Come ripristinare il prezzo elencato ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
- [Come ripristinare il modello di prezzi ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
- [Come ripristinare l'impostazione di visibilità di uno SKU elencato al valore di produzione](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
- [Come modificare l'incentivo per i rivenditori per i Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
- [Descrizione del report sulle informazioni dettagliate del venditore](marketplace-publishing-report-seller-insights.md)
- [Informazioni sui report sui proventi di Azure Marketplace](marketplace-publishing-report-payout.md)
- [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)

## Risorse aggiuntive
- [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0824_2016-->