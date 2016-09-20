<properties
   pageTitle="Creare report e verificare l'integrità con Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come inviare report di integrità dal codice del servizio e su come verificare l'integrità del servizio usando gli strumenti di monitoraggio dell'integrità forniti da Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>  

# Creare report e verificare l'integrità dei servizi
Quando si verificano problemi nei servizi, la possibilità di rispondere e correggere interruzioni ed eventi imprevisti dipende dalla capacità di rilevare i problemi in tempi rapidi. Se si segnalano problemi ed errori allo strumento di gestione dell'integrità di Azure Service Fabric dal codice del servizio, è possibile usare gli strumenti standard di monitoraggio dell'integrità forniti da Service Fabric per verificare lo stato di integrità.

È possibile segnalare lo stato di integrità dal servizio in due modi:

- Usare gli oggetti [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) o [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx). È possibile usare gli oggetti `Partition` e `CodePackageActivationContext` per segnalare lo stato di integrità degli elementi appartenenti al contesto corrente. Il codice in esecuzione come parte di una replica può ad esempio segnalare lo stato di integrità solo sulla replica, sulla partizione a cui appartiene e sull'applicazione di cui fa parte.

- Usare `FabricClient`. È possibile usare `FabricClient` per segnalare lo stato di integrità dal codice del servizio se il cluster non è [sicuro](service-fabric-cluster-security.md) o se il servizio è in esecuzione con privilegi di amministratore. Queste condizioni non si verificano nella maggior parte degli scenari concreti. Con `FabricClient` è possibile segnalare lo stato di integrità per qualsiasi entità appartenente al cluster. Idealmente il codice del servizio dovrebbe tuttavia inviare solo report correlati alla propria integrità.

Questo articolo illustra un esempio in cui viene segnalato lo stato di integrità dal codice del servizio. L'esempio illustra anche come usare gli strumenti forniti da Service Fabric per verificare lo stato di integrità. Questo articolo può essere usato come breve introduzione alle funzionalità di monitoraggio dell'integrità di Service Fabric. Per informazioni più dettagliate, è possibile leggere la serie di articoli di approfondimento sull'integrità accessibili dal collegamento presente alla fine di questo documento.

## Prerequisiti
È necessario che siano installati i seguenti elementi:

   * Visual Studio 2015
   * Service Fabric SDK

## Per creare un cluster di sviluppo sicuro locale
- Aprire PowerShell con privilegi di amministratore ed eseguire i comandi seguenti.

![Comandi che mostrano come creare un cluster di sviluppo sicuro locale](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## Per distribuire un'applicazione e verificarne l'integrità

1. Aprire Visual Studio come amministratore.

2. Creare un progetto usando il modello **Servizio con stato**.

    ![Creare un'applicazione di Service Fabric con un servizio con stato](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Premere **F5** per eseguire l'applicazione in modalità di debug. L'applicazione verrà distribuita al cluster locale.

4. Dopo aver eseguito l'applicazione, fare clic con il tasto destro del mouse sull'icona di Local Cluster Manager nell'area di notifica e aprire Service Fabric Explorer selezionando **Gestisci cluster locale** dal menu di scelta rapida.

    ![Apertura di Service Fabric Explorer dall'area di notifica](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. L'integrità dell'applicazione sarà simile a quella visualizzata in questa immagine. A questo punto, l'applicazione dovrebbe essere integra e senza errori.

    ![Applicazione integra in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Per verificare l'integrità è possibile usare anche PowerShell. È possibile verificare l'integrità di un'applicazione usando ```Get-ServiceFabricApplicationHealth``` e l'integrità di un servizio usando ```Get-ServiceFabricServiceHealth```. Il report sullo stato di integrità per la stessa applicazione in PowerShell è visualizzato in questa immagine.

    ![Applicazione integra in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Per aggiungere eventi di integrità personalizzati al codice del servizio
I modelli di progetto di Service Fabric in Visual Studio contengono codice di esempio. La procedura seguente illustra come segnalare eventi di integrità personalizzati dal codice del servizio. I report così creati verranno automaticamente visualizzati negli strumenti standard per il monitoraggio dell'integrità forniti da Service Fabric, ad esempio Service Fabric Explorer, la vista del portale di Azure relativa allo stato di integrità e PowerShell.

1. Aprire nuovamente l'applicazione creata in precedenza in Visual Studio o crearne una nuova usando il modello di Visual Studio **Servizio con stato**.

2. Aprire il file Stateful1.cs e cercare la chiamata `myDictionary.TryGetValueAsync` nel metodo `RunAsync`. Si noterà che questo metodo restituisce un `result` contenente il valore corrente del contatore, poiché la logica principale in questa applicazione è quella di mantenere un conteggio in esecuzione. Nel caso di un'applicazione reale con un errore dovuto alla mancanza di risultati, può essere opportuno contrassegnare l'evento.

3. Per segnalare un evento di integrità per un errore dovuto alla mancanza di risultati, aggiungere la procedura seguente.

    a. Aggiungere lo spazio dei nomi `System.Fabric.Health` al file Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Aggiungere il codice seguente dopo la chiamata di `myDictionary.TryGetValueAsync`

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Viene segnalata l'integrità di una replica, perché la segnalazione viene eseguita da un servizio con stato. Il parametro `HealthInformation` archivia informazioni relative al problema di integrità segnalato.

    Se è stato creato un servizio senza stato, utilizzare il codice seguente.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Se il servizio è in esecuzione con privilegi di amministratore o se il cluster non è [sicuro](service-fabric-cluster-security.md), è anche possibile usare `FabricClient` per segnalare lo stato di integrità, come illustrato nella procedura seguente.

    a. Creare l'istanza di `FabricClient` dopo la dichiarazione `var myDictionary`.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Aggiungere il codice seguente dopo la chiamata di `myDictionary.TryGetValueAsync`.

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

5. Simulare l'errore e osservare come venga visualizzato negli strumenti di monitoraggio dello stato. Per simulare l'errore, impostare la prima riga come commento nel codice del report di integrità aggiunto in precedenza. Al termine di questa operazione, il codice sarà simile all'esempio seguente.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Questo codice genera il report di integrità ogni volta che viene eseguito `RunAsync`. Dopo aver apportato la modifica, premere **F5** per eseguire l'applicazione.

6. Dopo l'esecuzione dell'applicazione, aprire Service Fabric Explorer per verificare l'integrità dell'applicazione. In questo caso in Service Fabric Explorer viene visualizzato un errore di integrità per l'applicazione, a causa dell'errore segnalato dal codice aggiunto in precedenza.

    ![Applicazione non integra in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se si seleziona la replica primaria nella visualizzazione ad albero di Service Fabric Explorer, viene visualizzato anche in questo caso un errore dello **stato di integrità**. In Service Fabric Explorer vengono visualizzati anche i dettagli del report di integrità aggiunti al parametro `HealthInformation` nel codice. È possibile visualizzare gli stessi report di integrità anche in PowerShell e nel portale di Azure.

    ![Integrità della replica in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Questo report rimane nello strumento di gestione dell'integrità finché non viene sostituito da un altro report o finché la replica non viene eliminata. Poiché per questo report di integrità non è stato impostato un valore `TimeToLive` nell'oggetto `HealthInformation`, il report non avrà scadenza.

È consigliabile creare report sull'integrità al livello più dettagliato possibile, che in questo caso corrisponde alla replica. È anche possibile segnalare lo stato di integrità relativo all'oggetto `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Per segnalare lo stato di integrità relativo a `Application`, `DeployedApplication` e `DeployedServicePackage`, usare `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## Passaggi successivi
[Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0907_2016-->