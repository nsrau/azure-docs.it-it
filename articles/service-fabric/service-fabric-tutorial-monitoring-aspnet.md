---
title: Monitoraggio e diagnostica per i servizi ASP.NET Core in Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare il monitoraggio e la diagnostica per un'applicazione ASP.NET Core di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 68788efffd27edf2813cf455490b651c2c7106a8
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Monitorare e diagnosticare un'applicazione ASP.NET Core in Service Fabric
Questa è la quarta di una serie di esercitazioni. Analizza i passaggi per configurare il monitoraggio e la diagnostica per un'applicazione ASP.NET Core in esecuzione in un cluster di Service Fabric con Application Insights. Verranno raccolti i dati di telemetria dall'applicazione sviluppata nella prima parte dell'esercitazione, [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md). 

Nella quarta parte della serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Configurare Application Insights per l'applicazione in uso
> * Raccogliere i dati di telemetria di risposta per tenere traccia delle comunicazioni basate su HTTP tra i servizi
> * Usare la funzionalità mappa app in Application Insights
> * Aggiungere eventi personalizzati tramite le API di Application Insights

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuire l'applicazione in un cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurare l'integrazione continua e la distribuzione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurare il monitoraggio e la diagnostica per l'applicazione

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio
Se l'applicazione di voto di esempio non è stata compilata nella [parte 1 di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md), è possibile scaricarla. In una finestra di comando o del terminale, eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurare una risorsa di Application Insights
Application Insights è la piattaforma di gestione delle prestazioni delle applicazioni di Azure e la piattaforma consigliata di Service Fabric per il monitoraggio delle applicazioni e la diagnostica. Per creare una risorsa di Application Insights, accedere al [portale di Azure](https://portal.azure.com). Fare clic su **Nuovo** nel menu di navigazione di sinistra per aprire Azure Marketplace. Fare clic su **Monitoraggio e gestione**, quindi su **Application Insights**.

![Creare una nuova risorsa di AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

A questo punto, è necessario immettere le informazioni necessarie sugli attributi della risorsa da creare. Immettere un *Nome*, *Gruppo di risorse* e *Sottoscrizione* appropriati. Impostare il *Percorso* in cui si desidera distribuire il cluster di Service Fabric in futuro. In questa esercitazione l'app verrà distribuita in un cluster locale, pertanto il campo *Percorso* non è rilevante. Nel campo *Tipo di applicazione* dovrebbe essere lasciato "Applicazione Web ASP.NET". 

![Attributi della risorsa di AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Dopo avere compilato le informazioni richieste, fare clic su **Crea** per effettuare il provisioning della risorsa; l'operazione dovrebbe richiedere circa un minuto. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Aggiungere Application Insights ai servizi dell'applicazione

Avviare Visual Studio 2017 con privilegi elevati. A questo scopo, fare clic con il pulsante destro del mouse sull'icona di Visual Studio nel menu Start e scegliere **Esegui come amministratore**. Fare clic su **File** > **Apri** > **Progetto/Soluzione** e passare all'applicazione di voto (creata nella prima parte dell'esercitazione o clonata da git). Aprire *Voting.sln* e se viene richiesto di ripristinare i pacchetti NuGet dell'applicazione, fare clic su **Sì**.

Seguire questi passaggi per configurare Application Insights per i servizi VotingWeb e VotingData:
1. Fare clic sul nome del servizio e fare clic su **Configura Application Insights...** .

    ![Configurare AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Fare clic su **Inizia gratis**.
3. Accedere al proprio account (con il quale è stata configurata anche la sottoscrizione di Azure) e selezionare la sottoscrizione in cui è stata creata la risorsa di Application Insights. Trovare la risorsa in *Risorsa di Application Insights esistente* nell'elenco a discesa "Risorsa". Fare clic su **Registra** per aggiungere Application Insights al servizio.

    ![Registrare AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Fare clic su **Fine** dopo che la finestra di dialogo che viene visualizzata ha completato l'azione.
    
Assicurarsi di eseguire i passaggi precedenti per **entrambi** i servizi dell'applicazione per completare la configurazione di Application Insights per l'applicazione. La stessa risorsa di Application Insights viene usata per entrambi i servizi per visualizzare le richieste in ingresso e in uscita e la comunicazione tra i servizi.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Aggiungere Microsoft.ApplicationInsights.ServiceFabric.Native NuGet ai servizi

Application Insights dispone di due NuGets specifici di Service Fabric, che possono essere usati a seconda dello scenario. Uno viene usato con i servizi nativi di Service Fabric e l'altro con i contenitori e i file eseguibili guest. In questo caso, verrà usato NuGet di Microsoft.ApplicationInsights.ServiceFabric.Native per sfruttare la comprensione del contesto di esecuzione del servizio che offre. Per altre informazioni su Application Insights SDK e NuGets specifici di Service Fabric, vedere [Microsoft Application Insights for Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md) (Microsoft Application Insights per Service Fabric). 

Di seguito sono riportati i passaggi per configurare NuGet:
1. Fare clic con il pulsante destro del mouse sulla **soluzione "Voting"** nella parte superiore di Esplora soluzioni e fare clic su **Gestisci pacchetti NuGet per la soluzione...**.
2. Fare clic su **Sfoglia** nel menu di navigazione superiore della finestra "NuGet - soluzione" e selezionare la casella **Includi versione preliminare** accanto alla barra di ricerca.
3. Cercare `Microsoft.ApplicationInsights.ServiceFabric.Native` e fare clic sul pacchetto NuGet appropriato.
4. Sulla destra, fare clic su due caselle di controllo accanto ai due servizi dell'applicazione, **VotingWeb** e **VotingData** e fare clic su **Installa**.
    ![Registrazione di AI completata](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Fare clic su **OK** nella finestra di dialogo *Rivedi modifiche* visualizzata e selezionare *l'Accettazione della licenza*. In questo modo verrà completata l'aggiunta di NuGet ai servizi.
6. A questo punto, è necessario configurare l'inizializzatore della telemetria nei due servizi. A questo scopo, aprire *VotingWeb.cs* e *VotingData.cs*. Per entrambi, eseguire i due passaggi seguenti:
    1. Aggiungere queste due istruzioni *using* nella parte superiore di ogni  *\<ServiceName>.cs*:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. Nell'istruzione *restituita* annidata di *CreateServiceInstanceListeners()* o *CreateServiceReplicaListeners()*, in servizi *ConfigureServices* > *,* tra i due servizi Singleton dichiarati, aggiungere: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Verrà aggiunto il *Contesto servizio* ai dati di telemetria, consentendo di comprendere meglio l'origine dei dati di telemetria in Application Insights. L'istruzione *restituita* annidata in *VotingWeb.cs* dovrebbe essere simile a quella riportata di seguito:
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Analogamente, in *VotingData.cs*, si dovrebbe avere:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

A questo punto, si è pronti per distribuire l'applicazione. Fare clic su **Start** nella parte superiore (o su **F5**), e Visual Studio eseguirà il build e il pacchetto dell'applicazione, configurerà il cluster locale e distribuirà l'applicazione nello stesso. 

Una volta eseguita la distribuzione dell'applicazione, procedere a [localhost:8080](localhost:8080), dove si dovrebbe poter vedere l'applicazione della pagina singola Esempio di voto. Votare per alcuni elementi diversi di propria scelta per creare alcuni dati e dati di telemetria di esempio; in questo caso sono stati scelti i dessert.

![Voti di esempio di AI](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

È possibile anche *Rimuovere* alcune delle opzioni di voto dopo aver aggiunto alcuni voti.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Visualizzare i dati di telemetria e la mappa app in Application Insights 

Passare alla risorsa di Application Insights nel portale di Azure e, nella barra di spostamento a sinistra della risorsa, fare clic su **Anteprime** in *Configura*. **Attivare** la *Mappa delle applicazioni multiruolo* nell'elenco di anteprime disponibili.

![Abilitare AppMap di AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Fare clic su **Panoramica** per tornare alla pagina di destinazione della risorsa. Quindi fare clic su **Ricerca** nella parte superiore per visualizzare le tracce in arrivo. Sono necessari alcuni minuti per visualizzare le tracce in Application Insights. Nel caso in cui non ne venga visualizzata alcuna, attendere un minuto e premere il pulsante **Aggiorna** nella parte superiore.
![Visualizzazione tracce in AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Scorrendo verso il basso nella finestra *Ricerca* verranno visualizzati tutti i dati di telemetria in ingresso ottenuti in modo predefinito con Application Insights. Per ogni azione eseguita nell'applicazione di voto dovrebbe esserci una richiesta PUT in uscita da *VotingWeb* (PUT Votes/Put [name]), una richiesta PUT in ingresso da *VotingData* (PUT VoteData/Put [name]), seguita da una coppia di richieste GET per l'aggiornamento dei dati visualizzati. Sarà presente, inoltre, una traccia di dipendenza per il protocollo HTTP su localhost, poiché si tratta di richieste HTTP. Di seguito è riportato un esempio di ciò che viene visualizzato per la modalità di aggiunta di un voto: ![traccia di richiesta di esempio AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

È possibile fare clic su una delle tracce per visualizzare ulteriori dettagli al riguardo. Sono presenti informazioni utili sulla richiesta prodotte da Application Insights, compreso il *Tempo di risposta* e l'*URL della richiesta*. Inoltre, poiché è stato aggiunto il NuGet specifico di Service Fabric, verranno prodotti anche i dati relativi all'applicazione nel contesto di un cluster di Service Fabric nella sezione *Dati personalizzati* riportata di seguito. Ciò include il contesto del servizio, in modo da visualizzare il *PartitionID* e *ReplicaId* dell'origine della richiesta e una migliore localizzazione dei problemi durante la diagnosi di errori nell'applicazione.

![Dettagli traccia AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Inoltre, poiché è stata abilitata la mappa app, nella pagina *Panoramica*, facendo clic sull'icona **mappa app** verranno visualizzati entrambi i servizi connessi.

![Dettagli traccia AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

La mappa app può aiutare a comprendere meglio la topologia dell'applicazione, soprattutto quando si inizia ad aggiungere più servizi diversi che interagiscono tra di loro. Inoltre, offre dati di base sulle percentuali di successo della richiesta e può aiutare a diagnosticare le richieste non riuscite per comprendere dove può essersi verificato un errore. Per ulteriori informazioni sull'utilizzo della mappa app, vedere [Mappa delle applicazioni in Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Aggiungere la strumentazione personalizzata all'applicazione

Application Insights offre molti dati di telemetria predefiniti, tuttavia, si potrebbe voler aggiungere ulteriore strumentazione personalizzata, in base alle esigenze dell'azienda o per migliorare la diagnostica in caso di errori nell'applicazione. Application Insights dispone di un'API per l'inserimento di eventi e metrica personalizzati, in merito alla quale sono disponibili altre informazioni [qui](../application-insights/app-insights-api-custom-events-metrics.md).

Aggiungere alcuni eventi personalizzati a *VoteDataController.cs* (in *VotingData* > *Controller*) per tenere traccia del momento in cui i voti vengono aggiunti ed eliminati dal *votesDictionary* sottostante. 
1. Aggiungere `using Microsoft.ApplicationInsights;` al termine delle altre istruzioni using.
2. Dichiarare un nuovo *TelemetryClient* all'inizio della classe, nella creazione di *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Nella funzione *Put()*, aggiungere un evento che confermi l'aggiunta di un voto. Aggiungere `telemetry.TrackEvent($"Added a vote for {name}");` dopo che la transazione è stata completata, immediatamente prima della restituzione dell'istruzione *OkResult*.
4. In *Delete()*, è presente "if/else" in base alla condizione che *votesDictionary* contenga voti per una determinata opzione di voto. 
    1. Aggiungere un evento di conferma dell'eliminazione di un voto nell'istruzione *if*, dopo *await tx.CommitAsync()*: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Aggiungere un evento per indicare che non è stata effettuata l'eliminazione nell'istruzione *else*, prima dell'istruzione restituita:`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Di seguito è riportato un esempio di come potrebbero apparire le funzioni *Put()* e *Delete ()* dopo l'aggiunta degli eventi:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Dopo aver apportato queste modifiche, **Avviare** l'applicazione in modo che compili e distribuisca la versione più recente. Al termine della distribuzione dell'applicazione, passare a [localhost:8080](localhost:8080) e aggiungere ed eliminare alcune opzioni di voto. Quindi, tornare alla risorsa di Application Insights per visualizzare le tracce dell'esecuzione più recente (come prima, possono essere necessari da 1 a 2 minuti per la visualizzazione delle tracce in Application Insights). Dovrebbe essere visualizzato ora un "Evento personalizzato* insieme a tutti i dati di telemetria della risposta, per tutti i voti aggiunti ed eliminati. 

![eventi personalizzati](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:
> [!div class="checklist"]
> * Configurare Application Insights per l'applicazione in uso
> * Raccogliere i dati di telemetria di risposta per tenere traccia delle comunicazioni basate su HTTP tra i servizi
> * Usare la funzionalità mappa app in Application Insights
> * Aggiungere eventi personalizzati tramite le API di Application Insights

Ora che è stata completata la configurazione del monitoraggio e della diagnostica per l'applicazione ASP.NET, procedere come segue:
- [Monitoraggio e diagnostica in Azure Service Fabric](service-fabric-diagnostics-overview.md)
- [Analisi di eventi di Azure Service Fabric con Azure Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Per altre informazioni su Application Insights, vedere [Documentazione di Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/)

