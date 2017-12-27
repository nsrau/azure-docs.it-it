---
title: Panoramica del servizio di inoltro di Azure e dei relativi vantaggi | Microsoft Docs
description: Panoramica del servizio di inoltro di Azure
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: d1b1c0661458669dc8f05a49037943320de2ecb3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="what-is-azure-relay"></a>Che cos'è il servizio di inoltro di Azure?

Il servizio di inoltro di Azure semplifica le applicazioni ibride consentendo di esporre in modo sicuro nel cloud pubblico i servizi che risiedono in una rete aziendale, senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale. Il servizio di inoltro supporta un'ampia gamma di protocolli di trasporto e standard dei servizi Web.

Il servizio di inoltro supporta il tradizionale traffico unidirezionale, richiesta/risposta e peer-to-peer. Supporta inoltre la distribuzione degli eventi nell'ambito di Internet per abilitare scenari di pubblicazione/sottoscrizione e le comunicazioni tramite socket bidirezionale per migliorare l'efficienza point-to-point. 

Nel modello di trasferimento dati con inoltro, un servizio locale si connette al servizio di inoltro attraverso una porta in uscita e crea un socket bidirezionale per la comunicazione associato a un determinato indirizzo rendezvous. Il client può quindi comunicare con il servizio locale inviando traffico al servizio di inoltro con l'indirizzo rendezvous come destinazione. Il servizio di inoltro "inoltra" quindi i dati al servizio locale tramite un socket bidirezionale dedicato a ogni client. Il client non necessita di una connessione diretta al servizio locale, non deve conoscere la posizione in cui risiede il servizio e il servizio locale non richiede porte in ingresso aperte sul firewall.

Le principali funzionalità offerte dal servizio di inoltro sono la comunicazione bidirezionale senza buffer attraverso i limiti di rete con limitazione di tipo TCP, individuazione degli endpoint, stato della connettività e sicurezza degli endpoint sovrapposta. Le funzionalità di inoltro si differenziano da quelle delle tecnologie di integrazione a livello di rete come VPN perché l'ambito dell'inoltro può essere limitato a un singolo endpoint applicazione in un singolo computer, mentre la tecnologia VPN ha un impatto molto più elevato perché si basa sulla modifica dell'ambiente di rete.

Il servizio di inoltro di Azure include due funzionalità.

1. [Connessioni ibride](#hybrid-connections): questa funzionalità usa WebSocket basati su standard aperti per supportare scenari multipiattaforma.
2. [Inoltri WCF](#wcf-relays): questa funzionalità usa Windows Communication Foundation (WCF) per supportare chiamate RPC. L'inoltro WCF è la soluzione di inoltro legacy che molti clienti usano già con i modelli di programmazione WCF.

Sia le connessioni ibride che gli inoltri WCF consentono la connessione sicura ad asset presenti all'interno di una rete aziendale. L'uso dell'una o dell'altra funzionalità dipende dalle specifiche esigenze, come illustrato nella tabella seguente:

|  | Inoltro WCF | Connessioni ibride |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Protocollo aperto basato su standard** | |x |
| **Più modelli di programmazione RPC** | |x |

## <a name="hybrid-connections"></a>Connessioni ibride

La funzionalità [Connessioni ibride del servizio di inoltro di Azure](relay-hybrid-connections-protocol.md) è un'evoluzione sicura basata su protocolli aperti delle funzionalità di inoltro esistenti e può essere implementata in qualsiasi piattaforma e linguaggio con funzionalità WebSocket di base. Questo include esplicitamente l'API WebSocket dei comuni Web browser. La funzionalità Connessioni ibride è basata su HTTP e WebSocket.

### <a name="service-history"></a>Storia del servizio

Le connessioni ibride sostituiscono la precedente funzionalità con nome simile di "Servizi BizTalk", basata sull'inoltro WCF del bus di servizio di Azure. La nuova funzionalità Connessioni ibride è complementare all'inoltro WCF esistente e queste due funzionalità coesistono fianco a fianco nel servizio di inoltro di Azure. Condividono un gateway comune, ma costituiscono per il resto implementazioni diverse.

## <a name="wcf-relay"></a>Inoltro WCF

L'inoltro WCF si applica all'intero .NET Framework (NETFX) e a WCF. È possibile avviare la connessione tra il servizio locale e il servizio di inoltro usando una suite di associazioni di "inoltro" WCF. Dietro le quinte, eseguire il mapping delle associazioni di inoltro ai nuovi elementi di associazione di trasporto progettati per creare i componenti di canale WCF che si integrano con il bus di servizio nel cloud. Per altre informazioni, vedere [Introduzione all'inoltro WCF](relay-wcf-dotnet-get-started.md).

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architettura: elaborazione delle richieste di inoltro in ingresso

Quando un client invia una richiesta al servizio di [inoltro di Azure](/azure/service-bus-relay/), Azure Load Balancer instrada la richiesta a uno dei nodi del gateway. Se la richiesta è una richiesta di ascolto, il nodo del gateway crea un nuovo inoltro. Se la richiesta è una richiesta di connessione a un inoltro specifico, il nodo del gateway invia la richiesta di connessione al nodo del gateway che possiede l'inoltro. Il nodo del gateway che possiede l'inoltro invia una richiesta di rendezvous al client di ascolto, chiedendo al listener di creare un canale temporaneo sul nodo del gateway che ha ricevuto la richiesta di connessione.

Quando viene stabilita la connessione di inoltro, i client possono scambiare messaggi tramite il nodo del gateway utilizzato per il rendezvous.

![Elaborazione delle richieste di inoltro Web application firewa in ingresso](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)

