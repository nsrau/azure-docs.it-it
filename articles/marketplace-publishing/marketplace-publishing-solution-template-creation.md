---
title: Guida alla creazione di un modello di soluzione per il Marketplace | Documentazione Microsoft
description: "Istruzioni dettagliate su come creare, certificare e distribuire un modello di soluzione con un'immagine con più macchine virtuali per l'acquisto in Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: b753bfb4bd69bd9aacf4eebd8999397394c28bc4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guida alla creazione di un modello di soluzione per Azure Marketplace
Dopo aver completato il passaggio 1, [Creazione e registrazione dell'account][link-acct-creation], sono state fornite indicazioni per la creazione di un modello di soluzione compatibile con Azure in [Prerequisiti tecnici per la creazione di un modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md). Ora verranno illustrati i passaggi per la creazione di un modello di soluzione per più macchine virtuali nel [portale di pubblicazione][link-pubportal] per Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Creare l'offerta di modello di soluzione nel portale di pubblicazione
Passare alla pagina [https://publish.windowsazure.com](http://publish.windowsazure.com). Quando si accede per la prima volta al [portale di pubblicazione](https://publish.windowsazure.com/), usare lo stesso account con cui è stato registrato il profilo venditore dell'azienda. Successivamente è possibile aggiungere qualsiasi dipendente dell'azienda come coamministratore nel portale di pubblicazione.

### <a name="1-select-solution-templates"></a>1. Selezionare "Modelli di soluzione"
  ![disegno][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Creare un nuovo modello di soluzione
  ![disegno][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Iniziare con le topologie
Un modello di soluzione è un elemento padre per tutte le relative topologie. È possibile definire più topologie in un singolo modello di soluzione/offerta. Quando un'offerta passa alla fase di gestione temporanea, passano a tale fase anche tutte le relative topologie. Per definire l'offerta, eseguire i passaggi seguenti:     

* Creare una topologia. "Identificatore topologia" è in genere il nome della topologia per il modello di soluzione. L'identificatore topologia viene usato nell'URL come illustrato di seguito:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portale di Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Aggiungere una nuova versione

### <a name="4-get-your-topology-versions-certified"></a>4. Ottenere la certificazione per le versioni della topologia
Caricare un file ZIP contenente tutti i file necessari per eseguire il provisioning della specifica versione della topologia. Il file zip deve contenere quanto segue:

* File *mainTemplate.json* e *createUiDefinition.json* nella directory radice.
* Tutti i modelli collegati e tutti gli script necessari.

  > [!TIP]
  > Mentre gli sviluppatori lavorano alla creazione delle topologie del modello di soluzione e alla relativa certificazione, il reparto commerciale, marketing e/o legale dell'azienda può occuparsi dei contenuti di marketing e legali.
  >
  >

## <a name="next-steps"></a>Passaggi successivi
Ora che il modello di soluzione è stato creato e il file zip caricato, seguire le istruzioni di [guida sui contenuti di marketing di Azure Marketplace](marketplace-publishing-push-to-staging.md) prima di eseguire il passaggio dell'offerta alla gestione temporanea. Per visualizzare il set completo di articoli di pubblicazione nel Marketplace, visitare [Come pubblicare e gestire un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md).

Articoli correlati:

* Immagini di macchina virtuale: [Informazioni sulle immagini di macchine virtuali in Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Estensioni di macchina virtuale: [Panoramica sull'agente VM e sulle estensioni VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [Estensioni VM e funzionalità di Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Semplici modelli di esempio](https://github.com/rjmax/ArmExamples)
* Limitazioni degli account di archiviazione: [Post di blog relativo al monitoraggio della limitazione degli account di archiviazione](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [Archiviazione Premium](../storage/common/storage-premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
