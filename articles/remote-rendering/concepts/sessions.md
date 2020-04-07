---
title: Sessioni di rendering remoto
description: Descrive cos'è una sessione di rendering remoto
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681688"
---
# <a name="remote-rendering-sessions"></a>Sessioni di rendering remoto

In Azure Remote Rendering (ARR), una *sessione* è un concetto chiave. Questo articolo spiega cos'è esattamente una sessione.

## <a name="overview"></a>Panoramica

Il rendering remoto di Azure funziona scaricando attività di rendering complesse nel cloud. Queste attività di rendering non possono essere soddisfatte da qualsiasi server, poiché la maggior parte dei server cloud non dispone di GPU. A causa della quantità di dati coinvolti e del requisito rigido di produrre risultati a frame rate interattivi, la responsabilità che il server gestisce quale richiesta utente non può essere consegnata a un'altra macchina in tempo reale, come possibile per il traffico web più comune.

Ciò significa che quando si usa il rendering remoto di Azure, un server cloud con le funzionalità hardware necessarie deve essere riservato esclusivamente per gestire le richieste di rendering. Una *sessione* si riferisce a tutto ciò che riguarda l'interazione con questo server. Inizia con la richiesta iniziale di riservare (*affittare*) una macchina per l'uso, continua con tutti i comandi per il caricamento e la manipolazione dei modelli e termina con il rilascio del lease sul server cloud.

## <a name="managing-sessions"></a>Gestione delle sessioni

