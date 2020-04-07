---
title: Aggiornamento di un'offerta di Power BI Per l'app Azure Marketplace
description: Aggiornare un'offerta di app Power BI dopo che è stata pubblicata in Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 02f2f8ce523e9316b749a9639112176a74da66d2
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745540"
---
# <a name="update-a-power-bi-app-offer"></a>Aggiornare un'offerta dell'app Power BI

>[!Important]
>A partire dal 30 marzo 2020, inizieremo a spostare la gestione delle offerte dell'app Power BI nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Panoramica](https://aka.ms/AzureCreatePBIServiceApp) sulla creazione di app Power BI per gestire le offerte migrate.

Questo articolo illustra come aggiornare l'offerta dell'app Microsoft Power BI nel [portale per i partner cloud](https://cloudpartner.azure.com/) e quindi ripubblicare l'offerta. Ecco alcuni motivi comuni per aggiornare un'offerta:

- Per aggiornare il contenuto dell'app in Power BI e ottenere un URL di installazione dall'app appena inserita nel pacchettoTo update the app's content in Power BI and get an installation URL from the newly packaged app
- Per aggiornare i metadati di Azure Marketplace dell'offerta (informazioni su vendite, marketing o supporto e risorse)
 
Utilizzare le funzionalità **Confronto** e **cronologia** del portale per tenere traccia di modifiche di questo tipo.

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Alcuni attributi di un'offerta di Power BI App non possono essere modificati dopo la pubblicazione dell'offerta in AppSource.Some attributes of a Power BI App offer can't be changed after the offer is published in AppSource. Le modifiche non consentite includono **ID offerta** e **ID editore**. Ma è ancora possibile modificare una vasta gamma di caratteristiche. Ecco alcune modifiche comuni.

### <a name="update-app-content-in-power-bi"></a>Aggiornare il contenuto dell'app in Power BI

Le app Power BI vengono spesso aggiornate periodicamente con nuovi contenuti, patch di sicurezza, funzionalità e così via. Quando un'app viene modificata, aggiorna il relativo URL di installazione attenendoti alla seguente procedura:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta da aggiornare.
3.  Nella scheda **Informazioni tecniche** immettere un nuovo URL di installazione.
4.  Selezionare Pubblica per avviare il flusso di lavoro per pubblicare la nuova versione dell'app in AppSource.Select **Publish** to start the workflow to publish the new app version to AppSource.


### <a name="update-the-offers-marketplace-metadata"></a>Aggiornare i metadati marketplace dell'offerta

I metadati Marketplace della tua offerta includono il nome della tua azienda, i loghi e altre informazioni. Per aggiornare i metadati, attenersi alla seguente procedura:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta da aggiornare.
3.  Nella scheda **Dettagli Storefront** modificare i metadati seguendo le istruzioni nell'articolo Dettagli Storefront di [Power BI.](./cpp-storefront-details-tab.md)
4.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.


## <a name="the-compare-feature"></a>La funzione Confronta

Quando aggiorni un'offerta pubblicata, puoi controllare le modifiche apportate. Per utilizzare la funzione **Confronta:**

1.  In qualsiasi momento del processo di modifica, seleziona il pulsante **Confronta** dell'offerta.

    ![Pulsante Confronta](./media/compare-feature-button.png)

2.  Visualizzare affiancate le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare la cronologia di pubblicazione dell'offerta, sul lato sinistro del portale Cloud Partner aprire la scheda **Cronologia.** Qui vedrai una cronologia delle azioni con timestamp sulle tue offerte AppSource.

## <a name="next-steps"></a>Passaggi successivi

Nel portale Cloud Partner, utilizza regolarmente [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) per trovare informazioni utili sui clienti e sull'utilizzo del Marketplace.  
