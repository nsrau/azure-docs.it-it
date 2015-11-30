<properties
   pageTitle="Infrastruttura di servizi - Come creare report e verificare l'integrità | Microsoft Azure"
   description="Questo articolo descrive come inviare report di integrità dal codice del servizio e verificare l'integrità del servizio usando gli strumenti di monitoraggio dello stato forniti dall'infrastruttura di servizi di Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/05/2015"
   ms.author="kunalds"/>


# Creazione di report e verifica dell'integrità del servizio
Quando si verificano problemi dei servizi, la possibilità di rispondere e correggere eventuali interruzioni ed eventi imprevisti correlati dipende dalla capacità di rilevare i problemi rapidamente. Segnalando problemi ed errori a Gestione integrità dell'infrastruttura di servizi dal codice del servizio, è possibile usare gli strumenti standard di monitoraggio dello stato forniti dell'infrastruttura di servizi per verificare lo stato di integrità. Questo documento illustra un esempio per aggiungere un report di integrità a un servizio e descrive come lo stato di integrità possa essere verificato tramite gli strumenti forniti dall'infrastruttura di servizi. Questo articolo intende fornire una rapida introduzione alle funzionalità di monitoraggio dello stato nell'infrastruttura di servizi. Per informazioni più dettagliate, è possibile leggere la serie di articoli di approfondimento sull'integrità a partire dal collegamento alla fine di questo documento.

## Prerequisiti
È necessario che siano installati i prodotti seguenti: * Visual Studio 2015 * Service Fabric SDK

## Distribuire un'applicazione e verificarne l'integrità
Per distribuire un'applicazione e verificarne l'integrità, seguire questa procedura:

1. Avviare Visual Studio come amministratori.

2. Creare un progetto per un servizio con stato.

  ![Creazione di un'applicazione dell'infrastruttura di servizi con il servizio con stato](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Premere F5 per eseguire l'applicazione in modalità debug. L'applicazione verrà distribuita al cluster locale.

4. Dopo l'esecuzione dell'applicazione, avviare Esplora infrastruttura di servizi facendo clic sull'app dell'area di notifica Gestore cluster locale e scegliendo Gestisci cluster locale dal menu di scelta rapida. ![Avvio di Esplora infrastruttura di servizi dall'area di notifica]()

5. L'integrità dell'applicazione viene visualizzata come nell'immagine seguente. A questo punto, l'applicazione dovrebbe essere integra e senza errori.

  ![Applicazione integra in Esplora infrastruttura di servizi](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. È inoltre possibile verificare l'integrità tramite PowerShell. È possibile verificare l'integrità di un'applicazione usando ```Get-ServiceFabricApplicationHealth``` ed è possibile eseguire query sull'integrità del servizio usando ```Get-ServiceFabricServiceHealth```. Il report di integrità per la stessa applicazione in PowerShell è simile al seguente. ![Applicazione integra in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Aggiungere eventi di integrità personalizzati al codice del servizio
I modelli di progetto di Visual Studio per l'infrastruttura di servizi contengono codice di esempio. La procedura seguente illustra come è possibile segnalare eventi di integrità personalizzati dal codice del servizio. Tali report verranno automaticamente visualizzati negli strumenti standard per il monitoraggio dello stato forniti dall'infrastruttura di servizi, ad esempio Esplora infrastruttura di servizi, la visualizzazione dell'integrità del portale di Azure e PowerShell.

1. Aprire nuovamente l'applicazione creata in Visual Studio o crearne una nuova con un servizio con stato dai modelli di Visual Studio.
2. Aprire il file **Stateful1.cs**. Trovare la dichiarazione per `var myDictionary` e aggiungere il codice seguente subito dopo la dichiarazione `var myDictionary`. L'oggetto `fabricClient` creato in questa fase verrà usato inseguito per creare report sull'integrità.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Aggiungere inoltre questo spazio dei nomi al file **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Cercare quindi la chiamata `myDictionary.TryGetValueAsync` nel metodo *RunAsync*. Si noterà che questo metodo restituisce un `result` che contiene il valore corrente del contatore, poiché la logica principale in questa applicazione è quella di mantenere un conteggio in esecuzione. Nel caso di un'applicazione reale con un errore dovuto alla mancanza di risultati, può essere opportuno segnalare l'errore a Gestione integrità.
5. Per segnalare un evento di integrità per un errore dovuto alla mancanza di risultati, aggiungere il codice seguente dopo la chiamata a `myDictionary.TryGetValueAsync`. L'evento viene segnalato come `StatefulServiceReplicaHealthReport` poiché viene segnalato da un servizio con stato. I valori PartitionId e ReplicaId passati all'evento di report consentiranno di identificare l'origine di questo report quando viene visualizzato in uno degli strumenti di monitoraggio dello stato, poiché un servizio con stato distribuito può avere più partizioni e ogni partizione può avere più repliche. Il parametro `HealthInformation` archivia informazioni relative al problema di integrità segnalato. Aggiungere questo spazio dei nomi al file **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Aggiungere questo il codice dopo la chiamata a `myDictionary.TryGetValueAsync`.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Simulare l'errore e notare come viene visualizzato negli strumenti di monitoraggio dello stato. Per simulare l'errore, impostare la prima riga come commento nel codice del report di integrità aggiunto in precedenza. Dopo tale operazione, il codice sarà simile al seguente. In questo modo, il report di integrità verrà generato ogni volta che viene eseguito RunAsync. Dopo aver apportato la modifica, eseguire l'applicazione usando F5.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. Dopo l'esecuzione dell'applicazione, aprire Esplora infrastruttura di servizi per verificare l'integrità dell'applicazione. In questo caso, Esplora infrastruttura di servizi visualizzerà un errore di integrità per l'applicazione, a causa dell'errore segnalato dal codice aggiunto in precedenza. ![Applicazione non integra in Esplora infrastruttura di servizi](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se si seleziona la replica primaria nella visualizzazione ad albero di Esplora infrastruttura di servizi, viene visualizzato anche in questo caso un errore di integrità e vengono inoltre visualizzati i dettagli del report di integrità aggiunti al parametro `HealthInformation` nel codice. È possibile visualizzare gli stessi report di integrità in PowerShell e nel portale di Azure. ![Integrità della replica in Esplora infrastruttura di servizi](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Questo report rimane in Gestione integrità finché non viene sostituito da un altro report o la replica non viene eliminata (*poiché non è stato importato un valore TimeToLive per questo report di integrità nell'oggetto HealthInformation, non avrà scadenza*). Affinché possano essere eseguite query o creati report sull'integrità, FabricClient deve essere in un processo con privilegi di amministratore.

## Passaggi successivi
[Introduzione al monitoraggio dell'integrità dell'infrastruttura di servizi](service-fabric-health-introduction.md)

<!---HONumber=Nov15_HO4-->