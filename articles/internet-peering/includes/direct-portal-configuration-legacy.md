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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775069"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > Configurazione del peering ![-](../media/setup-direct-conf-tab.png) diretta

    * Per **tipo di peering**selezionare *diretta*.
    * Per **Microsoft Network**scegliere *AS8075*. Non selezionare ASN 8069. È riservata per le applicazioni speciali e viene usata solo dal [peering Microsoft](mailto:peering@microsoft.com).
    * Selezionare **SKU** come *Basic Free*. Non selezionare *Premium gratuito* perché è riservato per applicazioni speciali.
    * Scegliere la **posizione** in cui si vuole convertire il peering nella risorsa di Azure. Se si dispone di connessioni peering con Microsoft nella località **metro** selezionata che non vengono convertite in risorsa di Azure, tali connessioni verranno elencate nella sezione **connessioni peering** , come illustrato di seguito. È ora possibile convertire queste connessioni di peering in una risorsa di Azure.

        > [!div class="mx-imgBorder"]
        > Configurazione del peering ![-connessioni dirette-legacy](../media/setup-directlegacy-conf-tab.png)

1. Se è necessario aggiornare la larghezza di banda, fare clic sul pulsante modifica per una riga, come illustrato di seguito, per modificare le impostazioni di connessione.

    > [!div class="mx-imgBorder"]
    > Configurazione del peering ![-modifica diretta](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se si desidera aggiungere ulteriori connessioni peering con Microsoft nella località **metro** selezionata, è possibile fare clic sul pulsante **Crea nuovo** . Per altre informazioni, vedere [creare o modificare un peering diretto usando il portale](../howto-direct-portal.md) .
    >

1. Fare clic su **Verifica + crea**. Si noti che il portale esegue la convalida di base delle informazioni immesse. Viene visualizzato in una barra multifunzione in alto, come *esecuzione della convalida finale...* .

    > [!div class="mx-imgBorder"]
    > ![scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo aver completato la *convalida*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **indietro** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > Invio del peering ![](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attenderne il completamento della distribuzione. Se la distribuzione non riesce, contattare il [peering Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come indicato di seguito.

    > [!div class="mx-imgBorder"]
    > Riuscita del peering ![](../media/setup-direct-success.png)
