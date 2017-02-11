---
title: Inviare i log del servizio Diagnostica di Azure ad Application Insights | Documentazione Microsoft
description: Configurare i dettagli dei log di diagnostica di Servizi cloud di Azure inviati al portale di Application Insights.
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Configurare la registrazione di Diagnostica di Azure in Application Insights
Quando si configura un progetto di servizi cloud o una macchina virtuale in Microsoft Azure, [Azure può generare un log di diagnostica](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) che è possibile inviare ad Application Insights per poterlo analizzare insieme alla diagnostica e alla telemetria di utilizzo inviate dall'app da Application Insights SDK. Il log di Azure include eventi di gestione dell'app, ad esempio avvio, arresto e arresti anomali, oltre a contatori delle prestazioni. Il log include anche le chiamate dell'app a System.Diagnostics.Trace.

Questo articolo descrive in dettaglio la configurazione dell'acquisizione della diagnostica.

È necessario che Azure SDK 2.8 o versione successiva sia installato in Visual Studio.

## <a name="get-an-application-insights-resource"></a>Ottenere una risorsa di Application Insights
Per un'esperienza ottimale, [aggiungere Application Insights SDK a ogni ruolo dell'app Servizi cloud](app-insights-cloudservices.md) o [a qualsiasi app si eseguirà nella VM](app-insights-overview.md). Sarà quindi possibile inviare i dati di diagnostica da analizzare e visualizzare nella stessa risorsa di Application Insights.

In alternativa, per non usare l'SDK, ad esempio se l'app è già attiva, è sufficiente [creare una nuova risorsa di Application Insights](app-insights-create-new-resource.md) nel portale di Azure. Scegliere **Diagnostica di Azure** come tipo di applicazione.

## <a name="send-azure-diagnostics-to-application-insights"></a>Inviare i dati del servizio Diagnostica di Azure ad Application Insights
Se è possibile aggiornare il progetto di app, in Visual Studio selezionare ogni ruolo, sceglierne le proprietà e nella scheda Configurazione selezionare **Invia i dati di diagnostica ad Application Insights**.

In alternativa, quando si usa il comando Pubblica per caricare l'app, è possibile scegliere l'opzione Application Insights nella pagina Diagnostica.

Se l'app è già attiva, usare Esplora server di Visual Studio o l'esplorazione di Servizi cloud per aprire le proprietà dell'app. Selezionare **Invia i dati di diagnostica ad Application Insights**.

In ogni caso, verranno chiesti i dettagli della risorsa di Application Insights creata.

È consigliabile inviare i dati per ruoli separati a risorse separate. È possibile visualizzare i grafici delle metriche affiancati nel portale creando un [dashboard](app-insights-dashboards.md).

