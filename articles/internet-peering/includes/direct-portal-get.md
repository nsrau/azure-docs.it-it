---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678832"
---
1. Passare a **gruppi di risorse**e selezionare il gruppo di risorse selezionato quando è stata creata la risorsa **peering** . Utilizzare la casella **filtro** se sono presenti troppi gruppi di risorse.

    > [!div class="mx-imgBorder"]
    > ![Gruppi di risorse](../media/setup-direct-get-resourcegroup.png)

1. Selezionare la risorsa di **peering** creata.

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione risorse peering](../media/setup-direct-get-open.png)

1. La pagina **Overview** Mostra informazioni di alto livello, come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Riquadro di panoramica delle risorse di peering](../media/setup-direct-get-overview.png)

1. A sinistra selezionare **informazioni ASN** per visualizzare le informazioni inviate al momento della creazione di peerasn sugli.

    > [!div class="mx-imgBorder"]
    > ![Informazioni sul peering delle risorse](../media/setup-direct-get-asninfo.png)

1. A sinistra selezionare **Connections (connessioni**). Nella parte superiore della schermata viene visualizzato un riepilogo delle connessioni di peering tra ASN e Microsoft, in diverse strutture all'interno della metropolitana. È anche possibile accedere al riepilogo delle connessioni dalla pagina **Panoramica** selezionando **connessioni** al centro del riquadro, come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Connessione alle risorse di peering](../media/setup-direct-get-connectionssummary.png)

    * **Lo stato della connessione** corrisponde allo stato della configurazione della connessione peering. Gli stati visualizzati in questo campo seguono il diagramma di stato illustrato nella [procedura dettagliata relativa al peering diretto](../walkthrough-direct-all.md).
    * Lo stato della sessione **IPv4** e **lo stato della sessione IPv6** corrispondono rispettivamente agli Stati di sessione BGP IPv4 e IPv6. 
    * Quando si seleziona una riga nella parte superiore della schermata, nella sezione **connessione** nella parte inferiore vengono visualizzati i dettagli per ogni connessione. Selezionare le frecce per espandere **configurazione**, **indirizzo IPv4**e **indirizzo IPv6**.
