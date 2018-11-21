---
title: SKU per un'immagine di contenitori di Azure | Microsoft Docs
description: Configurare SKU per un contenitore di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683979"
---
# <a name="container-skus-tab"></a>Scheda SKU del contenitore

La scheda **SKU** della pagina **Nuova offerta** consente di creare uno o più SKU e di associarli alla nuova offerta.  È possibile usare vari SKU per differenziare una soluzione per set di funzionalità, modelli di fatturazione o altre caratteristiche.

## <a name="sku-settings"></a>Impostazioni dello SKU

In fase di creazione, una nuova offerta non ha alcuno SKU associato. Per creare un nuovo SKU, seguire questa procedura:

1. Nella scheda SKU selezionare **Nuovo SKU**

   ![Richiesta nuovo SKU](./media/containers-sku-settings.png)

2. Immettere le informazioni necessarie relative allo SKU e al contenitore. Ogni SKU corrisponde a un'immagine del contenitore. Uno SKU si compone di due parti:

    -   Metadati dello SKU
    -   Metadati del contenitore

### <a name="sku-metadata"></a>Metadati dello SKU

I metadati dello SKU contengono informazioni di visualizzazione vetrina per l'elenco di contenitori.

![Metadati dello SKU](./media/containers-sku-details.png)

### <a name="container-metadata"></a>Metadati del contenitore

I metadati del contenitore includono informazioni di riferimento dei dettagli del repository di immagini all'interno del Registro contenitori di Azure. Azure Marketplace copia questa immagine in un registro pubblico specifico del Marketplace e quindi la rende disponibile per i clienti dopo la certificazione. Tutte le richieste dell'utente di Azure per l'utilizzo di un'immagine del contenitore di Azure Marketplace vengono rese disponibili dal registro pubblico del Marketplace e non dal Registro contenitori di Azure.

![Metadati del contenitore](./media/containers-image-repository.png)
    
I **dettagli del repository di immagini** illustrati nella precedente acquisizione di schermata includono i campi seguenti:

-   **ID sottoscrizione**: l'ID della sottoscrizione di Azure in cui si trova il Registro contenitori di Azure.
-   **Nome del gruppo di risorse**: il nome del gruppo di risorse del Registro contenitori di Azure.
-   **Nome del Registro**: il nome del Registro contenitori di Azure.
-   **Nome del repository**: il nome del repository. Dopo aver impostato il nome, questo valore non può più essere modificato. Usare un nome univoco per evitare conflitti con altre offerte nell'account.
-   **Nome utente**: il nome utente (nome utente amministratore) associato al Registro contenitori di Azure.
-   **Password**: la password associata al Registro contenitori di Azure.

    >[!NOTE]
    >Il nome utente e la password sono necessari per garantire che i partner abbiano accesso al Registro contenitori di Azure indicato nel processo di pubblicazione.

### <a name="image-version"></a>Versione dell'immagine

Durante la pubblicazione di un'immagine del contenitore è possibile specificare uno o più tag immagine e digest dell'Agente integrità sistema.

**Image Tag or Digest** (Digest o tag immagine)
 
- Il tag o digest deve includere un tag `latest` e un tag di versione (ad esempio, deve iniziare con `xx.xx.xx-`, dove xx rappresenta un numero). Devono essere [tag di manifesto](https://github.com/estesp/manifest-tool) per specificare come destinazione più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. 
- È possibile aggiungere varie versioni del contenitore mediante tag. Tutti i tag di manifesto (tranne `latest`) devono iniziare con `X.Y-` o `X.Y.Z-`, dove X, Y, Z sono numeri interi. <br/> Se ad esempio un tag `latest` punta a `1.0.1-linux-x64`, `1.0.1-linux-arm32` e `1.0.1-windows-arm32`, questi tag devono essere aggiunti qui.

>[!NOTE]
>Ricordarsi di aggiungere un **tag di test** all'immagine, in modo da poterla identificare durante i test.

## <a name="next-steps"></a>Passaggi successivi

Usare la [scheda Marketplace](./cpp-marketplace-tab.md) per creare una descrizione del marketplace per l'offerta. 