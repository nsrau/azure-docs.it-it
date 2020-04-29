---
title: Sessioni di Rendering remoto
description: Descrive l'aspetto di una sessione di rendering remoto
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681688"
---
# <a name="remote-rendering-sessions"></a>Sessioni di Rendering remoto

In Azure Remote rendering (ARR), una *sessione* è un concetto chiave. Questo articolo illustra esattamente la sessione.

## <a name="overview"></a>Panoramica

Il rendering remoto di Azure funziona con l'offload delle attività di rendering complesse nel cloud. Queste attività di rendering non possono essere soddisfatte solo da alcun server, perché la maggior parte dei server cloud non dispone di GPU. A causa della quantità di dati necessari e dei requisiti complessi per la produzione di risultati a frequenze di fotogrammi interattive, la responsabilità del server che gestisce la richiesta dell'utente non può essere passata a un altro computer in tempo reale, come possibile per il traffico Web più comune.

Ciò significa che quando si usa il rendering remoto di Azure, un server cloud con le funzionalità hardware necessarie deve essere riservato esclusivamente per gestire le richieste di rendering. Una *sessione* si riferisce a tutti gli elementi interessati dall'interazione con il server. Inizia con la richiesta iniziale di riservare (*noleggiare*) un computer per l'uso, continua con tutti i comandi per il caricamento e la manipolazione dei modelli e termina con il rilascio del lease nel server cloud.

## <a name="managing-sessions"></a>Gestione delle sessioni

Esistono diversi modi per gestire e interagire con le sessioni. Il modo indipendente dal linguaggio per creare, aggiornare e arrestare le sessioni consiste nell'usare [l'API REST di gestione](../how-tos/session-rest-api.md)delle sessioni. In C# e C++ queste operazioni vengono esposte tramite le `AzureFrontend` classi `AzureSession`e. Per le applicazioni Unity, sono disponibili altre funzioni di utilità `ARRServiceUnity` fornite dal componente.

Una volta stabilita la *connessione* a una sessione attiva, le operazioni come il [caricamento dei modelli](models.md) e l'interazione con la scena `AzureSession` vengono esposte tramite la classe.

### <a name="managing-multiple-sessions-simultaneously"></a>Gestione simultanea di più sessioni

Non è possibile *connettersi* completamente a più sessioni da un dispositivo. Tuttavia, è possibile creare, osservare e arrestare tutte le sessioni desiderate da un'unica applicazione. Fino a quando l'app non è destinata a connettersi a una sessione, non è necessario eseguirla in un dispositivo come HoloLens 2. Un caso di utilizzo per tale implementazione può essere se si desidera controllare le sessioni tramite un meccanismo centrale. Ad esempio, è possibile creare un'app Web, in cui è possibile accedere a più tablet e HoloLenses. Quindi, l'app può visualizzare le opzioni sui tablet, ad esempio il modello CAD da visualizzare. Se un utente effettua una selezione, queste informazioni vengono comunicate a tutti HoloLenses per creare un'esperienza condivisa.

## <a name="session-phases"></a>Fasi della sessione

Ogni sessione viene sottoposta a più fasi.

### <a name="session-startup"></a>Avvio della sessione

