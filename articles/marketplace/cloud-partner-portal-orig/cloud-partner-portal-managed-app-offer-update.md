---
title: Aggiornare un'offerta esistente per Azure Marketplace
description: Aggiornare un'offerta esistente per Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807567"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aggiornare un'offerta esistente per Azure Marketplace 
==============================================

Ci sono diversi tipi di aggiornamenti che è possibile eseguire quando un'offerta è attiva.

1.  Aggiunta di un nuovo \"pacchetto\" a uno SKU esistente
2.  Aggiunta di nuovi SKU a un'offerta esistente
3.  Aggiornamento dei metadati del marketplace di offerta/SKU

È necessario aggiornare l'offerta nel portale Cloud Partner e ripubblicarla. Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta di un'applicazione Azure.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Modifiche non consentite a offerta/SKU dell'applicazione Azure 
--------------------------------------------------

Ci sono alcuni attributi dell'offerta/SKU dell'applicazione Azure che non possono essere modificati dopo che un'offerta è stata attivata in Azure Marketplace.

1.  ID dell'offerta e ID dell'ente di pubblicazione dell'offerta.
2.  ID SKU di SKU esistenti.
3.  Aggiornamento di un pacchetto pubblicato.

<a name="adding-a-new-package-to-an-existing-sku"></a>Aggiunta di un nuovo pacchetto a uno SKU esistente 
---------------------------------------

È possibile che l'editore voglia aggiungere una nuova versione del pacchetto per aggiornare il pacchetto esistente. È possibile eseguire questa operazione caricando un nuovo pacchetto con un numero di versione diverso.

1.  Accedere al [portale Cloud Partner](http://cloudpartner.azure.com)
2.  In All offers (Tutte le offerte) individuare l'offerta che si vuole aggiornare
3.  Nel modulo SKUs (SKU) fare clic sullo SKU per il quale si vuole aggiornare il pacchetto
4.  Fare clic su \"New Package\" (Nuovo pacchetto) e specificare una nuova versione
5.  Caricare un nuovo file ZIP contenente il file modello aggiornato
6.  Fare clic su Publish (Pubblica) per avviare il flusso di lavoro di pubblicazione e attivare il nuovo SKU.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Aggiunta di un nuovo SKU a un'offerta esistente
-------------------------------------

È possibile rendere disponibile un nuovo SKU per l'offerta esistente. A tale scopo, eseguire la procedura seguente.

1.  Accedere al [portale Cloud Partner](http://cloudpartner.azure.com)
2.  In All offers (Tutte le offerte) individuare l'offerta che si vuole aggiornare
3.  Nel modulo SKUs (SKU) fare clic su Add new SKU (Aggiungi nuovo SKU) e specificare un ID SKU nel popup.
4.  Seguire i passaggi rimanenti come indicato [qui](./cloud-partner-portal-managed-app-publish.md).
5.  Fare clic su Publish (Pubblica) per avviare il flusso di lavoro di pubblicazione e attivare il nuovo SKU.

<a name="updating-offer-marketplace-metadata"></a>Aggiornamento dei metadati del marketplace dell'offerta 
-----------------------------------

È possibile avere scenari in cui è necessario aggiornare i metadati del marketplace associati all'offerta, ad esempio l'aggiornamento del logo della società e così via. Attenersi alla procedura descritta di seguito.

1.  Accedere al portale Cloud Partner
2.  In All offers (Tutte le offerte) individuare l'offerta che si vuole aggiornare
3.  Passare al modulo Marketplace e seguire le istruzioni presenti [qui](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) per apportare le modifiche.
4.  Fare clic su Publish (Pubblica) per avviare il flusso di lavoro di pubblicazione e attivare le modifiche.
