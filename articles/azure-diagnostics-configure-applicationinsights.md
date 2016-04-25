<properties
   pageTitle="Configurare Diagnostica di Azure per l'invio di dati ad Application Insights | Microsoft Azure"
   description="Aggiornare la configurazione pubblica di Diagnostica di Azure per inviare dati ad Application Insights"
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Configurare Diagnostica di Azure per inviare dati ad Application Insights

Diagnostica di Azure archivia i dati in tabelle di Archiviazione di Azure. Se si usa l'estensione 1.5 o successiva di Diagnostica di Azure, tuttavia, è possibile anche inoltrare tutti o un subset di dati ad Application Insights configurando "sink" e "canali" nella configurazione.

Questo articolo descrive come creare la configurazione pubblica per l'estensione di Diagnostica di Azure in modo da predisporla per inviare dati ad Application Insights.

## Configurazione di Application Insights come un sink

L'estensione 1.5 di Diagnostica di Azure introduce l'elemento **<SinksConfig>** nella configurazione pubblica. Definisce inoltre il *sink* aggiuntivo in cui è possibile inviare i dati di Diagnostica di Azure. È possibile specificare i dettagli della risorsa di Application Insights in cui si desidera inviare i dati di Diagnostica di Azure nell'ambito di questo elemento **<SinksConfig>**. Di seguito è riportato un elemento **SinksConfig** di esempio.

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

Per l'elemento **Sink**, l'attributo *nome* specifica un valore stringa che consente di fare riferimento al sink in modo univoco. L'elemento **ApplicationInsights** specifica la chiave di strumentazione della risorsa di Application Insights in cui verranno inviati i dati di Diagnostica di Azure. Se non si dispone di una risorsa di Application Insights, vedere [Creare una nuova risorsa di Application Insights](./application-insights/app-insights-create-new-resource.md) per altre informazioni sulla creazione di una risorsa e l'acquisizione della chiave di strumentazione.

Se si sta sviluppando un progetto di servizio cloud con Azure SDK 2.8, la chiave di strumentazione viene automaticamente popolata nella configurazione pubblica in base all'impostazione di configurazione del servizio **APPINSIGHTS\_INSTRUMENTATIONKEY** definita durante la creazione del progetto di servizio cloud. Vedere [Usare Application Insights con Diagnostica di Azure per risolvere i problemi del servizio cloud](./cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

L'elemento **Channel** consente di definire uno o più elementi **Channel** (canale) per i dati che verranno inviati al sink. Il canale agisce da filtro e consente di selezionare specifici livelli di log da inviare al sink. Ad esempio, è possibile raccogliere log dettagliati e inviarli alla risorsa di archiviazione oppure è possibile scegliere di definire un canale con il livello di log Errore e, nel momento in cui si inviano log tramite il canale, al sink verranno inviati solo i log di errore. Per l'elemento **Channel**, l'attributo *nome* consente di fare riferimento all'elemento in modo univoco, mentre l'attributo *loglevel* consente di specificare il livello di log consentito dal canale. I livelli di log disponibili, in base al livello di dettaglio delle informazioni, sono:
 - Dettagliato
 - Informazioni
 - Avviso
 - Errore
 - Critico

## Inviare dati al sink di Application Insights
Dopo aver definito il sink di Application Insights, è possibile inviare dati al sink aggiungendo l'attributo *sink* agli elementi contenuti nel nodo **DiagnosticMonitorConfiguration**. Aggiungendo l'elemento *sink* ad ogni nodo, si indica che i dati raccolti dal nodo e da qualsiasi altro nodo sottostante dovranno essere inviati al sink specificato.

Ad esempio, se si desidera che vengano inviati tutti i dati raccolti da Diagnostica di Azure, è possibile aggiungere l'attributo *sink* direttamente al nodo **DiagnosticMonitorConfiguration**. Impostare il valore *sinks* sul nome del sink specificato nell'elemento **SinkConfig**.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Se si desidera che al sink di Application Insights vengano inviati solo i log di errore, è possibile impostare il valore *sinks* sul nome del sink, seguito dal nome del canale, separato da un punto ("."). Ad esempio, è possibile inviare al sink di Application Insight solo i log di errore usando il canale MyTopDiagdata definito nell'elemento SinksConfig sopra riportato.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Se si desidera che ad Application Insights vengano inviati solo log applicazioni dettagliati, è possibile aggiungere l'attributo *sinks* al nodo **Logs**.

	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

È inoltre possibile includere più sink nella configurazione, a vari livelli della gerarchia. In questo caso, il sink specificato al livello superiore della gerarchia svolge la funzione di impostazione globale, mentre quello specificato al livello di singolo elemento agisce da override dell'impostazione globale.

Di seguito è riportato un esempio completo del file di configurazione pubblica che consente di inviare ad Application Insights tutti i log di errore (come specificato nel nodo **DiagnosticMonitorConfiguration**) e log applicazioni dettagliati (come specificato nel nodo **Logs**).

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![Configurazione pubblica di Diagnostica](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

Esistono tuttavia alcune limitazioni relative a questa funzionalità di cui è importante tenere conto

- I canali possono essere usati solo con i tipi di log e non con i contatori delle prestazioni. Se si specifica un canale con un elemento contatore delle prestazioni, verrà ignorato.
- Il livello di log per un canale non può superare il livello di log relativo a quanto raccolto da Diagnostica di Azure. Ad esempio: non è possibile raccogliere errori di log applicazioni nell'elemento Logs e provare a inviare log dettagliati al sink di Application Insight. L'attributo *scheduledTransferLogLevelFilter* deve sempre raccogliere un numero di log pari o superiore al numero di log che si sta tentando di inviare a un sink.
- Non è possibile inviare ad Application Insights dati BLOB raccolti dall'estensione di Diagnostica di Azure, ad esempio qualsiasi elemento specificato nel nodo *Directories*. Per i dump di arresto anomalo, il dump effettivo di arresto anomalo del sistema verrà comunque inviato all'archiviazione BLOB e ad Application Insights verrà inviata solo una notifica del dump di arresto anomalo del sistema.


## Passaggi successivi

- Usare [PowerShell](./cloud-services/cloud-services-diagnostics-powershell.md) per abilitare l'estensione di Diagnostica di Azure per un'applicazione. 
- Usare [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) per abilitare l'estensione di Diagnostica di Azure per un'applicazione.

<!---HONumber=AcomDC_0413_2016-->