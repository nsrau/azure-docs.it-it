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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678832"
---
1. Passare a **Gruppi di**risorse e selezionare il gruppo di risorse selezionato al momento della creazione della risorsa **peering.** Usare la casella **Filtro** se sono disponibili troppi gruppi di risorse.

    > [!div class="mx-imgBorder"]
    > ![Gruppi di risorse](../media/setup-direct-get-resourcegroup.png)

1. Selezionare la risorsa **di peering** creata.

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-get-open.png)

1. La pagina **Panoramica** mostra informazioni di alto livello, come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Riquadro Panoramica delle risorse di peering](../media/setup-direct-get-overview.png)

1. A sinistra, seleziona **Informazioni ASN** per visualizzare le informazioni inviate al momento della creazione di PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informazioni ASN sulle risorse di peering](../media/setup-direct-get-asninfo.png)

1. A sinistra, selezionare **Connessioni**. Nella parte superiore dello schermo viene visualizzato un riepilogo delle connessioni peering tra l'ASN e Microsoft, tra diverse strutture all'interno della metropolitana. È inoltre possibile accedere al riepilogo delle connessioni dalla pagina **Panoramica** selezionando **Connessioni** al centro del riquadro, come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Collegamento delle risorse di peeringPeering resource Connections](../media/setup-direct-get-connectionssummary.png)

    * **Stato connessione** corrisponde allo stato dell'impostazione della connessione di peering. Gli stati visualizzati in questo campo seguono il diagramma di stato illustrato nella [procedura dettagliata Peering diretto](../walkthrough-direct-all.md).
    * **Lo stato della sessione IPv4** e **lo stato sessione IPv6** corrispondono rispettivamente agli stati sessione BGP IPv4 e IPv6. 
    * Quando si seleziona una riga nella parte superiore dello schermo, la sezione **Connessione** nella parte inferiore mostra i dettagli per ogni connessione. Selezionare le frecce per espandere **Configurazione**, **Indirizzo IPv4**e **Indirizzo IPv6**.
