---
title: includere il file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680914"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare le caselle come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Creare un tipo di peering di scambio di pagine peering](../media/setup-exchange-conf-tab.png)

    * Per **tipo di peering**selezionare **Exchange**.
    * Selezionare **SKU** come **Basic Free**.
    * Selezionare la **località** in cui si vuole configurare il peering.

        > [!NOTE]
        > Se si hanno già connessioni peering con Microsoft nella località **metro** selezionata e si sta usando il portale per la prima volta per configurare il peering in tale percorso, le connessioni di peering esistenti verranno elencate nella sezione **connessioni peering** , come illustrato. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni in un'unica posizione. Per altre informazioni, vedere [convertire un peering di Exchange legacy in una risorsa di Azure tramite il portale](../howto-legacy-exchange-portal.md).
        >

1. In **connessioni peering**selezionare **Crea nuovo** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare o modificare le impostazioni di connessione, selezionare il pulsante modifica per una riga.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Modifica](../media/setup-exchange-conf-tab-edit.png)

    * Per eliminare una riga, selezionare **...**  >  **Eliminare**.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Elimina](../media/setup-exchange-conf-tab-delete.png)

    * È necessario specificare tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Pagina connessione peering di Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selezionare la **funzionalità di peering** in cui deve essere configurata la connessione.
        1. Nelle caselle **indirizzo IPv4** e **indirizzo IPv6** immettere rispettivamente gli indirizzi IPv4 e IPv6 che verrebbero configurati nei router Microsoft usando il comando Neighbor.
        1. Immettere il numero di prefissi IPv4 e IPv6 che verranno annunciati rispettivamente nelle caselle numero **massimo indirizzi IPv4 annunciati** e **indirizzi IPv6** annunciati.
        1. Selezionare **OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft si trova nella propria rete, all'interno della **metro** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, selezionare **Verifica + crea**.

    > [!div class="mx-imgBorder"]
    > ![Scheda configurazione peering](../media/setup-exchange-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore Visualizza il messaggio *che esegue la convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio è stato modificato in *convalida superata*, verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **precedente** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione ha esito negativo, contattare il team [Peer Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta viene visualizzata come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Il peering è riuscito](../media/setup-direct-success.png)
