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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678847"
---
1. Nella scheda **Configurazione** della pagina **Crea peering** compilare le caselle come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Creare una scheda Configurazione pagina peeringCreate a Peering page Configuration tab](../media/setup-direct-conf-tab.png)

    * Per **Tipo di peering**, selezionare **Diretto**.
    * Per **Rete Microsoft**, selezionare **AS8075**. Non selezionare ASN 8069. È riservato per applicazioni speciali e utilizzato solo da [Microsoft peering](mailto:peering@microsoft.com).
    * Selezionare **SKU** come **Basic Free**. Non selezionare Premium Free perché è riservato ad applicazioni speciali.
    * Selezionare la posizione Metro in cui si vuole convertire il peering in una risorsa di Azure.Select the **Metro** location where you want to convert peering to an Azure resource. Se si dispone di connessioni di peering con Microsoft nella posizione **Metro** selezionata che non vengono convertite in una risorsa di Azure, queste connessioni verranno elencate nella sezione **Connessioni peering** come illustrato. È ora possibile convertire queste connessioni di peering in una risorsa di Azure.You can now convert these peering connections to an Azure resource.

        > [!div class="mx-imgBorder"]
        > ![Elenco delle connessioni di peering](../media/setup-directlegacy-conf-tab.png)

1. Se è necessario aggiornare la larghezza di banda, selezionare il pulsante di modifica di una linea per modificare le impostazioni di connessione.

    > [!div class="mx-imgBorder"]
    > ![Pulsante Modifica](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se si desidera aggiungere ulteriori connessioni di peering con Microsoft nella posizione **Metro** selezionata, selezionare **Crea nuovo**. Per ulteriori informazioni, consultate [Creare o modificare un peering diretto tramite il portale.](../howto-direct-portal.md)
    >

1. Selezionare **Rivedi e crea**. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore visualizza il messaggio *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio diventa *Convalida superata,* verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **Indietro** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Viene visualizzata una distribuzione corretta come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo di peering](../media/setup-direct-success.png)
