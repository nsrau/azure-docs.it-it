---
title: Modalità servizio nel servizio Azure SignalR
description: Una panoramica delle diverse modalità di servizio nel servizio Azure SignalR, spiega le differenze e gli scenari utente applicabili
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 2cdce64b0ff03521a5848e2b4fd6e01431b5cc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89514842"
---
# <a name="service-mode-in-azure-signalr-service"></a>Modalità servizio nel servizio Azure SignalR

La modalità del servizio è un concetto importante nel servizio Azure SignalR. Quando si crea una nuova risorsa SignalR, verrà richiesto di specificare una modalità di servizio:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Scegliere la modalità del servizio al momento della creazione":::

È anche possibile modificarlo in un secondo momento nel menu impostazioni:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Scegliere la modalità del servizio al momento della creazione":::

Il servizio Azure SignalR supporta attualmente tre modalità di servizio: **predefinita**, senza **Server** e **classica**. La risorsa SignalR si comporterà in modo diverso in modalità diverse. In questo articolo vengono illustrate le differenze e viene illustrato come scegliere la modalità di servizio corretta in base allo scenario.

## <a name="default-mode"></a>Modalità predefinita

La modalità predefinita è il valore predefinito per la modalità del servizio quando si crea una nuova risorsa SignalR. In questa modalità l'applicazione funziona come un'applicazione di SignalR ASP.NET Core (o ASP.NET) tipica, in cui è presente un server Web che ospita un hub (denominato server Hub in seguito) e i client possono avere una comunicazione duplex in tempo reale con il server Hub. L'unica differenza è invece della connessione diretta tra client e server, il client e il server si connettono al servizio SignalR e usano il servizio come proxy. Di seguito è riportato un diagramma che illustra la struttura dell'applicazione tipica in modalità predefinita:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Scegliere la modalità del servizio al momento della creazione":::

Quindi, se si ha un'applicazione SignalR e si vuole eseguire l'integrazione con il servizio SignalR, la modalità predefinita è la scelta ideale per la maggior parte dei casi.

### <a name="connection-routing-in-default-mode"></a>Routing della connessione in modalità predefinita

In modalità predefinita, saranno presenti connessioni WebSocket tra il server Hub e il servizio SignalR (chiamate connessioni server). Queste connessioni vengono utilizzate per trasferire messaggi tra server e client. Quando si connette un nuovo client, il servizio SignalR instrada il client a un server Hub (si supponga di avere più di un server) tramite le connessioni server esistenti. Quindi, la connessione client si atterrà allo stesso server Hub durante la relativa durata. Quando il client invia messaggi, passa sempre allo stesso server Hub. Con questo comportamento, è possibile gestire in modo sicuro alcuni Stati per le singole connessioni nel server Hub. Se ad esempio si vuole trasmettere un flusso tra server e client, non è necessario prendere in considerazione il caso in cui i pacchetti di dati passano a server diversi.

> [!IMPORTANT]
> Questo significa anche che in modalità predefinita il client non è in grado di connettersi senza il server connesso per primo. Se tutti i server Hub sono disconnessi a causa di un'interruzione della rete o del riavvio del server, la connessione del client riceverà un errore che informa che non è presente alcun server connesso. Quindi, è responsabilità dell'utente verificare in qualsiasi momento che sia presente almeno un server hub connesso al servizio SignalR (ad esempio, avere più server Hub e assicurarsi che non si trovino offline allo stesso tempo per operazioni come la manutenzione).

Questo modello di routing significa anche che quando un server Hub passa alla modalità offline, le connessioni instradate verranno eliminate. Pertanto, è necessario aspettarsi che la connessione venga eliminata quando il server Hub è offline per la manutenzione e gestire correttamente la riconnessione in modo che non abbia un impatto negativo sull'applicazione.

## <a name="serverless-mode"></a>Modalità senza server

La modalità senza server, come suggerisce il nome, è una modalità che non può avere alcun server Hub. Se si esegue il confronto con la modalità predefinita, in questa modalità il client non richiede la connessione del server Hub. Tutte le connessioni sono connesse al servizio in modalità "senza server" e il servizio è responsabile della gestione delle connessioni client come la gestione dei ping client (in modalità predefinita, questo viene gestito dai server Hub).

Inoltre, in questa modalità non è disponibile alcuna connessione al server (se si tenta di utilizzare Service SDK per stabilire la connessione al server, verrà ricevuto un errore). Di conseguenza, non sono presenti anche il routing della connessione e la viscosità del client server (come descritto nella sezione modalità predefinita). Tuttavia, è comunque possibile disporre di un'applicazione lato server per eseguire il push dei messaggi ai client. Questa operazione può essere eseguita in due modi, usare le [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) per l'invio monouso o tramite una connessione WebSocket per poter inviare più messaggi in modo più efficiente. si noti che questa connessione WebSocket è diversa dalla connessione al server.

