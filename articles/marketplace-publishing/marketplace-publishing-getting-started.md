---
title: Panoramica su come creare e distribuire un&quot;offerta in Marketplace | Documentazione Microsoft
description: Informazioni sui passaggi necessari per diventare uno sviluppatore Microsoft approvato e creare e distribuire un&quot;immagine di macchina virtuale, un modello, un servizio dati o un servizio per sviluppatori in Azure Marketplace
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
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 8fbf201343f6710d2781a4b56ae54833ed4c06cf
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Pubblicare e gestire un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare gli sviluppatori a creare, distribuire e gestire le loro soluzioni disponibili in Azure Marketplace che altri clienti e partner di Azure possono acquistare e usare.

## <a name="marketplace-publishing"></a>Pubblicazione sul Marketplace
Come editore di Azure, l'utente può distribuire e vendere le soluzioni innovative o un servizio ad altri sviluppatori, agli ISV e ai professionisti IT nel Marketplace. Tramite il Marketplace, è possibile contattare i clienti che desiderano sviluppare rapidamente le loro applicazioni basate su cloud e le soluzioni per dispositivi mobili. Se la soluzione è destinata agli utenti aziendali, si consiglia di prendere in considerazione il marketplace [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipi di soluzioni supportati
La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. Il Marketplace supporta i tipi di offerte seguenti:

|Tipo di soluzione|Macchine virtuali|Modello di soluzione|
|---|---|---|
|**Definizione**|Immagini preconfigurate con un sistema operativo completamente installato e una o più applicazioni. Un'immagine di macchina virtuale fornisce le informazioni necessarie per creare e distribuire macchine virtuali nel servizio Macchine virtuali di Azure.|Una struttura di dati che può fare riferimento a uno o più servizi di Azure distinti, compresi i servizi pubblicati da altri venditori. I sottoscrittori di Azure possono usarlo per distribuire una o più offerte in un modo coordinato e individuale.|
|**Esempio**|In qualità di editore di Azure, l'utente ha creato e convalidato una macchina virtuale con un servizio di database innovativo. Altri sottoscrittori di Azure desiderano ottenere e distribuire questa macchina virtuale in ambienti di servizio cloud.|Come server di pubblicazione Azure, è stato reso disponibile un set di servizi in Azure che accelerano la distribuzione di servizi cloud con bilanciamento del carico, sicurezza avanzata e disponibilità elevata. Altri sottoscrittori di Azure possono risparmiare tempo trovando il modello di soluzione che soddisfa il proprio obiettivo. Non devono individuare, ottenere, distribuire e configurare manualmente gli stessi servizi di Azure o altri simili.|

> [!NOTE]
> Alcuni passaggi sono condivisi tra diversi tipi di soluzioni e altri sono distinti per il tipo di soluzione. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

## <a name="publish-a-solution"></a>Pubblicare una soluzione
![Candidare, registrare, pubblicare](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Proporre la soluzione per l'approvazione
Per pubblicare una [soluzione](https://createopportunity.azurewebsites.net) per macchina virtuale nel Marketplace, completare il **Solution Nomination Form** (Modulo di candidatura della soluzione) certificato da Microsoft Azure.

>[!NOTE]
> Se si lavora con un Partner Account Manager o un DX Partner Manager, chiedere loro di candidare la soluzione per il programma Azure Certified. Si può anche passare alla pagina Web [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) (Certificazioni Microsoft Azure) e compilare il modulo di richiesta. Inserire l'indirizzo di posta elettronica del Partner Account Manager o del DX Partner Manager nella casella **Microsoft Sponsor Contact** (Contatto dello sponsor Microsoft).

Se sono soddisfatti i criteri di idoneità per le [politiche di partecipazione ad Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) e l'applicazione è approvata, la collaborazione tra Microsoft e l'utente inizia subito per inserire la soluzione nel Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrare l'account come venditore Microsoft
Registrare l'account Microsoft come [account di Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Pubblicare la soluzione
Per pubblicare una soluzione nel Marketplace, seguire la procedura seguente:
1. Soddisfare i requisiti non tecnici.

    a. Soddisfare i [requisiti non tecnici](marketplace-publishing-pre-requisites.md).

    b. Soddisfare i [requisiti tecnici della macchina virtuale](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Soddisfare i [requisiti tecnici del modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Creare l'offerta.

    a. Creare un'offerta di [macchina virtuale](marketplace-publishing-vm-image-creation.md).

    b. Creare un'offerta del [modello di soluzione](marketplace-publishing-solution-template-creation.md).

3. Creare il [contenuto marketing](marketplace-publishing-push-to-staging.md) dell'offerta.

4. Testare l'offerta nella fase di gestione temporanea.

    a. Testare l'offerta VM [nella fase di gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testare l'offerta del modello di soluzione nella [fase di gestione temporanea](marketplace-publishing-solution-template-test-in-staging.md).

5. Distribuire l'offerta nel [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Creare e gestire un'immagine di macchina virtuale
Creare e gestire un'immagine di macchina virtuale con le risorse seguenti:
* Creare un'immagine di macchina virtuale [in locale](marketplace-publishing-vm-image-creation-on-premise.md).
* Creare una macchina virtuale con [Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Creare una macchina virtuale con [Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Risolvere problemi comuni incontrati durante la [creazione del disco rigido virtuale](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Gestire la soluzione
Gestire la soluzione con l'aiuto nelle risorse seguenti:
* [Leggere la guida di post-produzione per le offerte di macchine virtuali](marketplace-publishing-vm-image-post-publishing.md)
* [Aggiornare i dati non tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Aggiornare i dati tecnici di un'offerta o di uno SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Aggiungere un nuovo SKU in un'offerta elencata](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Modificare il numero dei dischi dati per uno SKU elencato](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Eliminare un'offerta elencata dal Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Eliminare uno SKU elencato da Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Eliminare la versione corrente di uno SKU elencato dal Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Ripristinare il prezzo elencato ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Ripristinare il modello di prezzi ai valori di produzione](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Ripristinare l'impostazione di visibilità di uno SKU elencato al valore di produzione](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Visualizzare e modificare il Cloud Solution Provider "Incentivi rivenditore" in Azure Marketplace](marketplace-publishing-csp-incentive.md)
* [Informazioni sui report relativi ai proventi](marketplace-publishing-report-payout.md)
* [Ottenere supporto come editore](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Risorse aggiuntive
[Configurare Azure PowerShell](marketplace-publishing-powershell-setup.md)

