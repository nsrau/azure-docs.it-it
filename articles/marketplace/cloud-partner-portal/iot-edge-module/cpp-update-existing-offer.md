---
title: Aggiornare un'offerta esistente di modulo Azure IoT Edge | Azure Marketplace
description: Come aggiornare un'offerta di modulo IoT Edge esistente in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 1dc6832fe09a610634ad934e3b173be6cc3331e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942164"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aggiornare un'offerta di modulo IoT Edge esistente

Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta di modulo IoT Edge nel [portale Cloud Partner](https://cloudpartner.azure.com/) e le modalità di ripubblicazione dell'offerta.

Esistono diversi motivi per cui è possibile aggiornare l'offerta, ad esempio:

-  Aggiunta di una nuova versione dell'immagine del modulo IoT Edge per SKU esistenti.
-  Aggiunta di nuovi SKU.
-  Aggiornamento dei metadati del marketplace per l'offerta o per singoli SKU.

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Modifiche non consentite per l'offerta di modulo IoT Edge per lo SKU

Sono presenti attributi di un'offerta di modulo di IoT Edge o dello SKU che non può essere modificato dopo l'offerta è live su Azure Marketplace. Non è possibile modificare le impostazioni seguenti:

-  **ID offerta** e **ID editore** dell'offerta
-  **ID SKU** di SKU esistenti
-  Tag di versione, ad esempio: `1.0.1`
-  Modifiche del modello di fatturazione/licenza per SKU esistenti

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le operazioni di aggiornamento seguenti sono comuni.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aggiornare la versione di immagine del modulo IoT Edge per uno SKU

È comune per un'immagine del modulo di IoT Edge a essere periodicamente aggiornato con le patch di sicurezza, le funzionalità aggiuntive e così via. In questo scenario si intende aggiornare l'immagine del modulo IoT Edge a cui fa riferimento lo SKU tramite la procedura seguente:

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

4.  Ripubblicare il modulo di IoT Edge usando i passaggi descritti in [pubblicare un modulo IoT Edge in Azure Marketplace](./cpp-publish-offer.md).

5.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare il nuovo SKU.


### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace associati all'offerta, seguire questa procedura. I metadati possibili sono ad esempio il nome della società, i logo e così via.

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** trovare l'offerta da aggiornare.

3.  Andare alla scheda **Marketplace**. Usare le istruzioni nel [pubblicare un modulo IoT Edge in Azure Marketplace](./cpp-publish-offer.md) articolo per apportare modifiche ai metadati.

4.  Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.

## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche in un'offerta pubblicata, è possibile usare la funzionalità **Confronta** per controllare le modifiche apportate. 

**Per usare la funzionalità Confronta**

1.  In qualsiasi momento durante il processo di modifica selezionare **Confronta** per l'offerta.

    ![Pulsante della funzionalità Confronta](./media/iot-edge-module-compare.png)


2.  Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** scheda nella barra del menu di spostamento a sinistra del portale Cloud Partner. È possibile visualizzare le azioni con timestamp eseguite durante il ciclo di vita delle offerte di Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
