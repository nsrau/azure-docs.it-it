---
title: Panoramica su come creare e distribuire un&quot;offerta in Marketplace | Documentazione Microsoft
description: Informazioni sui passaggi necessari a diventare uno sviluppatore Microsoft approvato e creare e distribuire un&quot;immagine di macchina virtuale, un modello, un servizio dati o un servizio per sviluppatori in Azure Marketplace
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: b76185c0a4a0e17b663affee9a02b65f222fedeb
ms.openlocfilehash: d679096476406831c1fda4f695adff84e63d6ae8


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Come pubblicare e gestire un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare uno sviluppatore a creare, distribuire e gestire le sue soluzioni disponibili in Azure Marketplace che altri clienti e partner di Azure possono acquistare e usare.

## <a name="what-is-the-azure-marketplace"></a>Cos'è Azure Marketplace?
In quanto server di pubblicazione di Azure, Azure Marketplace consente di distribuire e vendere soluzioni o servizi innovativi ad altri sviluppatori, ISV e professionisti IT che vogliono sviluppare rapidamente applicazioni basate su cloud e soluzioni per dispositivi mobili. Se la soluzione è destinata agli utenti aziendali, si consiglia di prendere in considerazione il marketplace [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipi di soluzioni supportati
La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. Azure Marketplace supporta i tipi di offerte seguenti:

|Tipo di soluzione|Macchina virtuale|Modello di soluzione|
|---|---|---|
|Definizione|Immagini preconfigurate con un sistema operativo completamente installato e una o più applicazioni. Un'immagine di macchina virtuale fornisce le informazioni necessarie per creare e distribuire macchine virtuali nel servizio Macchine virtuali di Azure.|Struttura di dati che può fare riferimento a uno o più servizi di Azure distinti, inclusi quelli pubblicati da altri venditori, per consentire ai sottoscrittori di Azure di distribuire una o più offerte in un unico modo coordinato.|
|Esempio|**Ad esempio** , come autori di Azure, potreste aver creato e convalidato una VM con un servizio di database innovativo e interessante al punto che altri sottoscrittori di Azure sono disposti a procurarsi e distribuire questa VM nei loro ambienti di servizio cloud.|**Ad esempio,** come server di pubblicazione Azure, è stato reso disponibile un set di servizi in Azure che accelerano la distribuzione di servizi cloud con bilanciamento del carico, sicurezza avanzata e disponibilità elevata. Altri sottoscrittori di Azure possono risparmiare tempo grazie a modelli di soluzioni che soddisfano gli obiettivi, anziché individuare, fornire, distribuire e configurare manualmente lo stesso servizio o servizi di Azure simili.|

> [!NOTE]
> Si noti che alcuni passaggi sono condivisi tra diversi tipi di soluzioni e altri sono distinti per il tipo di soluzione. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

## <a name="how-to-publish-a-solution"></a>Come pubblicare una soluzione
![disegno](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. Proporre la soluzione per l'approvazione
- Completare il modulo per la nomina della soluzione per **Microsoft Azure Certified per macchine virtuali** [qui](https://createopportunity.azurewebsites.net)

>[!NOTE]
> Se si lavora con un gestore di account partner o con un gestore di partner sviluppatori, chiedere di proporre la soluzione per il programma Azure Certified OPPURE accedere alla pagina Web [Microsoft Azure Certified](http://createopportunity.azurewebsites.net), compilare il modulo di richiesta e immettere l'indirizzo di posta elettronica del gestore di account partner o di partner sviluppatori nel campo relativo al contatto dello sponsor Microsoft.

Se sono soddisfatti i criteri di idoneità per le [Politiche di partecipazione ad Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) e l'applicazione è approvata, la collaborazione tra Microsoft e l'utente inizierà subito per inserire la soluzione in Azure Marketplace.

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2. Registrare l'account come venditore Microsoft
- [Registrare l'account Microsoft come account di Microsoft Developer](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. Pubblicare la soluzione
1. Soddisfare i requisiti non tecnici
  - [Rispettare i prerequisiti non tecnici](marketplace-publishing-pre-requisites.md)
  - [Prerequisiti tecnici per le macchine virtuali](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [Prerequisiti tecnici per il modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md)
2. Creare l'offerta
  - [Macchina virtuale](marketplace-publishing-vm-image-creation.md)
  - [Modello di soluzione](marketplace-publishing-solution-template-creation.md)
3. [Creare il contenuto marketing per l'offerta](marketplace-publishing-push-to-staging.md)
4. Testare l'offerta nello staging
  - [Testare l'offerta VM nello staging](marketplace-publishing-vm-image-test-in-staging.md)
  - [Test dell'offerta di modello di soluzione nello staging](marketplace-publishing-solution-template-test-in-staging.md)
5. [Distribuire l'offerta in Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>Informazioni specifiche per immagini di macchine virtuali
* [Creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creare una macchina virtuale con Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creare una macchina virtuale con Linux nel portale di Azure](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Troubleshooting common issues encountered during VHD creation](marketplace-publishing-vm-image-creation-troubleshooting.md) (Risoluzione di problemi comuni incontrati durante la creazione del disco rigido virtuale)

## <a name="how-to-manage-your-solution"></a>Come gestire la soluzione
* [Guida di post-produzione per le offerte di macchine virtuali](marketplace-publishing-vm-image-post-publishing.md)
* [Come aggiornare i dati non tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Come aggiornare i dati tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Come aggiungere un nuovo SKU in un'offerta elencata](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Come modificare il numero dei dischi dati per uno SKU elencato](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Come eliminare un'offerta elencata da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Come eliminare uno SKU elencato da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Come eliminare la versione corrente di uno SKU elencato da Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Come ripristinare il prezzo elencato ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Come ripristinare il modello di prezzi ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Come ripristinare l'impostazione di visibilità di uno SKU elencato al valore di produzione](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Come modificare l'incentivo per i rivenditori per i Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
* [Descrizione del report sulle informazioni dettagliate del venditore](marketplace-publishing-report-seller-insights.md)
* [Informazioni sui report sui proventi di Azure Marketplace](marketplace-publishing-report-payout.md)
* [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)



<!--HONumber=Jan17_HO1-->


