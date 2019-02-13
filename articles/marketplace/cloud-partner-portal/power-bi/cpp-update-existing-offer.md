---
title: Aggiornare un'offerta di app Power BI esistente - Azure Marketplace | Microsoft Docs
description: Aggiornare un'offerta di app Power BI dopo che è stata pubblicata in Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665702"
---
# <a name="update-an-existing-power-bi-app-offer"></a>Aggiornare un'offerta di app Power BI esistente

Questo articolo descrive in dettaglio i diversi aspetti dell'aggiornamento di un'offerta di app Power BI nel [portale Cloud Partner](https://cloudpartner.azure.com/) e della successiva ripubblicazione dell'offerta.  Si può decidere di aggiornare un'offerta per vari motivi, ad esempio:

- Aggiornamento del contenuto dell'app in Power BI e acquisizione di un nuovo URL di installazione dal nuovo pacchetto dell'app creato
- Aggiornamento dei metadati del marketplace per l'offerta: asset e informazioni di vendita, marketing o supporto
 
Per semplificare queste modifiche, il portale offre le funzionalità **Compare** (Confronta) e **History** (Cronologia).


## <a name="unpermitted-changes-to-offer"></a>Modifiche all'offerta non consentite

Dopo la pubblicazione di un'offerta di app Power BI in AppSource, alcuni attributi dell'offerta non possono essere modificati, in particolare **Offer ID** (ID offerta) e **Publisher ID** (ID editore).


## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le caratteristiche di un'offerta di app Power BI che è possibile modificare sono numerose. Le operazioni più comuni, tuttavia, sono le seguenti.


### <a name="update-app-content-in-power-bi"></a>Aggiornare il contenuto dell'app in Power BI

È normale che l'app in Power BI venga aggiornata periodicamente con nuovo contenuto, patch di sicurezza, funzionalità aggiuntive e così via. In tali scenari, è necessario aggiornare l'URL per l'installazione del nuovo pacchetto dell'app tramite la procedura seguente:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  In **All offers** (Tutte le offerte) individuare l'offerta da aggiornare.
3.  Nella scheda **Technical Info** (Informazioni tecniche) immettere l'URL del nuovo programma di installazione.
4.  Fare clic su **Publish** (Pubblica) per avviare il flusso di lavoro per la pubblicazione della nuova versione dell'app in AppSource.


### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace, ad esempio società nome, logo e così via, associati all'offerta, usare la procedura seguente:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  In **All offers** (Tutte le offerte) trovare l'offerta da aggiornare.
3.  Aprire la scheda **Storefront Details** (Dettagli vetrina) e seguire le istruzioni in [Scheda Storefront Details (Dettagli vetrina) delle app Power BI](./cpp-storefront-details-tab.md) per apportare modifiche ai metadati.
4.  Fare clic su **Publish** (Pubblica) per avviare il flusso di lavoro di pubblicazione delle modifiche.


## <a name="compare-feature"></a>Funzionalità Compare (Confronta)

Quando si apportano modifiche a un'offerta già pubblicata, è possibile usare la funzionalità **Compare** (Confronta) per controllare le modifiche apportate. Per usare questa funzionalità:

1.  In qualsiasi momento durante il processo di modifica fare clic sul pulsante **Compare** (Confronta) per l'offerta.

    ![Pulsante della funzionalità Compare (Confronta)](./media/compare-feature-button.png)

2.  Visualizzare affiancate le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare la cronologia di eventuali attività di pubblicazione precedenti, fare clic sulla scheda **History** (Cronologia) sulla barra dei menu di spostamento a sinistra del portale Cloud Partner. Qui sarà possibile visualizzare le azioni con timestamp che sono state eseguite durante il ciclo di vita delle offerte in AppSource.


## <a name="next-steps"></a>Passaggi successivi

È consigliabile usare regolarmente la funzionalità [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) (Informazioni dettagliate per il venditore) del [portale Cloud Partner](https://cloudpartner.azure.com/#insights) per visualizzare informazioni dettagliate sui clienti e sull'uso del marketplace.  