> [!NOTE]
> L'API REST e il modo WebSocket sono supportati in SignalR Service [Management SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md). Se si usa un linguaggio diverso da .NET, è anche possibile richiamare manualmente le API REST dopo questa [specifica](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Se si usa funzioni di Azure, è possibile usare le [associazioni del servizio SignalR per le funzioni di Azure (in](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service) seguito denominate associazione di funzioni) per inviare messaggi come binding di output.

È anche possibile che l'applicazione server riceva messaggi ed eventi di connessione dai client. Il servizio recapita i messaggi e gli eventi di connessione agli endpoint preconfigurati (detti upstream) usando i webhook. Il confronto con la modalità predefinita non garantisce che le richieste di appiccicosità e HTTP risultino meno efficienti rispetto alle connessioni WebSocket.

Per ulteriori informazioni su come configurare upstream, vedere questo [documento](https://docs.microsoft.com/azure/azure-signalr/concept-upstream).

Di seguito è riportato un diagramma che illustra il funzionamento della modalità senza server:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Scegliere la modalità del servizio al momento della creazione":::

> [!NOTE]
> Si noti che in modalità predefinita è anche possibile usare l'API REST/SDK di gestione/binding di funzione per inviare direttamente messaggi al client se non si vuole passare attraverso il server Hub. In modalità predefinita, tuttavia, le connessioni client vengono ancora gestite dai server Hub e upstream non funzionerà in tale modalità.

## <a name="classic-mode"></a>Modalità classica

La versione classica è una modalità mista di modalità predefinita e senza server. In questa modalità, la modalità di connessione viene decisa se è presente un server hub connesso quando viene stabilita la connessione client. Se è presente un server Hub, la connessione client verrà instradata a un server Hub. In caso contrario, entrerà in una modalità senza server in cui non è possibile recapitare il messaggio da client a server al server Hub. Questa operazione causerà alcune discrepanze, ad esempio se tutti i server Hub non sono disponibili per un breve periodo di tempo, tutte le connessioni client create durante tale periodo di tempo saranno in modalità senza server e non potranno inviare messaggi al server Hub.

> [!NOTE]
> La modalità classica è principalmente per la compatibilità con le versioni precedenti per le applicazioni create prima della modalità predefinita e senza server. Si consiglia vivamente di non usare più questa modalità. Per le nuove applicazioni, scegliere predefinito o senza server in base allo scenario. Per le applicazioni esistenti, è inoltre consigliabile esaminare i casi di utilizzo e scegliere una modalità di servizio appropriata.

La modalità classica non supporta inoltre alcune nuove funzionalità, ad esempio upstream, in modalità senza server.

## <a name="choose-the-right-service-mode"></a>Scegliere la modalità di servizio corretta

A questo punto è necessario comprendere le differenze tra le modalità di servizio e sapere come scegliere tra di esse. Come già illustrato nella sezione precedente, la modalità classica non è consigliata ed è consigliabile scegliere solo tra impostazione predefinita e senza server. Di seguito sono riportati alcuni suggerimenti che consentono di effettuare la scelta ideale per le nuove applicazioni e di ritirare la modalità classica per le applicazioni esistenti.

* Se si ha già familiarità con il funzionamento della libreria SignalR e si vuole passare da un SignalR self-hosted per usare il servizio Azure SignalR, scegliere la modalità predefinita. La modalità predefinita funziona esattamente come il SignalR indipendente (ed è possibile usare lo stesso modello di programmazione nella libreria SignalR), il servizio SignalR funge solo da proxy tra i client e i server Hub.

* Se si sta creando una nuova applicazione e non si vogliono gestire le connessioni server e server Hub, scegliere la modalità senza server. Questa modalità funziona in genere con funzioni di Azure, quindi non è necessario gestire alcun server. È comunque possibile disporre di comunicazioni duplex (con API REST/SDK di gestione/associazione di funzioni e upstream), ma il modello di programmazione sarà diverso dalla libreria SignalR.

* Se si dispone di entrambi i server Hub per gestire le connessioni client e l'applicazione back-end per eseguire direttamente il push dei messaggi ai client, ad esempio tramite l'API REST, è comunque necessario scegliere la modalità predefinita. Tenere presente che la differenza principale tra la modalità predefinita e quella senza server è se si dispone di server Hub e come vengono indirizzate le connessioni client. L'API REST/SDK di gestione/associazione di funzione può essere usata in entrambe le modalità.

* Se si dispone di uno scenario misto, ad esempio se si dispone di due hub diversi sulla stessa risorsa SignalR, uno usato come hub SignalR tradizionale e l'altro usato con funzioni di Azure e che non dispone di un server Hub, è opportuno prendere in considerazione la possibilità di separarli in due risorse SignalR, una in modalità predefinita e una in modalità senza server.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare la modalità predefinita e senza server, vedere gli articoli seguenti:

* [Elementi interni del Servizio Azure SignalR](signalr-concept-internals.md)

* [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](signalr-concept-serverless-development-config.md)