Esistono diversi modi per gestire e interagire con le sessioni. La modalità indipendente dalla lingua per la creazione, l'aggiornamento e l'arresto delle sessioni è tramite [l'API REST](../how-tos/session-rest-api.md)di gestione delle sessioni. Queste operazioni vengono esposte tramite le classi `AzureFrontend` `AzureSession`e . Per le applicazioni Unity, sono disponibili `ARRServiceUnity` ulteriori funzioni di utilità fornite dal componente.

Una volta *connessi* a una sessione attiva, operazioni quali il caricamento di `AzureSession` [modelli](models.md) e l'interazione con la scena vengono esposte tramite la classe .

### <a name="managing-multiple-sessions-simultaneously"></a>Gestione simultanea di più sessioni

Non è possibile *connettersi* completamente a più sessioni da un dispositivo. Tuttavia, è possibile creare, osservare e arrestare tutte le sessioni che si desidera da una singola applicazione. Finché l'app non è destinata a connettersi mai a una sessione, non è necessario eseguire su un dispositivo come HoloLens 2, sia. Un caso d'uso per tale implementazione può essere se si desidera controllare le sessioni tramite un meccanismo centrale. Ad esempio, si potrebbe creare un'app Web, in cui possono accedere più tablet e HoloLenses. Quindi l'applicazione può visualizzare le opzioni sui tablet, ad esempio quale modello CAD da visualizzare. Se un utente effettua una selezione, queste informazioni vengono comunicate a tutti gli HoloLenses per creare un'esperienza condivisa.

## <a name="session-phases"></a>Fasi della sessione

Ogni sessione viene sottoposta a più fasi.

### <a name="session-startup"></a>Avvio della sessione

Quando si chiede ad ARR di [creare una nuova sessione,](../how-tos/session-rest-api.md#create-a-session)la prima operazione da fare è restituire una sessione [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Questo UUID consente di eseguire query sulle informazioni sulla sessione. L'UUID e alcune informazioni di base sulla sessione vengono rese persistenti per 30 giorni, pertanto è possibile eseguire una query su tali informazioni anche dopo l'interruzione della sessione. A questo punto, **lo stato** della sessione verrà segnalato come **Avvio**.

Successivamente, il rendering remoto di Azure tenta di trovare un server che può ospitare la sessione. Esistono due parametri per questa ricerca. In primo luogo, riserva solo i server nella tua [regione](../reference/regions.md). Questo perché la latenza di rete tra le aree potrebbe essere troppo alta per garantire un'esperienza decente. Il secondo fattore è la *dimensione* specificata. In ogni regione, esiste un numero limitato di server in grado di soddisfare la richiesta di dimensione *Standard* o *Premium.* Di conseguenza, se tutti i server delle dimensioni richieste sono attualmente in uso nella propria area, la creazione della sessione avrà esito negativo. Il motivo dell'errore [può essere interrogato](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Se si richiede una dimensione di macchina virtuale *Standard* e la richiesta ha esito negativo a causa di una richiesta elevata, ciò non implica che anche la richiesta di un server *Premium* avrà esito negativo. Quindi, se si tratta di un'opzione per te, puoi provare a eseguire il fallback a una macchina virtuale *Premium.*

Quando il servizio trova un server adatto, deve copiare la macchina virtuale (VM) appropriata per trasformarla in un host di rendering remoto di Azure.When the service finds a suitable server, it has to copy the proper virtual machine (VM) onto it to turn it into an Azure Remote Rendering host. Questo processo richiede alcuni minuti. Successivamente viene avviata la macchina virtuale e **lo stato** della sessione passa a **Ready**.

A questo punto, il server è in esclusiva in attesa dell'input. Questo è anche il punto da cui si ottiene fatturato per il servizio.

### <a name="connecting-to-a-session"></a>Connessione a una sessione

Una volta che la sessione è *pronta,* è possibile *connettersi* ad essa. Mentre è connesso, il dispositivo può inviare comandi per caricare e modificare i modelli. Ogni host ARR serve solo un dispositivo client alla volta, quindi quando un client si connette a una sessione, ha il controllo esclusivo sul contenuto di cui è stato eseguito il rendering. Ciò significa anche che le prestazioni di rendering non variano mai per motivi al di fuori del controllo.

> [!IMPORTANT]
> Sebbene solo un client possa *connettersi* a una sessione, è possibile eseguire query sulle informazioni di base sulle sessioni, ad esempio lo stato corrente, senza connettersi.

Mentre un dispositivo è connesso a una sessione, i tentativi di connessione da parte di altri dispositivi avranno esito negativo. Tuttavia, una volta che il dispositivo connesso si disconnette, volontariamente o a causa di un qualche tipo di errore, la sessione accetterà un'altra richiesta di connessione. Tutti gli stati precedenti (modelli caricati e così via) vengono eliminati in modo tale che il dispositivo di connessione successivo ottiene una lavagna pulita. Così le sessioni possono essere riutilizzate molte volte, da dispositivi diversi e potrebbe essere possibile nascondere l'overhead di avvio della sessione dall'utente finale nella maggior parte dei casi.

> [!IMPORTANT]
> Il server remoto non modifica mai lo stato dei dati sul lato client. Tutte le mutazioni dei dati (ad esempio gli aggiornamenti delle trasformazioni e le richieste di caricamento) devono essere eseguite dall'applicazione client. Tutte le azioni aggiornano immediatamente lo stato del client.

### <a name="session-end"></a>Fine sessione

Quando si richiede una nuova sessione, si specifica un *tempo di lease massimo,* in genere compreso tra una e otto ore. Questa è la durata durante la quale l'host accetterà l'input.

Ci sono due motivi regolari per cui una sessione deve terminare. Richiedere manualmente l'arresto della sessione o scade il tempo di lease massimo. In entrambi i casi, qualsiasi connessione attiva all'host viene chiusa immediatamente e il servizio viene arrestato su tale server. Il server viene quindi restituito al pool di Azure e può essere richiesto per altri scopi. L'arresto di una sessione non può essere annullato o annullato. L'esecuzione di query **sullo stato** della sessione in una sessione arrestata restituirà **Arrestato** o **Scaduto**, a seconda che sia stato arrestato manualmente o perché è stato raggiunto il tempo di lease massimo.

Una sessione può anche essere interrotta a causa di un errore.

In tutti i casi, non ti verrà addebitato ulteriormente una volta interrotta una sessione.

> [!WARNING]
> Se ci si connette a una sessione e per quanto tempo non influisce sulla fatturazione. Il pagamento del servizio dipende dalla durata della *sessione,* ovvero il tempo in cui un server è riservato esclusivamente all'utente e dalle funzionalità hardware richieste (dimensioni della macchina virtuale). Se si avvia una sessione, ci si connette per cinque minuti e quindi non si interrompe la sessione, in modo che continui a essere in esecuzione fino alla scadenza del lease, verrà addebitato il tempo di lease completo della sessione. Al contrario, il *tempo massimo* di leasing è per lo più una rete di sicurezza. Non importa se si richiede una sessione con un tempo di lease di otto ore, quindi utilizzarlo solo per cinque minuti, se si interrompe manualmente la sessione in seguito.

#### <a name="extend-a-sessions-lease-time"></a>Estendere il tempo di lease di una sessioneExtend a session's lease time

È possibile [estendere il tempo](../how-tos/session-rest-api.md#update-a-session) di lease di una sessione attiva, se si scopre che è necessario più a lungo.

## <a name="example-code"></a>Codice di esempio

Il codice seguente mostra una semplice implementazione di avvio di una sessione, in attesa dello stato *pronto,* la connessione e quindi la disconnessione e l'arresto di nuovo.

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

Più `AzureFrontend` `AzureSession` e istanze possono essere gestiti, modificati e sottoposti a query dal codice. Ma solo un singolo dispositivo `AzureSession` può connettersi a un alla volta.

La durata di una macchina virtuale non `AzureFrontend` è `AzureSession` legata all'istanza o all'istanza. `AzureSession.StopAsync`deve essere chiamato per interrompere una sessione.

L'ID di sessione persistente `AzureSession.SessionUUID()` può essere sottoposto a query tramite e memorizzato nella cache locale. Con questo ID, un'applicazione può chiamare `AzureFrontend.OpenSession` per eseguire l'associazione a tale sessione.

Quando `AzureSession.IsConnected` è `AzureSession.Actions` true, restituisce un'istanza `RemoteManager`di , che contiene le funzioni per [caricare modelli,](models.md)modificare le [entità](entities.md)e le informazioni [sulle query](../overview/features/spatial-queries.md) sulla scena sottoposta a rendering.

## <a name="next-steps"></a>Passaggi successivi

* [Entities](entities.md)
* [Modelli](models.md)
