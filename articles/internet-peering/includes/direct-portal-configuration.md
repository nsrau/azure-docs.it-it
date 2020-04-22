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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681045"
---
1. Nella scheda **Configurazione** della pagina **Crea peering** compilare le caselle come illustrato di seguito.

    * Per **Tipo di peering**, selezionare **Diretto**.
    * Per **Rete Microsoft**, selezionare **AS8075**. Non creare il peering con ASN 8069. È riservato per applicazioni speciali e utilizzato solo da [Microsoft peering](mailto:peering@microsoft.com).
    * Selezionare **SKU** come **Basic Free**. Non selezionare Premium Free perché è riservato ad applicazioni speciali.
    * Selezionare la posizione **Metro** in cui si desidera impostare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni di peering con Microsoft nella posizione **Metro** selezionata e si usa il portale di Azure per la prima volta per configurare il peering in tale posizione, le connessioni peering esistenti verranno elencate nella sezione **Connessioni peering** come illustrato. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte, insieme alle nuove connessioni, in un'unica posizione. Per altre informazioni, vedere [Convertire un peering diretto legacy in una risorsa](../howto-legacy-direct-portal.md)di Azure usando il portale.
        >

1. In **Connessioni peering**selezionare **Crea nuova** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare o modificare le impostazioni di connessione, selezionare il pulsante di modifica per una linea.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Modifica](../media/setup-direct-conf-tab-edit.png)
    
    * Per eliminare una riga, selezionare **...**  >  **Elimina**.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Elimina](../media/setup-direct-conf-tab-delete.png)

    * È necessario fornire tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Pagina Connessione peering diretto](../media/setup-direct-conf-tab-connection.png)

        1. Selezionare la **funzionalità Peering** in cui è necessario configurare la connessione.
        1. **Provider di indirizzi di sessione** viene utilizzato per determinare chi fornisce la subnet necessaria per configurare la sessione BGP tra la rete e Microsoft. Se è possibile fornire la subnet, selezionare **Peer**. In caso contrario, seleziona **Microsoft** e [Microsoft peering](mailto:peering@microsoft.com) ti contatterà. La scelta di questa opzione richiederà più tempo per Microsoft per elaborare la richiesta di peering. In alcuni casi, Microsoft potrebbe non essere in grado di fornire subnet, che comporterà la negazione della richiesta.
        1. Se è stata selezionata l'opzione **Provider di indirizzi** di sessione come **Peer**, immettere gli indirizzi IPv4 e IPv6 insieme alle maschere di prefisso nelle caselle **Prefisso IPv4 sessione** e **Prefisso IPv6 sessione,** rispettivamente.
        1. Inserisci il numero di prefissi IPv4 e IPv6 che pubblicizzerai rispettivamente nelle caselle Numero massimo di **indirizzi IPv4 annunciati** e Numero massimo di **indirizzi IPv6 pubblicizzati.**
        1. Regolare il dispositivo di scorrimento **Larghezza di banda totale** per riflettere la larghezza di banda per la connessione.
        1. Selezionare **Salva** per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft sia colocalizzata con la rete, all'interno della **metropolitana** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, selezionare **Revisione e creazione**.

    > [!div class="mx-imgBorder"]
    > ![Finale della scheda Configurazione peering](../media/setup-direct-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore visualizza il messaggio *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio diventa *Convalida superata,* verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **Indietro** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Viene visualizzata una distribuzione corretta come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo di peering](../media/setup-direct-success.png)
