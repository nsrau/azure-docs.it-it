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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775277"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare i campi come illustrato di seguito.

    * Per **tipo di peering**selezionare *diretta*.
    * Per **Microsoft Network**scegliere *AS8075*. Non creare il peering con ASN 8069. È riservata per le applicazioni speciali e viene usata solo dal [peering Microsoft](mailto:peering@microsoft.com).
    * Selezionare **SKU** come *Basic Free*. Non selezionare *Premium gratuito* perché è riservato per applicazioni speciali.
    * Scegliere la **posizione in** cui si vuole configurare il peering.

        > [!NOTE]
        > Se si dispone già di connessioni peering con Microsoft nella località **metro** selezionata e si usa il portale per la prima volta per configurare il peering in tale percorso, le connessioni di peering esistenti verranno elencate nella sezione **connessioni peering** , come illustrato di seguito. Microsoft convertirà automaticamente queste connessioni di peering in una risorsa di Azure in modo che sia possibile gestirle tutte insieme alle nuove connessioni, in un'unica posizione. Per altre informazioni, vedere [convertire un peering diretto legacy in una risorsa di Azure usando il portale](../howto-legacy-direct-portal.md) .
        >

1. In **connessioni peering**fare clic su **Crea nuovo** per aggiungere una riga per ogni nuova connessione che si desidera configurare.

    * Per configurare/modificare le impostazioni di connessione, fare clic sul pulsante modifica per una riga.

        > [!div class="mx-imgBorder"]
        > Configurazione del peering ![-modifica diretta](../media/setup-direct-conf-tab-edit.png)
    
    * Per eliminare una riga, fare clic sul pulsante **...** > **Elimina**.

        > [!div class="mx-imgBorder"]
        > Configurazione del peering ![-modifica diretta](../media/setup-direct-conf-tab-delete.png)

    * È necessario specificare tutte le impostazioni per una connessione, come illustrato di seguito.

         > [!div class="mx-imgBorder"]
         > Configurazione del peering ![-connessione diretta](../media/setup-direct-conf-tab-connection.png)

        1. Selezionare la **funzionalità di peering** in cui deve essere configurata la connessione.
        1. Il **provider di indirizzi di sessione** viene usato per determinare chi fornisce la subnet necessaria per configurare la sessione BGP tra la rete e Microsoft. Se si è in grado di fornire una subnet, scegliere *peer*. Altrimenti scegliere **Microsoft** e il [peering Microsoft](mailto:peering@microsoft.com) contatterà l'utente. Si noti che la scelta di questa opzione richiederà più tempo per l'elaborazione della richiesta di peering da parte di Microsoft. In alcuni casi Microsoft potrebbe non essere in grado di fornire subnet che comporteranno il rifiuto della richiesta.
        1. Se si sceglie **provider di indirizzi di sessione** come *peer*, immettere l'indirizzo IPv4 e IPv6 insieme alla maschera di prefisso rispettivamente nel prefisso **IPv4 della sessione** campi e nel **prefisso IPv6 della sessione** .
        1. Immettere il numero di prefissi IPv4 e IPv6 da annunciare nei campi numero **massimo di indirizzi IPv4 annunciati** e numero **massimo di indirizzi IPv6 annunciati** rispettivamente.
        1. Regolare il dispositivo di scorrimento della **larghezza di banda totale** in base alla larghezza di banda per la connessione.
        1. Fare clic su **OK** per salvare le impostazioni di connessione.

1. Ripetere il passaggio precedente per aggiungere altre connessioni in qualsiasi struttura in cui Microsoft si trova nella propria rete, entro la **metro** selezionata in precedenza.

1. Dopo aver aggiunto tutte le connessioni necessarie, fare clic su **Verifica + crea**.

    > [!div class="mx-imgBorder"]
    > ![scheda di configurazione peering finale](../media/setup-direct-conf-tab-final.png)

1. Si noti che il portale esegue la convalida di base delle informazioni immesse. Viene visualizzato in una barra multifunzione in alto, come *esecuzione della convalida finale...* .

    > [!div class="mx-imgBorder"]
    > ![scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo aver completato la *convalida*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **indietro** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > Invio del peering ![](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attenderne il completamento della distribuzione. Se la distribuzione non riesce, contattare il [peering Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come indicato di seguito.

    > [!div class="mx-imgBorder"]
    > Riuscita del peering ![](../media/setup-direct-success.png)
