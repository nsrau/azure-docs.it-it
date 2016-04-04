<properties
   pageTitle="Creare report e verificare l&#39;integrità con Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come inviare report di integrità dal codice del servizio e verificare l&#39;integrità del servizio usando gli strumenti di monitoraggio dello stato forniti da Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/18/2016"
   ms.author="toddabel"/>

# Creare report e verificare l'integrità dei servizi
Quando si verificano problemi nei servizi, la possibilità di rispondere e correggere eventuali interruzioni ed eventi imprevisti correlati dipende dalla capacità di rilevare i problemi in tempi rapidi. Segnalando problemi ed errori allo strumento di gestione dell'integrità di Service Fabric dal codice del servizio, è possibile usare gli strumenti standard di monitoraggio dello stato forniti da Service Fabric per verificare lo stato di integrità.

È possibile segnalare lo stato di integrità dal servizio in due modi.

1. Mediante gli oggetti [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) o [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx). Gli oggetti `Partition` e `CodePackageActivationContext` consentono di creare report sull'integrità degli elementi appartenenti al contesto corrente. Ad esempio, il codice in esecuzione come parte di una replica può segnalare lo stato di integrità solo sulla replica, sulla partizione a cui appartiene e sull'applicazione di cui fa parte.

2. Mediante `FabricClient`. È possibile usare `FabricClient` per segnalare lo stato di integrità dal codice del servizio solo se il cluster non è [sicuro](service-fabric-cluster-security.md) o se il servizio è in esecuzione con privilegi di amministratore. Queste condizioni non si verificano nella maggior parte degli scenari concreti. FabricClient consente di segnalare lo stato di integrità per qualsiasi entità appartenente al cluster. Idealmente il codice del servizio dovrebbe inviare solo report correlati alla propria integrità.

Questo articolo illustra un esempio di creazione di report sull'integrità dal codice del servizio e descrive come verificare lo stato di integrità mediante gli strumenti forniti da Service Fabric. Fornisce inoltre una breve introduzione alle funzionalità di monitoraggio dello stato disponibili in Service Fabric. Per informazioni più dettagliate, è possibile leggere la serie di articoli di approfondimento sull'integrità accessibili dal collegamento presente alla fine di questo documento.

## Prerequisiti
È necessario che siano installati i seguenti elementi:

   * Visual Studio 2015
   * Service Fabric SDK

## Per creare un cluster di sviluppo sicuro locale
Avviare PowerShell con privilegi di amministratore ed eseguire i comandi seguenti.

