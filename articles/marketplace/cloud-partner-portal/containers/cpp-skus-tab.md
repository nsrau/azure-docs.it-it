---
title: SKU per un'immagine di contenitori di Azure | Azure Marketplace
description: Configurare SKU per un contenitore di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823156"
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

I metadati del contenitore includono informazioni di riferimento dei dettagli del repository di immagini all'interno di Registro Azure Container. Azure Marketplace copia questa immagine in un registro pubblico specifico del Marketplace e quindi la rende disponibile per i clienti dopo la certificazione. Tutte le richieste dell'utente di Azure per l'utilizzo di un'immagine del contenitore di Azure Marketplace vengono rese disponibili dal registro pubblico del Marketplace e non da Registro Azure Container.

![Metadati del contenitore](./media/containers-image-repository.png)
    
I **Dettagli dell'archivio immagini** nell'acquisizione schermo precedente contengono i campi seguenti.  I campi obbligatori sono indicati da un asterisco (*).

-   **ID sottoscrizione\*** : ID sottoscrizione di Azure in cui è presente il registro contenitori di Azure.
-   **Nome del gruppo di risorse\*** : il nome del gruppo di risorse dell'ACR.
-   **Nome del registro di sistema\*** : nome dell'ACR.
-   **Nome repository\*** : nome del repository. Dopo aver impostato il nome, questo valore non può più essere modificato. Usare un nome univoco per evitare conflitti con altre offerte nell'account.
-   **Nome utente\*** : nome utente (nome utente amministratore) associato all'immagine ACR.
-   **Password\*** : la password associata all'immagine ACR.

    >[!NOTE]
    >Il nome utente e la password sono necessari per garantire che i partner abbiano accesso al Registro Azure Container indicato nel processo di pubblicazione.


### <a name="image-version"></a>Versione dell'immagine

Durante la pubblicazione di un'immagine del contenitore è possibile specificare uno o più tag immagine e digest dell'Agente integrità sistema.

**\* o digest del tag image**
 
- Il tag o digest deve includere un tag `latest` e un tag di versione (ad esempio, deve iniziare con `xx.xx.xx-`, dove xx rappresenta un numero). Devono essere [tag di manifesto](https://github.com/estesp/manifest-tool) per specificare come destinazione più piattaforme. È necessario aggiungere anche tutti i tag referenziati da un tag di manifesto per poterli caricare. 
- È possibile aggiungere varie versioni del contenitore mediante tag. Tutti i tag di manifesto (tranne `latest`) devono iniziare con `X.Y-` o `X.Y.Z-`, dove X, Y, Z sono numeri interi. <br/> Se ad esempio un tag `latest` punta a `1.0.1-linux-x64`, `1.0.1-linux-arm32` e `1.0.1-windows-arm32`, questi tag devono essere aggiunti qui.

>[!NOTE]
>Ricordarsi di aggiungere un **tag di test** all'immagine, in modo da poterla identificare durante i test.


## <a name="next-steps"></a>Passaggi successivi

Usare la [scheda Marketplace](./cpp-marketplace-tab.md) per creare una descrizione del marketplace per l'offerta. 