[Altre informazioni sulla configurazione di Application Insights per un'app di servizi cloud](app-insights-cloudservices.md).

## <a name="configuring-the-azure-diagnostics-adapter"></a>Configurazione dell'adattatore diagnostico di Azure
Continuare a leggere solo se si vuole selezionare le parti del log inviate ad Application Insights. Per impostazione predefinita, vengono inviati tutti i dati, inclusi: eventi di Microsoft Azure, contatori delle prestazioni, chiamate di traccia dall'app a System.Diagnostics.Trace.

Quando si modificano le opzioni di diagnostica nell'editor delle proprietà dei ruoli o nella pubblicazione guidata, in realtà si modifica il contenuto di due set di file:

* [File di configurazione della diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) dei ruoli, che è possibile trovare in Esplora soluzioni in `<Your Service>/Roles/*/diagnostics.wadcfgx`.
* File di configurazione del servizio, `ServiceConfiguration.*.cscfg`.

Modificare i file direttamente in quest'ordine per impostare opzioni più specifiche di quelle consentite nelle procedure guidate. Continuare a leggere per altri dettagli. 

## <a name="separate-development-and-production-resources"></a>Separare le risorse di sviluppo e di produzione
È possibile inviare i dati di telemetria dai timbri di sviluppo e produzione dell'app a risorse di Application Insights diverse ed evitare così che i dati di telemetria di sviluppo vengano sovraccaricati dai dati di telemetria live. 

1. [Creare nuove risorse di Application Insights](app-insights-create-new-resource.md) per ogni timbro. Ottenere la chiave di strumentazione dalla scheda Informazioni di base di ogni risorsa.
2. Modificare i due file CSCFG e inserire chiavi di strumentazione diverse.

## <a name="choose-the-priority-levels-to-send"></a>Scegliere i livelli di priorità per l'invio
Nel file di configurazione della diagnostica `diagnostics.wadcfgx` per ogni ruolo è possibile filtrare i messaggi di log per livello.

### <a name="define-a-sink"></a>Definire un sink
`<SinksConfig>` definisce il sink aggiuntivo in cui è possibile inviare i dati di Diagnostica di Azure.  Un esempio di `SinksConfig` è simile al seguente:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` assegna un nome a un flusso di dati che verrà inviato al sink. Il canale agisce come un filtro, mentre l'attributo `loglevel` consente di specificare il livello di log inviato dal canale. I valori disponibili sono: `{Verbose, Information, Warning, Error, Critical}`.

### <a name="send-data-to-the-sink"></a>Inviare dati al sink
Inviare i dati al sink di Application Insights aggiungendo l'attributo sinks nel nodo DiagnosticMonitorConfiguration o a un nodo sottostante. Aggiungendo l'elemento sinks ad ogni nodo, si indica che i dati raccolti dal nodo e da qualsiasi altro nodo sottostante dovranno essere inviati al sink specificato.

Ad esempio, l'impostazione predefinita creata da Azure SDK prevede l'invio di tutti i dati di diagnostica di Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Per inviare invece solo i log degli errori, qualificare il nome del sink con un nome di canale:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Si noti che viene usato il nome del sink che è stato definito, con il nome di un canale definito sopra.

Per inviare ad Application Insights solo registri applicazioni dettagliati, è possibile aggiungere l'attributo sinks al nodo `Logs` .

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

È inoltre possibile includere più sink nella configurazione, a vari livelli della gerarchia. In questo caso, il sink specificato al livello superiore della gerarchia svolge la funzione di impostazione globale, mentre quello specificato al livello di singolo elemento agisce da override dell'impostazione globale.

Ecco un esempio completo del file di configurazione pubblica che invia ad Application Insights tutti i log degli errori (come specificato nel nodo `DiagnosticMonitorConfiguration`) e i registri applicazioni dettagliati (come specificato nel nodo `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
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
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


Esistono tuttavia alcune limitazioni relative a questa funzionalità di cui è importante tenere conto:

* I canali possono essere usati solo con i tipi di log e non con i contatori delle prestazioni. Se si specifica un canale con un elemento contatore delle prestazioni, verrà ignorato.
* Il livello di log per un canale non può superare il livello di log relativo a quanto raccolto da Diagnostica di Azure. Ad esempio: non è possibile raccogliere errori di log applicazioni nell'elemento Logs e provare a inviare log dettagliati alla funzione di sincronizzazione di Application Insight. L'attributo scheduledTransferLogLevelFilter deve sempre raccogliere un numero di log pari o superiore al numero di log che si sta tentando di inviare a un sink.
* Non è possibile inviare ad Application Insights dati BLOB raccolti dall'estensione di Diagnostica di Azure, ad esempio qualsiasi elemento specificato nel nodo Directories. Per i dump di arresto anomalo, il dump effettivo di arresto anomalo del sistema verrà comunque inviato all'archiviazione BLOB e ad Application Insights verrà inviata solo una notifica del dump di arresto anomalo del sistema.

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio di Servizi cloud di Azure con Application Insights](app-insights-cloudservices.md)
* [Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)
* [File di configurazione di Diagnostica di Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


