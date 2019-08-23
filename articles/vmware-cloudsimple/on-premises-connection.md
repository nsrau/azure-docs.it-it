---
title: Soluzione VMware di Azure tramite CloudSimple-connessione locale con ExpressRoute
description: Descrive come richiedere una connessione locale usando ExpressRoute dalla rete dell'area CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee359b76072da3caee9ae1f5fab3d0fc28d25c0e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972695"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Connettersi da locale a CloudSimple usando ExpressRoute

Se si dispone già di una connessione Azure ExpressRoute da una posizione esterna, ad esempio locale, ad Azure, è possibile connetterla all'ambiente CloudSimple. Questa operazione può essere eseguita tramite una funzionalità di Azure che consente la connessione tra due circuiti ExpressRoute. Questo metodo stabilisce una connessione protetta, privata, a bassa latenza tra i due ambienti.

[![Connessione ExpressRoute locale-Copertura globale](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="prerequisites"></a>Prerequisiti

* È necessario un circuito ExpressRoute di Azure per poter stabilire la connessione tra il circuito e le reti di cloud privato CloudSimple.
* Un utente è necessario con privilegi per creare chiavi di autorizzazione in un circuito ExpressRoute.

## <a name="scenarios"></a>Scenari

La connessione della rete locale alla rete cloud privata consente di usare il cloud privato in diversi modi, inclusi i seguenti:

* Accedere alla rete cloud privata senza creare una connessione VPN da sito a sito.
* Usa la Active Directory locale come origine delle identità nel cloud privato.
* Eseguire la migrazione di macchine virtuali in esecuzione in locale nel cloud privato.
* Usare il cloud privato come parte di una soluzione di ripristino di emergenza.
* Utilizzare risorse locali nelle VM del carico di lavoro del cloud privato.

## <a name="connecting-expressroute-circuits"></a>Connessione di circuiti ExpressRoute

Per stabilire la connessione ExpressRoute, è necessario creare un'autorizzazione sul circuito ExpressRoute e fornire le informazioni di autorizzazione a CloudSimple.

### <a name="create-expressroute-authorization"></a>Crea autorizzazione ExpressRoute

1. Accedere al portale di Azure.

2. Nella barra di ricerca superiore cercare il **circuito ExpressRoute** e fare clic su **ExpressRoute** Circuits in **Services**.
    [![Circuiti ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selezionare il circuito ExpressRoute che si intende connettere alla rete CloudSimple.

4. Nella pagina ExpressRoute fare clic su **autorizzazioni**, immettere un nome per l'autorizzazione e fare clic su **Salva**.
    [![Autorizzazione del circuito ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copiare l'ID risorsa e la chiave di autorizzazione facendo clic sull'icona di copia. Incollare l'ID e la chiave in un file di testo.
    [![Copia di autorizzazione del circuito ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > L' **ID risorsa** deve essere copiato dall'interfaccia utente e deve essere nel formato ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` quando lo si fornisce per supportare.

6. Consente di archiviare un ticket con <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a> per la connessione da creare.
    * Tipo di problema: **Tecnico**
    * Sottoscrizione: **Sottoscrizione in cui è distribuito il servizio CloudSimple**
    * Servizio: **Soluzione VMware per CloudSimple**
    * Tipo di problema: **Richiesta di servizio**
    * Sottotipo di problema: **Creare una connessione ExpressRoute in locale**
    * Specificare l'ID risorsa e la chiave di autorizzazione copiati e salvati nel riquadro dettagli.
