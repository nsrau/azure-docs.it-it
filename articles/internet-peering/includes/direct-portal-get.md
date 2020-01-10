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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775082"
---
1. Passare a **gruppi di risorse** e fare clic sul gruppo di risorse selezionato durante la creazione della risorsa **peering** . Se sono presenti troppi gruppi di risorse, è possibile usare il campo *filtro* .

    > [!div class="mx-imgBorder"]
    > ![gruppo di risorse peering](../media/setup-direct-get-resourcegroup.png)

1. Fare clic sulla risorsa di **peering** creata.

    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-get-open.png)

1. La pagina **Panoramica** Mostra informazioni di alto livello. Osservare le informazioni evidenziate di seguito.

    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-get-overview.png)

1. A sinistra fare clic su **informazioni ASN** per visualizzare le informazioni inviate durante la creazione di peerasn sugli

    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-get-asninfo.png)

1. A sinistra fare clic su **connessioni**. Osservare in primo piano un riepilogo delle connessioni di peering tra ASN e Microsoft, in diverse strutture all'interno della metropolitana. È anche possibile arrivare alla pagina Riepilogo connessioni da **Panoramica** , facendo clic su **connessioni** nel riquadro centrale, come evidenziato sopra.

    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-get-connectionssummary.png)

    * **Lo stato della connessione** corrisponde allo stato della connessione di peering configurata. Gli stati visualizzati in questo campo seguono il diagramma di stato illustrato nella [procedura dettagliata relativa al peering diretto](../walkthrough-direct-all.md)
    * Lo stato della sessione **IPv4** e **lo stato** della sessione IPv6 corrispondono rispettivamente agli Stati di sessione BGP IPv4 e IPv6.  
    * Quando si seleziona una riga nella parte superiore, la sezione ***connessione*** nella parte inferiore mostra i dettagli per ogni connessione. È possibile fare clic sui segni di freccia per espandere la ***configurazione***delle sottosezioni, l' ***indirizzo IPv4*** e l' ***indirizzo IPv6***
