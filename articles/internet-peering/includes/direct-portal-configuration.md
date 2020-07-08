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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681045"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare le caselle come illustrato qui.

    * Per **tipo di peering**selezionare **diretta**.
    * Per **Microsoft Network**selezionare **AS8075**. Non creare il peering con ASN 8069. È riservata per applicazioni speciali e viene usata solo dal [peering Microsoft](mailto:peering@microsoft.com).
    * Selezionare **SKU** come **Basic Free**. Non selezionare Premium Free perché è riservato per applicazioni speciali.
    * Selezionare la **località** in cui si vuole configurare il peering.

        > [!NOTE]
        > Se si hanno già connessioni peering con Microsoft nella località **metro** selezionata e si sta usando il portale di Azure per la prima volta per configurare il peering in tale percorso, le connessioni di peering esistenti verranno elencate nella sezione **connessioni peering** , come illustrato. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte, insieme alle nuove connessioni, in un'unica posizione. Per altre informazioni, vedere [convertire un peering diretto legacy in una risorsa di Azure tramite il portale](../howto-legacy-direct-portal.md).
        >

1. In **connessioni peering**selezionare **Crea nuovo** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare o modificare le impostazioni di connessione, selezionare il pulsante modifica per una riga.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Modifica](../media/setup-direct-conf-tab-edit.png)
    
    * Per eliminare una riga, selezionare **...**  >  **Eliminare**.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Elimina](../media/setup-direct-conf-tab-delete.png)

    * È necessario specificare tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Pagina connessione diretta peering](../media/setup-direct-conf-tab-connection.png)

        1. Selezionare la **funzionalità di peering** in cui deve essere configurata la connessione.
        1. Il **provider di indirizzi di sessione** viene usato per determinare chi fornisce la subnet necessaria per configurare la sessione BGP tra la rete e Microsoft. Se è possibile specificare la subnet, selezionare **peer**. In caso contrario, selezionare **Microsoft** e il [peering Microsoft](mailto:peering@microsoft.com) contatteranno l'utente. La scelta di questa opzione richiederà più tempo per l'elaborazione della richiesta di peering da parte di Microsoft. In alcuni casi, Microsoft potrebbe non essere in grado di fornire subnet, il che comporterà la negazione della richiesta.
        1. Se è stata selezionata l'opzione **provider indirizzo sessione** come **peer**, immettere rispettivamente gli indirizzi IPv4 e IPv6 insieme alle maschere di prefisso nelle caselle prefisso **IPv4** sessione e prefisso **IPv6 sessione** .
        1. Immettere il numero di prefissi IPv4 e IPv6 che verranno annunciati rispettivamente nelle caselle numero **massimo indirizzi IPv4 annunciati** e **indirizzi IPv6** annunciati.
        1. Regolare il dispositivo di scorrimento della **larghezza di banda totale** in base alla larghezza di banda per la connessione.
        1. Selezionare **Save (Salva** ) per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft si trova nella propria rete, all'interno della **metro** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, selezionare **Verifica + crea**.

    > [!div class="mx-imgBorder"]
    > ![Fine scheda configurazione peering](../media/setup-direct-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore Visualizza il messaggio *che esegue la convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio è stato modificato in *convalida superata*, verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **precedente** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione ha esito negativo, contattare il team [Peer Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta viene visualizzata come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Il peering è riuscito](../media/setup-direct-success.png)
