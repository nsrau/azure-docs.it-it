---
title: Contenitori | Microsoft Docs
description: Passaggi per la pubblicazione di un'immagine del contenitore.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807987"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Pubblicazione di un'immagine del contenitore nel portale Cloud Partner
========================================================

Questo articolo descrive come pubblicare una nuova immagine del contenitore nel portale Cloud Partner.

Usare la procedura seguente per pubblicare una nuova immagine del contenitore.

1. Selezionare **Nuova offerta** e quindi selezionare **Contenitori**.

    ![Selezionare l'opzione Contenitori per la nuova offerta](media/cpp-containers-guide/azure-container-offer.png)

2. Nella scheda Impostazioni dell'offerta in Identità dell'offerta immettere **ID offerta**, **ID editore** e **Nome**.

    ![Identità dell'offerta](media/cpp-containers-guide/containers-offer-settings.png)

3. Nella scheda SKU selezionare **Nuovo SKU**.
    >[!NOTE]
    >È possibile aggiungere più di uno SKU.

    ![Richiesta nuovo SKU](media/cpp-containers-guide/containers-sku-settings.png)

4. Immettere le informazioni relative allo SKU e al contenitore. Ogni SKU corrisponde a un'immagine del contenitore. Uno SKU si compone di due parti:

    -   Metadati dello SKU
    -   Metadati del contenitore

    I metadati dello SKU contengono le informazioni visualizzate per le immagini del contenitore.

    ![Metadati dello SKU](media/cpp-containers-guide/containers-sku-details.png)

    I metadati del contenitore includono informazioni di riferimento dei dettagli del repository di immagini all'interno del Registro contenitori di Azure. Azure Marketplace copia quindi l'immagine nel registro marketplace pubblico rendendola disponibile per i clienti dopo la certificazione. Tutte le richieste dell'utente di Azure per il consumo di un'immagine del contenitore vengono servite dal registro contenitori di Marketplace.

   ![Metadati del contenitore](media/cpp-containers-guide/containers-image-repository.png)

    I dettagli del repository di immagini illustrati nella precedente acquisizione di schermata includono i campi seguenti:

    -   **ID sottoscrizione**: l'ID della sottoscrizione di Azure in cui risiede il Registro contenitori di Azure.
    -   **Nome del gruppo di risorse**: il nome del gruppo di risorse del Registro contenitori di Azure.
    -   **Nome del registro**: il nome del Registro contenitori di Azure.
    -   **Nome del repository**: il nome del repository. Dopo che è stato impostato, questo valore non può essere modificato. Il nome deve essere univoco in modo che nessun'altra offerta nell'account abbia lo stesso nome.
    -   **Nome utente**: il nome utente associato al Registro contenitori di Azure (nome utente amministratore).
    -   **Password**: la password associata al Registro contenitori di Azure.

    >[!NOTE]
    >Il nome utente e la password sono necessari per garantire che i partner abbiano accesso al Registro contenitori di Azure indicato nel processo di pubblicazione.

    Durante la pubblicazione di un'immagine del contenitore è possibile specificare anche uno o più tag immagine. Oltre a un tag immagine, i partner possono specificare anche digest SHA. Assicurarsi di aggiungere un **tag di test** all'immagine in modo da poterla identificare durante i test.

5. Nella scheda Marketplace aggiungere il contenuto di marketing specifico.

    ![Informazioni relative al marketplace](media/cpp-containers-guide/containers-marketplace-tab.png)

6. Nella scheda Supporto immettere le informazioni relative al contatto tecnico e all'assistenza clienti.

   ![Informazioni di supporto](media/cpp-containers-guide/containers-support-tab.png)

7. Selezionare **Pubblica** per pubblicare l'offerta. Dopo aver selezionato Pubblica, verrà visualizzata una sequenza temporale che illustra i passaggi necessari per la pubblicazione dell'immagine del contenitore.
