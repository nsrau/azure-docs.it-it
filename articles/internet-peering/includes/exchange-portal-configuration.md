---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774497"
---
1. Nella pagina **Crea peering,** nella scheda **Configurazione,** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Configurazione peering - Exchange](../media/setup-exchange-conf-tab.png)

    * Per **Tipo di peering**, selezionare *Exchange*.
    * Selezionare **SKU** come *Basic Free*.
    * Scegli la posizione **Metro** in cui vuoi configurare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni di peering con Microsoft nella posizione **Metro** selezionata e si utilizza il portale per la prima volta per configurare il peering in tale posizione, le connessioni di peering esistenti verranno elencate nella sezione **Connessioni peering** come illustrato di seguito. Microsoft convertirà automaticamente queste connessioni di peering in risorse di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni, in un'unica posizione. Per altre informazioni, vedere [Convertire un peering di Exchange legacy in una risorsa](../howto-legacy-exchange-portal.md) di Azure usando il portale.
        >

1. In **Connessioni peering**fare clic su **Crea nuova** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare/modificare le impostazioni di connessione, fare clic sul pulsante di modifica per una linea.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Modifica di Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Per eliminare una riga, fare clic sul pulsante **...** > **Elimina**.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Modifica di Exchange](../media/setup-exchange-conf-tab-delete.png)

    * È necessario fornire tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Configurazione peering - Connessione di Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selezionare la **funzionalità Peering** in cui è necessario configurare la connessione.
        1. Nei campi **Indirizzo IPv4** e **Indirizzo IPv6**immettere rispettivamente l'indirizzo IPv4 e IPv6 che verranno configurati nei router Microsoft utilizzando il comando neighbor.
        1. Immettere il numero di prefissi IPv4 e IPv6 annunciati nei campi **Massimo indirizzi IPv4 annunciati** e Numero massimo di **indirizzi IPv6 annunciati** rispettivamente.
        1. Fare clic **su OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft è colocalizzata con la rete, all'interno della **metropolitana** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, fare clic su **Rivedi : crea**.

    > [!div class="mx-imgBorder"]
    > ![Scheda Configurazione peering finale](../media/setup-exchange-conf-tab-final.png)

1. Osservare che il portale esegue la convalida di base delle informazioni immesse. Questo viene visualizzato in una barra multifunzione nella parte superiore, come *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che si passa a *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **Precedente** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere che completi la distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo peering](../media/setup-direct-success.png)
