---
title: Che cos'è il servizio di inoltro di Azure? | Microsoft Docs
description: Questo articolo offre una panoramica del servizio Inoltro di Azure, che consente di sviluppare applicazioni cloud che utilizzano servizi locali in esecuzione nella rete aziendale senza aprire una connessione firewall o apportare modifiche di notevole impatto all'infrastruttura di rete.
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 2937d1f665dae03795892f9ff0e8a93a894e1992
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818040"
---
# <a name="what-is-azure-relay"></a>Che cos'è il servizio di inoltro di Azure?
Il servizio Inoltro di Azure consente di esporre in modo sicuro nel cloud pubblico i servizi in esecuzione che vengono eseguiti nella rete aziendale. È possibile eseguire questa operazione senza aprire una porta nel firewall o apportare modifiche di notevole impatto all'infrastruttura di rete aziendale. 

Il servizio di inoltro supporta gli scenari seguenti tra servizi locali e applicazioni in esecuzione nel cloud o in un altro ambiente locale. 

- Comunicazione tradizionale unidirezionale, richiesta/risposta e peer-to-peer 
- Distribuzione di eventi in ambito Internet per abilitare scenari di pubblicazione/sottoscrizione 
- Comunicazione bidirezionale e tramite socket non memorizzato nel buffe tra limiti di rete.

Inoltro di Azure è diverso dalle tecnologie di integrazione a livello di rete come VPN. È possibile limitare un inoltro di Azure a un singolo endpoint applicazione in un singolo computer. La tecnologia VPN ha un impatto di gran lunga maggiore perché si basa sulla modifica dell'ambiente di rete. 

## <a name="basic-flow"></a>Flusso di base
Nel modello di trasferimento dei dati con inoltro i passaggi di base sono i seguenti:

1. Un servizio locale si connette al servizio di inoltro tramite una porta in uscita. 
2. Crea un socket bidirezionale per la comunicazione e lo associa a un indirizzo specifico. 
3. Il client può quindi comunicare con il servizio locale inviando traffico al servizio di inoltro con l'indirizzo come destinazione. 
4. Il servizio di inoltro *inoltra* quindi i dati al servizio locale tramite il socket bidirezionale dedicato al client. Il client non necessita di una connessione diretta al servizio locale. Non deve conoscere la posizione del servizio. Il servizio locale, a sua volta, non richiede la presenza di porte in ingresso aperte nel firewall.


## <a name="features"></a>Funzionalità 
Il servizio di inoltro di Azure include due funzionalità.

- [Connessioni ibride](#hybrid-connections): questa funzionalità usa WebSocket basati su standard aperti per supportare scenari multipiattaforma.
- Inoltri WCF: questa funzionalità usa Windows Communication Foundation (WCF) per supportare chiamate RPC. L'inoltro WCF è la soluzione di inoltro legacy che molti clienti usano già con i modelli di programmazione WCF.

## <a name="hybrid-connections"></a>connessioni ibride

La funzionalità Connessioni ibride inclusa in Inoltro di Azure è un'evoluzione sicura basata su protocolli aperti delle funzionalità di inoltro già esistenti e può essere usata in qualsiasi piattaforma e linguaggio. La funzionalità Connessioni ibride inclusa in Inoltro di Azure è basata sui protocolli HTTP e WebSocket. Consente di inviare richieste e ricevere risposte tramite WebSocket o HTTP(S). Questa funzionalità è compatibile con l'API WebSocket nei comuni Web browser. 

Per informazioni dettagliate sul protocollo Connessioni ibride, vedere [Protocollo per le connessioni ibride di inoltro di Azure](relay-hybrid-connections-protocol.md). È possibile usare Connessioni ibride con qualsiasi libreria WebSocket per qualsiasi linguaggio/runtime.

> [!NOTE]
> Connessioni ibride di Inoltro di Azure sostituisce la vecchia funzionalità Connessioni ibride di Servizi BizTalk. La funzionalità Connessioni ibride in Servizi BizTalk era basata sulla funzionalità Inoltro WCF del bus di servizio di Azure. La funzionalità Connessioni ibride di Inoltro di Azure è complementare a quella Inoltro WCF già esistente e queste due funzionalità coesistono fianco a fianco nel servizio Inoltro di Azure. Condividono un gateway comune, ma costituiscono per il resto implementazioni diverse.

## <a name="wcf-relay"></a>Inoltro WCF
La funzionalità Inoltro WCF è compatibile con la versione completa di .NET Framework e con WCF. È possibile avviare una connessione tra il servizio locale e il servizio di inoltro usando un gruppo di binding di "inoltro" WCF. Viene eseguito il mapping dei binding di inoltro ai nuovi elementi di binding del trasporto progettati per creare i componenti del canale WCF che si integrano con il bus di servizio nel cloud. Per altre informazioni, vedere [Introduzione all'inoltro WCF](relay-wcf-dotnet-get-started.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Connessioni ibride e Inoltro WCF
Sia Connessioni ibride che Inoltro WCF consentono la connessione sicura ad asset presenti all'interno di una rete aziendale. L'uso dell'una o dell'altra funzionalità dipende dalle specifiche esigenze, come illustrato nella tabella seguente:

|  | Inoltro WCF | connessioni ibride |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.JS** | |x |
| **Protocollo aperto basato su standard** | |x |
| **Modelli di programmazione RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architettura: Elaborazione delle richieste di inoltro in ingresso
Il diagramma seguente mostra in che modo il servizio Inoltro di Azure gestisce le richieste di inoltro in ingresso:

![Elaborazione delle richieste di inoltro Web application firewa in ingresso](./media/relay-what-is-it/ic690645.png)

1. Il client in ascolto invia una richiesta di ascolto al servizio Inoltro di Azure. Il servizio di bilanciamento del carico di Azure indirizza la richiesta a uno dei nodi del gateway. 
2. Il servizio Inoltro di Azure crea un inoltro nell'archivio gateway. 
3. Il client di invio invia una richiesta per la connessione al servizio in ascolto. 
4. Il gateway che riceve la richiesta cerca l'inoltro nell'archivio gateway. 
5. Il gateway inoltra la richiesta di connessione al gateway corretto indicato nell'archivio gateway. 
6. Il gateway invia una richiesta al client in ascolto per creare un canale temporaneo con il nodo del gateway più vicino al client di invio. 
7. Il client in ascolto crea un canale temporaneo per il gateway più vicino al client di invio. Ora che la connessione tra i client è stata stabilita tramite un gateway, i client possono scambiarsi messaggi. 
8. Il gateway inoltra i messaggi dal client in ascolto al client di invio. 
9. Il gateway inoltra i messaggi dal client di invio al client in ascolto.  

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a WebSocket per .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione alle richieste HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Introduzione a WebSocket per Node](relay-hybrid-connections-node-get-started.md)
* [Introduzione alle richieste HTTP Node](relay-hybrid-connections-http-requests-node-get-started.md)
* [Domande frequenti sull'inoltro](relay-faq.md)

