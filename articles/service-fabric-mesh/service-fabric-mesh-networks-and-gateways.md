---
title: Introduzione alla rete di Microsoft Azure Service Fabric | Microsoft Docs
description: Informazioni su reti, gateway e routing del traffico intelligente in Azure Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339262"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Introduzione alla rete nelle applicazioni Azure Service Fabric Mesh
Questo articolo descrive i diversi tipi di servizi di bilanciamento del carico, come i gateway connettono la rete ad altre reti tramite le applicazioni e come viene eseguito il routing del traffico tra i servizi delle applicazioni.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Confronto tra servizi di bilanciamento del carico di livello 4 e livello 7
Il bilanciamento del carico può essere eseguito in diversi livelli nel [modello OSI](https://en.wikipedia.org/wiki/OSI_model) per la rete, spesso nel livello 4 (L4) e nel livello 7 (L7).  In genere, esistono due tipi di servizi di bilanciamento del carico:

- Un servizio di bilanciamento del carico L4 funziona a livello trasporto/rete, che gestisce il recapito dei pacchetti senza considerare il loro contenuto. Il servizio di bilanciamento del carico controlla solo le intestazioni dei pacchetti, per cui i criteri di bilanciamento sono limitati agli indirizzi IP e alle porte. Ad esempio, un client effettua una connessione TCP al servizio di bilanciamento del carico. Il servizio di bilanciamento del carico termina la connessione (rispondendo direttamente al SYN), seleziona un back-end e crea una nuova connessione TCP al back-end (inviando un nuovo SYN). In genere, un servizio di bilanciamento del carico L4 opera solo al livello di connessione o sessione TCP/UDP L4. In questo modo, il bilanciamento del carico reindirizza i byte, assicurando che i byte della stessa sessione si ritrovino nello stesso back-end. Il servizio di bilanciamento del carico L4 non è a conoscenza di eventuali dettagli dell'applicazione dei byte che sta spostando. I byte possono essere qualsiasi protocollo dell'applicazione.

- Un servizio di bilanciamento del carico L7 funziona allo stesso livello dell'applicazione, che riguarda il contenuto di ciascun pacchetto. Il servizio analizza il contenuto dei pacchetti in quanto riconosce protocolli come HTTP, HTTPS o WebSocket. Questo consente al servizio di bilanciamento del carico di eseguire il routing avanzato. Ad esempio, un client effettua una singola connessione TCP HTTP/2 al servizio di bilanciamento del carico. Il servizio di bilanciamento del carico procede quindi a stabilire due connessioni back-end. Quando il client invia due flussi HTTP/2 al bilanciamento del carico, il flusso uno viene inviato al back-end uno, mentre il flusso due viene inviato al back-end due. Di conseguenza, anche i client di multiplazione che hanno carichi di richieste notevolmente diversi saranno bilanciati in modo adeguato lungo il back-end. 

## <a name="networks-and-gateways"></a>Reti e gateway
Nel [Modello di risorse Service Fabric](service-fabric-mesh-service-fabric-resources.md), una risorsa di rete è distribuibile singolarmente ed è indipendente da una risorsa di un'applicazione o di un servizio che può fare riferimento a essa come dipendenza. Viene usata per creare una rete per le applicazioni aperta a internet. Più servizi di applicazioni diverse possono far parte della stessa rete. Questa rete privata viene creata e gestita da Service Fabric e non è una rete virtuale di Azure (VNET). Le applicazioni possono essere aggiunte e rimosse dalla risorsa di rete in modo dinamico per abilitare e disabilitare la connettività tra reti virtuali. 

Viene sfruttato un gateway per il bridging tra due reti. La risorsa del gateway consente di distribuire un [proxy Envoy](https://www.envoyproxy.io/) che fornisce il routing L4 per qualsiasi protocollo e il routing L7 per un routing avanzato HTTP(S) dell'applicazione. Il gateway esegue il routing del traffico della rete a una rete esterna e determina il servizio a cui eseguire il routing del traffico.  La rete esterna può essere una rete aperta (in pratica, la rete internet pubblica) o una rete virtuale di Azure, permettendo la connessione ad altre applicazioni e risorse di Azure. 

![Rete e gateway][Image1]

Quando viene creata la risorsa di rete con `ingressConfig`, viene assegnato un indirizzo IP pubblico alla risorsa di rete. L'IP pubblico sarà legato alla durata della risorsa di rete.

Quando viene creata un'applicazione Mesh, questa deve fare riferimento a una risorsa di rete esistente. È possibile aggiungere nuove porte pubbliche o rimuovere le porte esistenti dalla configurazione del traffico in ingresso. Non sarà possibile eliminare una risorsa di rete se è un riferimento di una risorsa dell'applicazione. Quando l'applicazione viene eliminata, la risorsa di rete viene rimossa.

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni su mesh Service Fabric leggere la panoramica:
- [Panoramica di mesh Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png