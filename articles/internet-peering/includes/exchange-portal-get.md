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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774549"
---
Una volta distribuita correttamente la risorsa **peering,** è possibile visualizzarla seguendo la procedura seguente.

1. Passare a **Gruppi di** risorse e fare clic sul gruppo di risorse selezionato durante la creazione della risorsa di **peering.** È possibile utilizzare il campo *Filtro* se sono disponibili troppi gruppi di risorse.

    > [!div class="mx-imgBorder"]
    > ![Gruppo di risorse peering](../media/setup-direct-get-resourcegroup.png)

1. Fare clic sulla risorsa **peering** creata.

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-get-open.png)

1. La pagina **Panoramica** mostra informazioni generali. Osservare le informazioni evidenziate di seguito.

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-get-overview.png)

1. A sinistra, fai clic su **Informazioni ASN** per visualizzare le informazioni inviate durante la creazione di PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-get-asninfo.png)

1. A sinistra, fare clic su **Connessioni**. Osservare in alto un riepilogo delle connessioni peering tra l'ASN e Microsoft, attraverso diverse strutture all'interno della metropolitana. È inoltre possibile accedere al riepilogo delle connessioni dalla pagina **Panoramica,** facendo clic su **Connessioni** nel riquadro centrale come evidenziato in precedenza.

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-get-connectionssummary.png)

    * **Stato connessione** corrisponde allo stato della configurazione della connessione di peering. Gli stati visualizzati in questo campo seguono il diagramma di stato illustrato nella [procedura dettagliata di peering di Exchange](../walkthrough-exchange-all.md)
    * **Lo stato sessione IPv4** e **lo stato sessione IPv6** corrispondono rispettivamente agli stati sessione BGP IPv4 e IPv6.  
    * Quando si seleziona una riga in alto, la sezione ***Connessione*** in basso mostra i dettagli per ogni connessione. È possibile fare clic sui segni freccia per espandere le sottosezioni ***Configurazione***, ***Indirizzo IPv4*** e ***Indirizzo IPv6***

    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-get-connectionsipv4.png)
