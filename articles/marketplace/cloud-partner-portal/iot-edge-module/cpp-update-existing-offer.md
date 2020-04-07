---
title: Aggiornare un'offerta di modulo Azure IoT Edge esistente Azure Marketplace
description: Come aggiornare un'offerta di modulo IoT Edge esistente in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 821007e40eaa91c98f157ee0b28fe01f37e3cbc9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743954"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aggiornare un'offerta di modulo IoT Edge esistente

>[!Important]
>A partire dal 30 marzo 2020, inizieremo a spostare la gestione delle offerte del modulo IoT Edge al Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare un'offerta del modulo IoT Edge](https://aka.ms/AzureCreateIoT) per gestire le offerte migrate.

Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta di modulo IoT Edge nel [portale Cloud Partner](https://cloudpartner.azure.com/) e le modalità di ripubblicazione dell'offerta.

Esistono diversi motivi per cui è possibile aggiornare l'offerta, ad esempio:

-  Aggiunta di una nuova versione dell'immagine del modulo IoT Edge per SKU esistenti.
-  Aggiunta di nuovi SKU.
-  Aggiornamento dei metadati del marketplace per l'offerta o per singoli SKU.

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Modifiche non consentite per l'offerta di modulo IoT Edge per lo SKU

Esistono attributi di un'offerta del modulo IoT Edge o di uno SKU che non possono essere modificati dopo che l'offerta è disponibile in Azure Marketplace. Non è possibile modificare le impostazioni seguenti:

-  **ID offerta** e **ID editore** dell'offerta
-  **ID SKU** di SKU esistenti
-  Tag di versione, ad esempio: `1.0.1`
-  Modifiche del modello di fatturazione/licenza per SKU esistenti

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le operazioni di aggiornamento seguenti sono comuni.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aggiornare la versione di immagine del modulo IoT Edge per uno SKU

È comune che l'immagine di un modulo IoT Edge venga aggiornata periodicamente con patch di sicurezza, funzionalità aggiuntive e così via. In questo scenario si intende aggiornare l'immagine del modulo IoT Edge a cui fa riferimento lo SKU tramite la procedura seguente:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** trovare l'offerta da aggiornare.

3.  Nella scheda **SKU** selezionare lo SKU associato all'immagine del modulo IoT Edge da aggiornare.

4.  In **Edge module image** (Immagine modulo Edge) selezionare **+ New Image Version** (Versione nuova immagine) per aggiungere una nuova immagine del modulo IoT Edge.

5.  Specificare le **versioni immagine** del nuovo modulo IoT Edge. La versione dell'immagine deve seguire le stesse linee guida per i tag alle versioni precedenti. I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Verificare che il numero della nuova versione specificata sia maggiore di quello di tutte le versioni precedenti.

6.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare la nuova versione del modulo IoT Edge in Azure Marketplace.

### <a name="add-a-new-sku"></a>Aggiungere un nuovo SKU

Per rendere disponibile un nuovo SKU in un'offerta, seguire questa procedura: 

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** trovare l'offerta da aggiornare.

3.  Nella scheda **SKU** selezionare **Add new SKU** (Aggiungere nuovo SKU) e indicare un **ID SKU** nella finestra popup.

4.  Ripubblicare il modulo IoT Edge seguendo la procedura descritta in [Pubblicare un modulo IoT Edge](./cpp-publish-offer.md)in Azure Marketplace.

5.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare il nuovo SKU.


### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace associati all'offerta, seguire questa procedura. I metadati possibili sono ad esempio il nome della società, i logo e così via.

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte**, individuare l'offerta che si desidera aggiornare.

3.  Passare alla scheda **Marketplace.** [Publish an IoT Edge module to Azure Marketplace](./cpp-publish-offer.md)

4.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.

## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche in un'offerta pubblicata, è possibile usare la funzionalità **Confronta** per controllare le modifiche apportate. 

**Per usare la funzionalità Confronta**

1.  In qualsiasi momento durante il processo di modifica selezionare **Confronta** per l'offerta.

    ![Pulsante della funzionalità Confronta](./media/iot-edge-module-compare.png)


2.  Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** scheda nella barra del menu di spostamento a sinistra del portale Cloud Partner. È possibile visualizzare le azioni con timestamp eseguite durante il ciclo di vita delle offerte di Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