![Creare un cluster di sviluppo sicuro locale](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## Per distribuire un'applicazione e verificarne l'integrità
Per distribuire un'applicazione e verificarne l'integrità, seguire questa procedura:

1. Avviare Visual Studio come amministratori.

2. Creare un progetto per un servizio con stato.

    ![Creare un'applicazione di Service Fabric con un servizio con stato](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Premere **F5** per eseguire l'applicazione in modalità di debug. L'applicazione verrà distribuita al cluster locale.

4. Dopo aver eseguito l'applicazione, avviare Service Fabric Explorer facendo clic con il pulsante destro del mouse sull'app dell'area di notifica Local Cluster Manager e scegliendo **Gestisci cluster locale** dal menu di scelta rapida.

    ![Avvio di Esplora infrastruttura di servizi dall'area di notifica](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. L'integrità dell'applicazione viene visualizzata come nell'immagine seguente. A questo punto, l'applicazione dovrebbe essere integra e senza errori.

    ![Applicazione integra in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Per verificare l'integrità è possibile usare anche PowerShell. In particolare, è possibile verificare l'integrità di un'applicazione usando ```Get-ServiceFabricApplicationHealth``` e l'integrità del servizio usando ```Get-ServiceFabricServiceHealth```. Il report sull'integrità per la stessa applicazione in PowerShell avrà un aspetto simile al seguente.

    ![Applicazione integra in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Per aggiungere eventi di integrità personalizzati al codice del servizio
I modelli di progetto di Visual Studio per Service Fabric contengono codice di esempio. La procedura seguente illustra come è possibile segnalare eventi di integrità personalizzati dal codice del servizio. I report così creati verranno automaticamente visualizzati negli strumenti standard per il monitoraggio dello stato forniti da Service Fabric, ad esempio Service Fabric Explorer, la vista del portale di Azure relativa allo stato di integrità e PowerShell.

1. Aprire nuovamente l'applicazione creata in Visual Studio o crearne una nuova usando un servizio con stato dai modelli di Visual Studio.

2. Aprire quindi il file **Stateful1.cs** e cercare la chiamata `myDictionary.TryGetValueAsync` nel metodo *RunAsync*. Si noterà che questo metodo restituisce un `result` che contiene il valore corrente del contatore, poiché la logica principale in questa applicazione è quella di mantenere un conteggio in esecuzione. Nel caso di un'applicazione reale con un errore dovuto alla mancanza di risultati, può essere opportuno contrassegnare l'errore tramite lo stato di integrità.

3. Per segnalare un evento di integrità per un errore dovuto alla mancanza di risultati, aggiungere il codice seguente dopo la chiamata a `myDictionary.TryGetValueAsync`. Viene segnalata l'integrità di una replica, perché la segnalazione viene effettuata da un servizio con stato. Il parametro `HealthInformation` archivia informazioni relative al problema di integrità segnalato. Aggiungere questo spazio dei nomi al file **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Aggiungere questo codice dopo la chiamata a `myDictionary.TryGetValueAsync`.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

    Per un servizio senza stato usare il codice seguente.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

    Se il servizio è in esecuzione con privilegi di amministratore o se il cluster non è [sicuro](service-fabric-cluster-security.md), è anche possibile usare FabricClient per segnalare lo stato di integrità, come illustrato di seguito.

    Creare FabricClient dopo la dichiarazione `var myDictionary`

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Aggiungere quindi questo codice dopo la chiamata a `myDictionary.TryGetValueAsync`.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

4. Simulare l'errore e osservare come venga visualizzato negli strumenti di monitoraggio dello stato. Per simulare l'errore, impostare la prima riga come commento nel codice del report di integrità aggiunto in precedenza. Al termine di questa operazione, il codice sarà simile al seguente. In questo modo, il report di integrità verrà generato ogni volta che viene eseguito RunAsync. Dopo aver apportato la modifica, eseguire l'applicazione usando **F5**.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

5. Dopo l'esecuzione dell'applicazione, aprire Esplora infrastruttura di servizi per verificare l'integrità dell'applicazione. In questo caso, Esplora infrastruttura di servizi visualizzerà un errore di integrità per l'applicazione, a causa dell'errore segnalato dal codice aggiunto in precedenza.

    ![Applicazione non integra in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

6. Se si seleziona la replica primaria nella visualizzazione ad albero di Service Fabric Explorer, viene visualizzato anche in questo caso un errore di integrità e vengono mostrati i dettagli del report di integrità aggiunti al parametro `HealthInformation` nel codice. È possibile visualizzare gli stessi report sull'integrità anche in PowerShell e nel portale di Azure.

    ![Integrità della replica in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Questo report rimane nello strumento di gestione dell'integrità finché non viene sostituito da un altro report o la replica non viene eliminata. Poiché per questo report di integrità non è stato impostato un valore TimeToLive nell'oggetto HealthInformation, infatti, il report non avrà scadenza.

È consigliabile creare report sull'integrità al livello più dettagliato possibile, che nel caso precedente corrisponde alla replica. È anche possibile segnalare lo stato di integrità relativo a un oggetto `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Per segnalare lo stato di integrità relativo a `Application`, `DeployedApplication` e `DeployedServicePackage`, usare `CodePackageActivationContext`

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## Passaggi successivi
[Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0323_2016-->