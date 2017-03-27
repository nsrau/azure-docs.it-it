---
title: Configurare Diagnostica di Azure per l&quot;invio di dati ad Application Insights | Documentazione Microsoft
description: Aggiornare la configurazione pubblica di Diagnostica di Azure per inviare dati ad Application Insights
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: f6848fef5b23a864496565334b22dc2e2e8d1492
ms.lasthandoff: 03/22/2017


---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Inviare i dati di diagnostica del servizio Cloud, della macchina virtuale o di Service Fabric ad Application Insights
I servizi cloud, le macchine virtuali, il set di scalabilità di macchine virtuali e Service Fabric usano l'estensione Diagnostica di Azure per raccogliere i dati.  La diagnostica di Azure invia i dati alle tabelle di Archiviazione di Azure.  Tuttavia, è possibile anche inviare pipe o un subset di dati ad altri percorsi con l'estensione Diagnostica di Azure 1.5 o versione successiva.

In questo articolo viene descritto come inviare i dati dall'estensione Diagnostica di Azure ad Application Insights.

## <a name="diagnostics-configuration-explained"></a>Descrizione della configurazione di Diagnostica
L'estensione Diagnostica di Azure 1.5 ha introdotto i sink, ovvero percorsi aggiuntivi a cui è possibile inviare i dati di diagnostica.

Esempio di configurazione di un sink per Application Insights:

```XML
    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
```

- L'attributo **Sink** *name* è un valore della stringa che identifica in modo univoco il sink.

- L'elemento **ApplicationInsights** specifica la chiave di strumentazione della risorsa di Application Insights a cui verranno inviati i dati di Diagnostica di Azure.
    - Se non si ha una risorsa di Application Insights, vedere [Creare una risorsa di Application Insights](../application-insights/app-insights-create-new-resource.md) per altre informazioni sulla creazione di una risorsa e l'acquisizione della chiave di strumentazione.
    - Se si sviluppa un servizio Cloud con Azure SDK 2.8 e versioni successive, questa chiave di strumentazione viene compilata automaticamente. Il valore si basa sull'impostazione di configurazione del servizio **APPINSIGHTS_INSTRUMENTATIONKEY** quando si crea il pacchetto del progetto del servizio cloud. Vedere [Usare Application Insights con Diagnostica di Azure per risolvere i problemi del servizio cloud](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

- L'elemento **Channels** contiene uno o più elementi **Channel**.
    - L'attributo *name* fa riferimento in modo univoco a tale canale.
    - L'attributo *loglevel* consente di specificare il livello di log consentito dal canale. I livelli di log disponibili, in base al livello di dettaglio delle informazioni, sono:
        - Dettagliato
        - Informazioni
        - Avviso
        - Errore
        - Critico

Un canale agisce da filtro e consente di selezionare specifici livelli di log da inviare al sink di destinazione. Ad esempio, l'utente può raccogliere log dettagliati inviarli alla risorsa di archiviazione, ma inviare solo gli errori al sink.

Il grafico seguente illustra questa relazione.

![Configurazione pubblica di Diagnostica](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

Nel grafico seguente sono riepilogati i valori di configurazione e il relativo funzionamento. È inoltre possibile includere più sink nella configurazione, a vari livelli della gerarchia. Il sink specificato al livello superiore svolge la funzione di impostazione globale, mentre quello specificato al livello di singolo elemento agisce da override dell'impostazione globale.

![Configurazione dei sink di diagnostica con Application Insights](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Esempio di configurazione del sink completo
Ecco un esempio completo del file di configurazione pubblico che
1. invia tutti gli errori ad Application Insights (specificato nel nodo **DiagnosticMonitorConfiguration**)
2. invia anche i registri di livello Verbose per i log dell'applicazione (specificato nel nodo **Log**).

```XML
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
```

Nella configurazione precedente, le righe riportate di seguito hanno i significati seguenti:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Inviare tutti i dati raccolti dalla diagnostica di Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Inviare solo i log di errore al sink di Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Inviare i log Verbose dell'applicazione ad Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

## <a name="limitations"></a>Limitazioni

- **Unico tipo di log Channels e non per i contatori delle prestazioni.** Se si specifica un canale con un elemento contatore delle prestazioni, verrà ignorato.
- **Il livello di log per un canale non può superare il livello di log relativo a quanto raccolto da Diagnostica di Azure.** Ad esempio, non è possibile raccogliere errori di log applicazioni nell'elemento Los e provare a inviare log Verbose al sink di Application Insight. L'attributo *scheduledTransferLogLevelFilter* deve sempre raccogliere un numero di log pari o superiore al numero di log che si sta tentando di inviare a un sink.
- **Non è possibile inviare ad Application Insights dati BLOB raccolti dall'estensione Diagnostica di Azure.** Ad esempio qualsiasi elemento specificato nel nodo *Directories*. Per i dump di arresto anomalo, il dump effettivo di arresto anomalo viene inviato all'archiviazione BLOB e ad Application Insights viene inviata solo una notifica che attesta la creazione del dump di arresto anomalo.

## <a name="next-steps"></a>Passaggi successivi
* Usare [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) per abilitare l'estensione di Diagnostica di Azure per un'applicazione.
* Usare [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) per abilitare l'estensione di Diagnostica di Azure per un'applicazione.

