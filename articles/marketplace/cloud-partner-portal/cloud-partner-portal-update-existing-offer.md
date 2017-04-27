---
title: Aggiornare un&quot;offerta esistente per Azure Marketplace | Microsoft Docs
description: Questo articolo illustra i dettagli per l&quot;aggiornamento di un&quot;offerta esistente tramite Microsoft Cloud Partner
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 48424cb03c7fa521a2556ff00c2136eb5fae38ad
ms.lasthandoff: 04/12/2017


---
# <a name="update-an-existing-offer-for-azure-marketplace"></a>Aggiornare un'offerta esistente per Azure Marketplace
Ci sono diversi tipi di aggiornamenti che è possibile eseguire quando un'offerta è live.

1. Aggiunta di una nuova versione dell'immagine di macchina virtuale a una SKU esistente.
1. Cambiare le aree in cui è disponibile una SKU. 
1. Aggiunta di nuove SKU. 
1. Aggiornamento dei metadati del marketplace dell'offerta/SKU .

A tale scopo è necessario aggiornare l'offerta nel [portale di Cloud Partner](https://cloudpartner.azure.com/) e ripubblicarla. Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta della macchina virtuale.

## <a name="unpermitted-changes-to-vm-offersku"></a>Modifiche non consentite per l'offerta/SKU della macchina virtuale
Ci sono alcuni attributi dell'offerta/SKU della macchina virtuale che non possono essere modificati dopo che un'offerta è live in Azure Marketplace.
1. ID dell'offerta e ID dell'ente di pubblicazione dell'offerta.
2. ID SKU di SKU esistenti.
3. Aprire la configurazione di porte di SKU esistenti.
4. Numero di dischi dati di SKU esistenti.
5. Modifiche del modello di fatturazione/licenza per SKU esistenti.

## <a name="updating-the-vm-image-version-for-a-sku"></a>L'aggiornamento della versione dell'immagine di macchina virtuale per una SKU
L'immagine di una macchina virtuale per una SKU dell'offerta potrebbe essere stata aggiornata con funzionalità aggiuntive, patch di sicurezza e così via. In tali scenari, si vuole aggiornare l'immagine di una macchina virtuale a cui fa riferimento la SKU. A tale scopo, eseguire la procedura seguente:
1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/)
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare
3. Nel modulo **SKU** fare clic sulla SKU di cui si vuole aggiornare l'immagine di una macchina virtuale
4. In **Versione disco** fare clic su **+ Nuova versione disco** per aggiungere una nuova immagine di macchina virtuale
5. Specificare la **versione del disco** delle immagini di macchina virtuale. La versione del disco deve seguire il formato della [versione semantica](http://semver.org/). Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Assicurarsi che la nuova versione specificata sia più recente delle versioni precedenti, altrimenti non verrà visualizzata nel Portale di Azure o in Azure Marketplace al momento della pubblicazione.
6. Nell'**URL del disco rigido virtuale del sistema operativo** immettere [l'URI di firma di accesso condiviso](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) creato per il disco rigido virtuale del sistema operativo. Si noti che il numero di dischi dati non può cambiare tra versioni diverse della SKU. Se le versioni precedenti avevano dischi dati configurati, questa nuova versione deve anche avere dischi dati configurati. 
7. Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione per rendere live la nuova versione di macchina virtuale nel Portale di Azure e in Azure Marketplace. 

## <a name="changing-regions-a-sku-is-available-in"></a>Modifica delle aree in cui è disponibile una SKU
Con il tempo, si potrebbe voler pubblicare l'offerta/SKU in altre aree. In alternativa, si potrebbe voler interrompere il supporto per l'offerta/SKU in una determinata area. A tale scopo, eseguire la procedura seguente.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/)
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare
3. Nel modulo **SKU** fare clic sulla SKU di cui si vuole aggiornare la disponibilità delle aree
4. Fare clic sul pulsante **Selezionare paesi** che si trova sotto il campo **Disponibilità paese**. 
5. Nella finestra a comparsa delle aree disponibili, aggiungere o rimuovere le aree per questa SKU.
6. Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione per aggiornare la disponibilità delle aree delle SKU. 

Se una SKU è stata resa disponibile in una nuova area, sarà possibile specificare i prezzi per quella area specifica tramite la funzionalità di **esportazione dei dati dei prezzi**. Si noti che se si aggiunge nuovamente un'area che era disponibile prima, non sarà possibile aggiornare i rispettivi prezzi poiché non sono consentite modifiche ai prezzi. 

## <a name="adding-a-new-sku"></a>Aggiunta di una nuova SKU
È possibile rendere disponibile una nuova SKU per l'offerta esistente. A tale scopo, eseguire la procedura seguente.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/)
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare
3. Sotto il modulo **SKU** fare clic su **Aggiungi nuovo SKU** e specificare un **ID SKU** nella finestra a comparsa. 
4. Seguire i passaggi rimanenti come indicato [qui](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md).
6. Fare clic su **Pubblica** per avviare il flusso di lavoro per rendere la nuova SKU live.

## <a name="updating-offer-marketplace-metadata"></a>Aggiornamento dei metadati del marketplace dell'offerta.
È possibile avere scenari in cui è necessario aggiornare i metadati del marketplace associati all'offerta, ad esempio l'aggiornamento del logo della società e così via. A tale scopo, eseguire la procedura seguente.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/)
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare
3. Passare al modulo **Marketplace** e seguire le istruzioni illustrate [qui](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md) per apportare modifiche. 
4. Fare clic su **Pubblica** per avviare il flusso di lavoro per rendere le modifiche live.


