---
title: Aggiornare un'offerta di app Power BI | Azure Marketplace
description: Aggiornare un'offerta di app Power BI dopo che è stata pubblicata in Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b210a945993457fa76703528178763fdc67e876b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141698"
---
# <a name="update-a-power-bi-app-offer"></a>Aggiornare un'offerta di app Power BI

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte di Power BI App al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [Power bi panoramica sulla creazione di app](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) .

Questo articolo illustra come aggiornare l'offerta dell'app Microsoft Power BI nel [portale cloud partner](https://cloudpartner.azure.com/) e quindi per ripubblicare l'offerta. Ecco alcuni motivi comuni per aggiornare un'offerta:

- Per aggiornare il contenuto dell'app in Power BI e ottenere un URL di installazione dall'app appena creata in pacchetto
- Per aggiornare i metadati di Azure Marketplace dell'offerta (vendite, marketing o informazioni di supporto e risorse)
 
Usare le funzionalità di **confronto** e di **cronologia** del portale per tenere traccia di modifiche simili.

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Alcuni attributi di un'offerta di app Power BI non possono essere modificati dopo la pubblicazione dell'offerta in AppSource. Le modifiche non consentite includono l' **ID offerta** e l' **ID editore**. Tuttavia, è comunque possibile modificare un'ampia gamma di caratteristiche. Di seguito sono riportate alcune modifiche comuni.

### <a name="update-app-content-in-power-bi"></a>Aggiornare il contenuto dell'app in Power BI

Le app Power BI vengono spesso aggiornate periodicamente con nuovo contenuto, patch di sicurezza, funzionalità e così via. Quando si modifica un'app, aggiornare l'URL di installazione attenendosi alla procedura seguente:

1.  Accedere al [portale cloud partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta da aggiornare.
3.  Nella scheda **Technical info (informazioni tecniche** ) immettere un nuovo URL di installazione.
4.  Selezionare **pubblica** per avviare il flusso di lavoro per pubblicare la nuova versione dell'app in AppSource.


### <a name="update-the-offers-marketplace-metadata"></a>Aggiornare i metadati del Marketplace dell'offerta

I metadati del Marketplace dell'offerta includono il nome della società, i logo e altre informazioni. Per aggiornare i metadati, attenersi alla seguente procedura:

1.  Accedere al [portale cloud partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta da aggiornare.
3.  Nella scheda **Dettagli storefront** modificare i metadati seguendo le istruzioni riportate nell'articolo [Power bi scheda Dettagli dell'app storefront](./cpp-storefront-details-tab.md).
4.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.


## <a name="the-compare-feature"></a>Funzionalità di confronto

Quando si aggiorna un'offerta pubblicata, è possibile controllare le modifiche apportate. Per usare la funzionalità di **confronto** :

1.  In qualsiasi momento del processo di modifica, selezionare il pulsante **Confronta** dell'offerta.

    ![Pulsante Confronta](./media/compare-feature-button.png)

2.  Visualizzare affiancate le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare la cronologia di pubblicazione dell'offerta, sul lato sinistro del portale Cloud Partner aprire la scheda **cronologia** . Qui viene visualizzata una cronologia delle azioni con timestamp sulle offerte AppSource.

## <a name="next-steps"></a>Passaggi successivi

Nel portale Cloud Partner, usare regolarmente [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) per trovare informazioni utili sui clienti e sull'utilizzo del Marketplace.  
