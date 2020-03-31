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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775277"
---
1. Nella pagina **Crea peering,** nella scheda **Configurazione,** compilare i campi come illustrato di seguito.

    * Per **Tipo di peering**, selezionare *Diretto*.
    * Per **rete Microsoft**, scegliere *AS8075*. Non creare il peering con ASN 8069. È riservato per applicazioni speciali e utilizzato solo da [Microsoft peering](mailto:peering@microsoft.com).
    * Selezionare **SKU** come *Basic Free*. Non selezionare *Premium Free* poiché è riservato ad applicazioni speciali.
    * Scegli la posizione **Metro** in cui vuoi configurare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni di peering con Microsoft nella posizione **Metro** selezionata e si utilizza il portale per la prima volta per configurare il peering in tale posizione, le connessioni di peering esistenti verranno elencate nella sezione **Connessioni peering** come illustrato di seguito. Microsoft convertirà automaticamente queste connessioni di peering in risorse di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni, in un'unica posizione. Per altre info, vedi [Convertire un peering diretto legacy](../howto-legacy-direct-portal.md) in una risorsa di Azure usando il portale.
        >

1. In **Connessioni peering**fare clic su **Crea nuova** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare/modificare le impostazioni di connessione, fare clic sul pulsante di modifica per una linea.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Modifica diretta](../media/setup-direct-conf-tab-edit.png)
    
    * Per eliminare una riga, fare clic sul pulsante **...** > **Elimina**.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Modifica diretta](../media/setup-direct-conf-tab-delete.png)

    * È necessario fornire tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Configurazione peering - Connessione diretta](../media/setup-direct-conf-tab-connection.png)

        1. Selezionare la **funzionalità Peering** in cui è necessario configurare la connessione.
        1. **Provider di indirizzi di sessione** viene utilizzato per determinare chi fornisce la subnet necessaria per configurare la sessione BGP tra la rete e Microsoft. Se si è in grado di fornire una subnet, scegliere *Peer*. Altrimenti scegliere **Microsoft** e [Microsoft peering](mailto:peering@microsoft.com) vi contatterà. Si noti che la scelta di così, richiederebbe più tempo per Microsoft elaborare la richiesta di peering. In alcuni casi Microsoft potrebbe non essere in grado di fornire subnet che comporteranno la negazione della richiesta.
        1. Se si sceglie **Provider di indirizzi** di sessione come *peer*, immettere l'indirizzo IPv4 e IPv6 insieme alla maschera prefisso nei campi **Prefisso IPv4 sessione** e **Prefisso IPv6 sessione** rispettivamente.
        1. Immettere il numero di prefissi IPv4 e IPv6 annunciati nei campi **Massimo indirizzi IPv4 annunciati** e Numero massimo di **indirizzi IPv6 annunciati** rispettivamente.
        1. Regolare il dispositivo di scorrimento **Larghezza di banda totale** per riflettere la larghezza di banda per la connessione.
        1. Fare clic **su OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft è colocalizzata con la rete, all'interno della **metropolitana** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, fare clic su **Rivedi : crea**.

    > [!div class="mx-imgBorder"]
    > ![Scheda Configurazione peering finale](../media/setup-direct-conf-tab-final.png)

1. Osservare che il portale esegue la convalida di base delle informazioni immesse. Questo viene visualizzato in una barra multifunzione nella parte superiore, come *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che si passa a *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **Precedente** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere che completi la distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo peering](../media/setup-direct-success.png)
