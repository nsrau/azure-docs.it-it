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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680914"
---
1. Nella scheda **Configurazione** della pagina **Crea peering** compilare le caselle come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Creare un tipo di peering di peering per pagine di peering](../media/setup-exchange-conf-tab.png)

    * Per **Tipo di peering**, selezionare **Exchange**.
    * Selezionare **SKU** come **Basic Free**.
    * Selezionare la posizione **Metro** in cui si desidera impostare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni di peering con Microsoft nella posizione **Metro** selezionata e si usa il portale per la prima volta per configurare il peering in tale posizione, le connessioni peering esistenti verranno elencate nella sezione **Connessioni peering** come illustrato. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni in un'unica posizione. Per altre informazioni, vedere [Convertire un peering di Exchange legacy in una risorsa](../howto-legacy-exchange-portal.md)di Azure tramite il portale .
        >

1. In **Connessioni peering**selezionare **Crea nuova** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare o modificare le impostazioni di connessione, selezionare il pulsante di modifica per una linea.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Modifica](../media/setup-exchange-conf-tab-edit.png)

    * Per eliminare una riga, selezionare **...**  >  **Elimina**.

        > [!div class="mx-imgBorder"]
        > ![Pulsante Elimina](../media/setup-exchange-conf-tab-delete.png)

    * È necessario fornire tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > ![Pagina Connessione peering di Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selezionare la **funzionalità Peering** in cui è necessario configurare la connessione.
        1. Nelle caselle **Indirizzo IPv4** e **Indirizzo IPv6** immettere rispettivamente gli indirizzi IPv4 e IPv6 che verranno configurati nei router Microsoft utilizzando il comando neighbor.
        1. Inserisci il numero di prefissi IPv4 e IPv6 che pubblicizzerai rispettivamente nelle caselle Numero massimo di **indirizzi IPv4 annunciati** e Numero massimo di **indirizzi IPv6 pubblicizzati.**
        1. Selezionare **OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft sia colocalizzata con la rete, all'interno della **metropolitana** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, selezionare **Revisione e creazione**.

    > [!div class="mx-imgBorder"]
    > ![Scheda Configurazione peering](../media/setup-exchange-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore visualizza il messaggio *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio diventa *Convalida superata,* verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **Indietro** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Viene visualizzata una distribuzione corretta come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo di peering](../media/setup-direct-success.png)