Quando si chiede a ARR di [creare una nuova sessione](../how-tos/session-rest-api.md#create-a-session), la prima cosa da fare è restituire un [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)della sessione. Questo UUID consente di eseguire query sulle informazioni relative alla sessione. L'UUID e alcune informazioni di base sulla sessione vengono rese permanente per 30 giorni, pertanto è possibile eseguire query su tali informazioni anche dopo l'arresto della sessione. A questo punto, lo **stato della sessione** verrà segnalato come **avvio**.

Successivamente, il rendering remoto di Azure tenta di trovare un server in grado di ospitare la sessione. Per questa ricerca sono disponibili due parametri. In primo luogo, riserva solo i server nella propria [area](../reference/regions.md). Ciò è dovuto al fatto che la latenza di rete tra le aree può essere troppo elevata per garantire un'esperienza decente. Il secondo fattore è la *dimensione* desiderata specificata. In ogni area è disponibile un numero limitato di server che possono soddisfare la richiesta di dimensioni *standard* o *Premium* . Di conseguenza, se tutti i server della dimensione richiesta sono attualmente in uso nell'area, la creazione della sessione avrà esito negativo. Il motivo dell'errore [può essere](../how-tos/session-rest-api.md#get-sessions-properties)sottoposto a query.

> [!IMPORTANT]
> Se si richiede una dimensione della macchina virtuale *standard* e la richiesta ha esito negativo a causa di una richiesta elevata, questo non implica che la richiesta di un server *Premium* avrà esito negativo. Quindi, se si tratta di un'opzione, è possibile provare a eseguire il fallback a una VM *Premium* .

Quando il servizio trova un server appropriato, deve copiare la macchina virtuale (VM) appropriata per trasformarla in un host di rendering remoto di Azure. Questo processo richiede alcuni minuti. Successivamente, la macchina virtuale viene avviata e le transizioni di **stato della sessione** sono **pronte**.

A questo punto, il server è in attesa esclusivamente dell'input. Si tratta anche del punto da cui viene addebitato il servizio.

### <a name="connecting-to-a-session"></a>Connessione a una sessione

Quando la sessione è *pronta*, è possibile *connettersi* a essa. Durante la connessione, il dispositivo può inviare comandi per caricare e modificare i modelli. Ogni host ARR serve un solo dispositivo client alla volta, quindi quando un client si connette a una sessione, dispone di un controllo esclusivo sul contenuto sottoposto a rendering. Ciò significa anche che le prestazioni di rendering non variano mai per i motivi all'esterno del controllo.

> [!IMPORTANT]
> Anche se un solo client può *connettersi* a una sessione, è possibile eseguire query sulle informazioni di base sulle sessioni, ad esempio lo stato corrente, senza connettersi.

Quando un dispositivo è connesso a una sessione, i tentativi di connessione di altri dispositivi avranno esito negativo. Tuttavia, una volta che il dispositivo connesso si disconnette, volontariamente o a causa di un certo tipo di errore, la sessione accetterà un'altra richiesta di connessione. Tutti gli stati precedenti (modelli caricati e così da) vengono eliminati in modo tale che il successivo dispositivo connesso ottenga uno Slate pulito. Quindi, le sessioni possono essere riutilizzate più volte, da dispositivi diversi e potrebbe essere possibile nascondere l'overhead di avvio della sessione dall'utente finale nella maggior parte dei casi.

> [!IMPORTANT]
> Il server remoto non modifica mai lo stato dei dati sul lato client. Tutte le mutazioni dei dati, ad esempio gli aggiornamenti delle trasformazioni e le richieste di caricamento, devono essere eseguite dall'applicazione client. Tutte le azioni aggiornano immediatamente lo stato del client.

### <a name="session-end"></a>Fine sessione

Quando si richiede una nuova sessione, si specifica un *tempo di lease massimo*, in genere compreso tra uno e otto ore. Si tratta del periodo di tempo durante il quale l'host accetterà l'input.

Per terminare una sessione, è necessario eseguire due motivi regolari. È necessario richiedere manualmente che la sessione venga arrestata o che il tempo massimo di lease sia scaduto. In entrambi i casi, qualsiasi connessione attiva all'host viene chiusa immediatamente e il servizio viene arrestato su tale server. Il server viene quindi restituito al pool di Azure e può essere requisito per altri scopi. L'arresto di una sessione non può essere annullato o annullato. Quando si esegue una query sullo **stato della sessione** in una sessione arrestata, viene restituito **arrestato** o **scaduto**, a seconda che sia stato arrestato manualmente o che sia stato raggiunto il tempo massimo di lease.

Una sessione può anche essere arrestata a causa di un errore.

In tutti i casi, l'addebito non verrà addebitato ulteriormente dopo l'arresto di una sessione.

> [!WARNING]
> Se ci si connette a una sessione e per quanto tempo non influisce sulla fatturazione. Il pagamento per il servizio dipende dalla *durata della sessione*, ovvero dal tempo in cui un server viene riservato esclusivamente e dalle funzionalità hardware richieste (le dimensioni della macchina virtuale). Se si avvia una sessione, connettersi per cinque minuti e quindi non arrestare la sessione, in modo che continui a funzionare fino alla scadenza del lease, verrà addebitato il tempo di lease della sessione completo. Viceversa, il tempo di *lease massimo* è principalmente una rete di sicurezza. Non è importante se si richiede una sessione con un tempo di lease di otto ore, quindi si usa solo per cinque minuti, se si arresta manualmente la sessione in un secondo momento.

#### <a name="extend-a-sessions-lease-time"></a>Estendi ora lease di una sessione

È possibile [estendere il tempo di lease](../how-tos/session-rest-api.md#update-a-session) di una sessione attiva, se si scopre che è necessario più tempo.

## <a name="example-code"></a>Codice di esempio

Il codice seguente illustra una semplice implementazione di avvio di una sessione, in attesa dello stato *pronto* , connessione e disconnessione e chiusura di nuovo.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Più `AzureFrontend` istanze `AzureSession` di e possono essere gestite, modificate ed eseguite su query dal codice. Tuttavia, solo un singolo dispositivo può connettersi a `AzureSession` un alla volta.

La durata di una macchina virtuale non è associata all' `AzureFrontend` istanza o all' `AzureSession` istanza. `AzureSession.StopAsync`deve essere chiamato per arrestare una sessione.

L'ID sessione persistente può essere sottoposto `AzureSession.SessionUUID()` a query tramite e memorizzato nella cache in locale. Con questo ID, un'applicazione può chiamare `AzureFrontend.OpenSession` per eseguire l'associazione a tale sessione.

Quando `AzureSession.IsConnected` è true, `AzureSession.Actions` restituisce un'istanza di `RemoteManager`che contiene le funzioni per [caricare modelli](models.md), modificare le [entità](entities.md)ed [eseguire query sulle informazioni](../overview/features/spatial-queries.md) sulla scena di cui è stato eseguito il rendering.

## <a name="next-steps"></a>Passaggi successivi

* [Entities](entities.md)
* [Modelli](models.md)
