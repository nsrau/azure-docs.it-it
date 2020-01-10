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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774497"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > Configurazione del peering ![-](../media/setup-exchange-conf-tab.png) di Exchange

    * Per **tipo di peering**selezionare *Exchange*.
    * Selezionare **SKU** come *Basic Free*.
    * Scegliere la **posizione in** cui si vuole configurare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni peering con Microsoft nella località **metro** selezionata e si usa il portale per la prima volta per configurare il peering in tale percorso, le connessioni di peering esistenti verranno elencate nella sezione **connessioni peering** , come illustrato di seguito. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni, in un'unica posizione. Per altre informazioni, vedere [convertire un peering Exchange legacy in una risorsa di Azure usando il portale](../howto-legacy-exchange-portal.md) .
        >

1. In **connessioni peering**fare clic su **Crea nuovo** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare/modificare le impostazioni di connessione, fare clic sul pulsante modifica per una riga.

        > [!div class="mx-imgBorder"]
        > Configurazione del peering ![-modifica di Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Per eliminare una riga, fare clic sul pulsante **...** > **Elimina**.

        > [!div class="mx-imgBorder"]
        > Configurazione del peering ![-modifica di Exchange](../media/setup-exchange-conf-tab-delete.png)

    * È necessario specificare tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > Configurazione del peering ![-connessione Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selezionare la **funzionalità di peering** in cui deve essere configurata la connessione.
        1. Nei campi indirizzo **IPv4** e **indirizzo IPv6**immettere rispettivamente l'indirizzo IPv4 e IPv6 che verrà configurato nei router Microsoft mediante il comando Neighbor.
        1. Immettere il numero di prefissi IPv4 e IPv6 da annunciare nei campi numero **massimo di indirizzi IPv4 annunciati** e numero **massimo di indirizzi IPv6 annunciati** rispettivamente.
        1. Fare clic su **OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft si trova nella propria rete, entro la **metro** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, fare clic su **Verifica + crea**.

    > [!div class="mx-imgBorder"]
    > ![scheda di configurazione peering finale](../media/setup-exchange-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Viene visualizzato in una barra multifunzione in alto, come *esecuzione della convalida finale...* .

    > [!div class="mx-imgBorder"]
    > ![scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo aver completato la *convalida*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **indietro** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > Invio del peering ![](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attenderne il completamento della distribuzione. Se la distribuzione non riesce, contattare il [peering Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come indicato di seguito.

    > [!div class="mx-imgBorder"]
    > Riuscita del peering ![](../media/setup-direct-success.png)
