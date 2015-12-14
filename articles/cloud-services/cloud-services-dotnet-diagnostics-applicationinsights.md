<properties
   pageTitle="Usare Application Insights con Diagnostica di Azure per risolvere i problemi del servizio cloud | Microsoft Azure"
   description="Informazioni su come risolvere i problemi relativi al servizio cloud mediante Application Insights per elaborare i dati del servizio Diagnostica di Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="saurabh" />


# Usare Application Insights con Diagnostica di Azure per risolvere i problemi del servizio cloud

Con [Azure SDK 2.8](https://azure.microsoft.com/downloads/) e l'estensione Diagnostica di Azure 1.5 è ora possibile inviare i dati del servizio Diagnostica di Azure per il servizio cloud direttamente ad Application Insights. I vari tipi di log raccolti da Diagnostica di Azure, tra cui log applicazioni, registri eventi di Windows, log ETW e contatori delle prestazioni possono ora essere inviati ad Application Insights e visualizzati nell'interfaccia utente del portale di Application Insights. Quando vengono usati con Application Insights SDK, è possibile ottenere informazioni dettagliate sulle metriche e i log provenienti dall'applicazione, nonché dai dati del livello di sistema e dell'infrastruttura provenienti da Diagnostica di Azure.
  
## Configurare Diagnostica di Azure per inviare dati ad Application Insights

Seguire questi passaggi per configurare il progetto del servizio cloud per l'invio di dati del servizio Diagnostica di Azure ad Application Insights.

1) In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse su un ruolo e scegliere **Proprietà** per aprire la finestra di progettazione dei ruoli
	
![Proprietà del ruolo di Esplora soluzioni][1]

2) Nella finestra di progettazione dei ruoli, nella sezione relativa alla diagnostica, selezionare la casella di controllo **Invia i dati di diagnostica ad Application Insights**

![La finestra di progettazione dei ruoli invia dati di diagnostica ad Application Insights][2]

3) Nella finestra di dialogo visualizzata selezionare la risorsa di Application Insights a cui si desidera inviare i dati del servizio Diagnostica di Azure. La finestra di dialogo consente di selezionare una risorsa di Application Insights esistente dalla sottoscrizione o di specificare manualmente una chiave di strumentazione per una risorsa di Application Insights. Se non si dispone di una risorsa esistente di Application Insights, è possibile crearne una facendo clic sul collegamento **Crea una nuova risorsa**, che apre una finestra del browser che porta al portale di Azure classico, in cui è possibile creare una risorsa di Application Insights. Per altre informazioni sulla creazione di una risorsa di Application Insights, vedere [Creare una nuova risorsa di Application Insights](app-insights-create-new-resource.md)

![selezionare una risorsa di Application Insights][3]

4) Dopo aver aggiunto la risorsa di Application Insights, la chiave di strumentazione per la risorsa viene archiviata come impostazione di configurazione del servizio con il nome **APPINSIGHTS\_INSTRUMENTATIONKEY**. È possibile modificare questa impostazione di configurazione per ogni configurazione o ambiente del servizio selezionando una configurazione diversa dall'elenco Configurazione servizio e specificando una nuova chiave di strumentazione per la configurazione.

![selezionare una configurazione del servizio][4]
	
L'impostazione di configurazione **APPINSIGHTS\_INSTRUMENTATIONKEY** è usata da Visual Studio per configurare l'estensione di diagnostica con le informazioni appropriate sulle risorse di Application Insights durante la pubblicazione. L'impostazione di configurazione è un modo pratico per definire chiavi di strumentazione diverse per configurazioni del servizio diverse. Visual Studio traduce tale impostazione e la inserisce nella configurazione pubblica dell'estensione di diagnostica durante la pubblicazione. Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto di Visual Studio contiene anche il codice XML di configurazione pubblica con la chiave di strumentazione corretta di Application Insights. I file della configurazione pubblica vengono creati nella cartella Extensions e seguono il modello PaaSDiagnostics.<RoleName>.PubConfig.xml. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.

5) Abilitando l'opzione **Invia i dati di diagnostica ad Application Insights**, il servizio Diagnostica di Azure viene configurato automaticamente per inviare tutti i contatori delle prestazioni e i log a livello di errore raccolti dall'agente di Diagnostica di Azure ad Application Insights. Se si desidera configurare ulteriormente i dati inviati ad Application Insights, è necessario modificare manualmente il file *diagnostics.wadcfgx* per ogni ruolo. Per altre informazioni sull'aggiornamento manuale della configurazione, vedere l'articolo relativo alla [configurazione di Diagnostica di Azure per inviare dati ad Application Insights](azure-diagnostics-configure-applicationinsights.md).

Dopo aver configurato il servizio cloud per l'invio di dati del servizio Diagnostica di Azure ad Application Insights, è possibile distribuirlo in Azure normalmente, verificando che l'estensione della diagnostica di Azure sia abilitata. Vedere l'articolo relativo alla [pubblicazione di un servizio cloud con Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## Visualizzazione dei dati del servizio Diagnostica di Azure in Application Insights
La telemetria diagnostica di Azure verrà visualizzata nella risorsa di Application Insights configurata per il servizio cloud.

L'elenco puntato seguente illustra il modo in cui i vari tipi di log di Diagnostica di Azure vengono mappati ai concetti di Application Insights:

-  I contatori delle prestazioni vengono visualizzati come metriche personalizzate in Application Insights
-  I registri eventi di Windows vengono visualizzati come tracce ed eventi personalizzati in Application Insights
-  I log applicazioni, i log ETW e gli eventuali log dell'infrastruttura di diagnostica vengono visualizzati come tracce in Application Insights.

Per visualizzare i dati del servizio Diagnostica di Azure in Application Insights:

- Usare [Esplora metriche](../application-insights/app-insights-metrics-explorer.md) per visualizzare tutti i contatori delle prestazioni personalizzati o i conteggi di diversi tipi di eventi del registro eventi di Windows.

![Metriche personalizzate in Esplora metriche][5]

- Usare [Cerca](../application-insights/app-insights-diagnostic-search.md) per eseguire ricerche nei vari log di traccia inviati da Diagnostica di Azure. Ad esempio, se è presente un'eccezione non gestita in un ruolo che ha causato l'arresto e il riciclo del ruolo, tali informazioni verranno visualizzate nel canale *Applicazione* del *registro eventi di Windows*. È possibile usare la funzionalità di ricerca per esaminare l'errore del registro eventi di Windows e l'analisi dello stack completa per l'eccezione. In questo modo sarà possibile trovare la causa radice del problema. 

![Cerca tracce][6]

## Passaggi successivi

- [Aggiungere Application Insights SDK al servizio cloud](../application-insights/app-insights-cloudservices.md) per inviare dati relativi a richieste, eccezioni, dipendenze ed eventuale telemetria personalizzata dall'applicazione. Uniti ai dati del servizio Diagnostica di Azure, offriranno una visione completa dell'applicazione e del sistema nella stessa risorsa di Application Insight.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_1203_2015-->