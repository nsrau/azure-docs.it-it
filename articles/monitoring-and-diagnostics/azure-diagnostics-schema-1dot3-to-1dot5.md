---
title: Schema di configurazione di Diagnostica di Azure 1.3, 1.4, 1.5 | Documentazione Microsoft
description: Schema versione 1.3, 1.4 e 1.5 per Diagnostica di Azure fornito con Microsoft Azure SDK.
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 550f328ddd03f67419a339aa995920b1feef8d4e


---
# <a name="azure-diagnostics-13-14-15-configuration-schema"></a>Schema di configurazione di Diagnostica di Azure 1.3, 1.4 e 1.5
> [!NOTE]
> Diagnostica di Azure è il componente usato per raccogliere i contatori delle prestazioni e altre statistiche da Macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e Servizi cloud di Azure.  Questa pagina è utile solo se si usa uno di questi servizi.
>

Lo strumento Diagnostica di Azure viene utilizzato in combinazione con altri prodotti di diagnostica Microsoft, quali Monitoraggio di Azure, Application Insights e Log Analytics.

Il file di configurazione di Diagnostica di Azure viene usato per definire le impostazioni di configurazione della diagnostica quando viene avviato il monitor di diagnostica.  

Questa pagina è valida per le versioni 1.3, 1.4 e 1.5 (Azure SDK 2.4 e versioni più recenti). Le sezioni di configurazione più recenti hanno commenti per indicare in quale versione sono state aggiunte.  

 Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Per altre informazioni sulla diagnostica di Azure, vedere [Abilitazione di Diagnostica di Azure in Servizi cloud di Azure](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Esempio del file di configurazione della diagnostica  
 L'esempio seguente illustra un tipico file di configurazione della diagnostica:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
      </SinksConfig>   

    </WadCfg>  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" />  
  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

## <a name="reading-this-page"></a>Come leggere questa pagina  
 I tag seguenti sono più o meno nell'ordine indicato nell'esempio precedente.  Se non si trova rapidamente la descrizione completa, cercare l'elemento o l'attributo nella pagina.  

## <a name="common-attribute-types"></a>Tipi di attributi comuni  
 L'attributo **scheduledTransferPeriod** è presente in diversi elementi. Si tratta dell'intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).

## <a name="diagnostics-configuration-namespace"></a>Spazio dei nomi di configurazione della diagnostica  
 Lo spazio dei nomi XML per il file di configurazione della diagnostica è il seguente:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="diagnosticsconfiguration-element"></a>Elemento DiagnosticsConfiguration  
 Aggiunto nella versione 1.3.  

 Elemento di livello superiore del file di configurazione della diagnostica.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**PublicConfig**|Obbligatorio. Vedere la descrizione altrove in questa pagina.|  
|**PrivateConfig**|Facoltativa. Vedere la descrizione altrove in questa pagina.|  
|**IsEnabled**|Booleano. Vedere la descrizione altrove in questa pagina.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 Descrive la configurazione della diagnostica pubblica.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**WadCfg**|Obbligatorio. Vedere la descrizione altrove in questa pagina.|  
|**StorageAccount**|Nome dell'account di archiviazione di Azure in cui archiviare i dati. Può anche essere specificato come parametro quando si esegue il cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Directory nella macchina virtuale in cui l'agente di monitoraggio archivia i dati degli eventi. Se non impostata, verrà usata la directory predefinita:<br /><br /> Per un ruolo di lavoro/Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Per una macchina virtuale: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Gli attributi obbligatori sono i seguenti:<br /><br /> - **path**: directory nel sistema che dovrà essere usata da Diagnostica di Azure.<br /><br /> - **expandEnvironment**: definisce se le variabili di ambiente vengono espanse nel nome del percorso.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 Identifica e configura i dati di telemetria da raccogliere.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Elemento obbligatorio.<br /><br /> Gli attributi facoltativi sono i seguenti:<br /><br /> - **overallQuotaInMB**: spazio massimo sul disco locale che può essere usato dai vari tipi di dati di diagnostica raccolti da Diagnostica di Azure. L'impostazione predefinita è 5120 MB.<br /><br /> - **useProxyServer**: configurare Diagnostica di Azure per l'uso delle impostazioni del server proxy definite nelle impostazioni di Internet Explorer.|  
|**CrashDumps**|Vedere la descrizione altrove in questa pagina.|  
|**DiagnosticInfrastructureLogs**|Abilita la raccolta dei log generati da Diagnostica di Azure. I log dell'infrastruttura di diagnostica sono utili per la risoluzione dei problemi del sistema di diagnostica stesso. Gli attributi facoltativi sono i seguenti:<br /><br /> - **scheduledTransferLogLevelFilter**: consente di configurare il livello di gravità minimo dei log raccolti.<br /><br /> - **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**Directories**|Vedere la descrizione altrove in questa pagina.|  
|**EtwProviders**|Vedere la descrizione altrove in questa pagina.|  
|**Metriche**|Vedere la descrizione altrove in questa pagina.|  
|**PerformanceCounters**|Vedere la descrizione altrove in questa pagina.|  
|**WindowsEventLog**|Vedere la descrizione altrove in questa pagina.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Abilitare la raccolta di dump di arresto anomalo del sistema.  

|Attributi|Descrizione|  
|----------------|-----------------|  
|**containerName**|Facoltativa. Nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i dump di arresto anomalo del sistema.|  
|**crashDumpType**|Facoltativa.  Configura Diagnostica di Azure per la raccolta di dump di arresto anomalo del sistema completi o mini.|  
|**directoryQuotaPercentage**|Facoltativa.  Configura la percentuale di **overallQuotaInMB** da riservare per i dump di arresto anomalo del sistema nella macchina virtuale.|  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Obbligatorio. Definisce i valori di configurazione di ogni processo.<br /><br /> Anche l'attributo seguente è obbligatorio:<br /><br /> **processName**: nome del processo per il quale Diagnostica di Azure dovrà raccogliere un dump di arresto anomalo del sistema.|  

## <a name="directories-element"></a>Elemento Directories  
 Abilita la raccolta del contenuto di una directory, dei log delle richieste di accesso IIS non riuscite e/o dei log IIS.  

 Attributo **scheduledTransferPeriod** facoltativo. Vedere la spiegazione indicata in precedenza.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**DataSources**|Elenco di directory da monitorare.|  
|**FailedRequestLogs**|Con questo elemento nella configurazione è possibile raccogliere i log relativi alle richieste non riuscite per un'applicazione o un sito IIS. È anche necessario abilitare le opzioni di traccia sotto **system.WebServer** in **Web.config**.|  
|**IISLogs**|Includendo questo elemento nella configurazione viene abilitata la raccolta di log IIS:<br /><br /> **containerName**: nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i log IIS.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Elenco di directory da monitorare.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Obbligatorio. Attributo obbligatorio:<br /><br /> **containerName**: nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i file log.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Può includere l'elemento **Absolute** o **LocalResource**, ma non entrambi.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**Absolute**|Percorso assoluto della directory da monitorare. Gli attributi seguenti sono obbligatori:<br /><br /> - **Path**: percorso assoluto della directory da monitorare.<br /><br /> - **expandEnvironment**: definisce se le variabili di ambiente vengono espanse in Path.|  
|**LocalResource**|Percorso relativo di una risorsa locale da monitorare. Gli attributi obbligatori sono i seguenti:<br /><br /> - **Name**: nome della risorsa locale che contiene la directory da monitorare<br /><br /> - **relativePath**: percorso relativo del nome che contiene la directory da monitorare|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura la raccolta di eventi ETW da EventSource e/o da provider basati su manifesti ETW.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura la raccolta di eventi generati dalla [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Attributo obbligatorio:<br /><br /> **provider**: nome della classe dell'evento EventSource.<br /><br /> Gli attributi facoltativi sono i seguenti:<br /><br /> - **scheduledTransferLogLevelFilter**: livello di gravità minimo per il trasferimento nell'account di archiviazione.<br /><br /> - **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**EtwManifestProviderConfiguration**|Attributo obbligatorio:<br /><br /> **provider**: GUID del provider di eventi<br /><br /> Gli attributi facoltativi sono i seguenti:<br /><br /> - **scheduledTransferLogLevelFilter**: livello di gravità minimo per il trasferimento nell'account di archiviazione.<br /><br /> - **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura la raccolta di eventi generati dalla [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**DefaultEvents**|Attributo facoltativo:<br/><br/> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  
|**Event**|Attributo obbligatorio:<br /><br /> **id**: ID dell'evento.<br /><br /> Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**DefaultEvents**|Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  
|**Event**|Attributo obbligatorio:<br /><br /> **id**: ID dell'evento.<br /><br /> Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  

## <a name="metrics-element"></a>Elemento Metrics  
 Consente di generare una tabella di contatori delle prestazioni ottimizzata per le query veloci. Ogni contatore delle prestazioni definito nell'elemento **PerformanceCounters** viene archiviato nella tabella delle metriche oltre che nella tabella dei contatori delle prestazioni.  

 L'attributo **resourceId** è obbligatorio.  Si tratta dell'ID risorsa della macchina virtuale nella quale si distribuisce Diagnostica di Azure. Ottenere l'attributo **resourceID** dal [portale di Azure](https://portal.azure.com). Selezionare **Esplora** -> **Gruppi di risorse** -> **<Nome\>**. Fare clic sul riquadro **Proprietà** e copiare il valore del campo **ID**.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**MetricAggregation**|Attributo obbligatorio:<br /><br /> **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Abilita la raccolta dei contatori delle prestazioni.  

 Attributo facoltativo:  

 Attributo **scheduledTransferPeriod** facoltativo. Vedere la spiegazione indicata in precedenza.

|Elemento figlio|Descrizione|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Gli attributi seguenti sono obbligatori:<br /><br /> - **counterSpecifier**: nome del contatore delle prestazioni. Ad esempio: `\Processor(_Total)\% Processor Time`. Per ottenere un elenco di contatori delle prestazioni nell'host eseguire il comando `typeperf`.<br /><br /> - **sampleRate**: frequenza di campionamento del contatore.<br /><br /> Attributo facoltativo:<br /><br /> **unit**: unità di misura del contatore.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Abilita la raccolta dei registri eventi di Windows.  

 Attributo **scheduledTransferPeriod** facoltativo. Vedere la spiegazione indicata in precedenza.  

|Elemento figlio|Descrizione|  
|-------------------|-----------------|  
|**DataSource**|Registri eventi di Windows da raccogliere. Attributo obbligatorio:<br /><br /> **name**: query XPath che descrive gli eventi di Windows da raccogliere. Ad esempio:<br /><br /> `Application!*[Application[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[Security[(Level <= 3)]`<br /><br /> Per raccogliere tutti gli eventi, specificare "*".|  

## <a name="logs-element"></a>Elemento Logs  
 Presente nella versione 1.0 e 1.1. Assente nella versione 1.2. Aggiunto nuovamente nella versione 1.3.  

 Definisce la configurazione del buffer per i log di base di Azure.  

|Attributo|Tipo|Descrizione|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Facoltativa. Specifica lo spazio massimo di archiviazione del file system disponibile per i dati specificati.<br /><br /> Il valore predefinito è 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|Facoltativa. Specifica il livello di gravità minimo per le voci di log trasferite. Il valore predefinito è **Non definito**, con il quale verranno trasferiti tutti i log. Altri valori possibili (dal più dettagliato al meno dettagliato) sono **Dettagli**, **Informazioni**, **Avviso**, **Errore** e **Critico**.|  
|**scheduledTransferPeriod**|**duration**|Facoltativa. Specifica l'intervallo tra trasferimenti di dati pianificati, arrotondato per eccesso al minuto più vicino.<br /><br /> Il valore predefinito è PT0S.|  
|**sinks** aggiunto nella versione 1.5|**string**|Facoltativa. Punta a una posizione di sink per l'invio di dati di diagnostica, ad esempio Application Insights.|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 Elenco di posizioni a cui inviare i dati di diagnostica e la configurazione associata a tali posizioni.  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**Sink**|Vedere la descrizione altrove in questa pagina.|  

## <a name="sink-element"></a>Elemento Sink  
 Aggiunto nella versione 1.5.  

 Definisce le posizioni a cui inviare i dati di diagnostica, ad esempio il servizio Application Insights.  

|Attributo|Tipo|Descrizione|  
|---------------|----------|-----------------|  
|**nome**|string|Stringa che identifica il nome del sink.|  

|Elemento|Type|Descrizione|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Usato solo per inviare dati ad Application Insights. Contiene la chiave di strumentazione per un account di Application Insights attivo a cui è possibile accedere.|  
|**Channels**|string|Uno per ogni filtro aggiuntivo per i flussi|  

## <a name="channels-element"></a>Elemento Channels  
 Aggiunto nella versione 1.5.  

 Definisce i filtri per i flussi di dati di log che attraversano un sink.  

|Elemento|Type|Descrizione|  
|-------------|----------|-----------------|  
|**Channel**|string|Vedere la descrizione altrove in questa pagina.|  

## <a name="channel-element"></a>Elemento Channel  
 Aggiunto nella versione 1.5.  

 Definisce le posizioni a cui inviare i dati di diagnostica, ad esempio il servizio Application Insights.  

|Attributi|Type|Descrizione|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Specifica il livello di gravità minimo per le voci di log trasferite. Il valore predefinito è **Non definito**, con il quale verranno trasferiti tutti i log. Altri valori possibili (dal più dettagliato al meno dettagliato) sono **Dettagli**, **Informazioni**, **Avviso**, **Errore** e **Critico**.|  
|**nome**|**string**|Nome univoco per fare riferimento al canale|  

## <a name="privateconfig-element"></a>Elemento PrivateConfig  
 Aggiunto nella versione 1.3.  

 Facoltativo  

 Archivia le informazioni private dell'account di archiviazione, ovvero nome, chiave ed endpoint. Queste informazioni vengono inviate alla macchina virtuale, ma non possono essere recuperate dalla macchina virtuale stessa.  

|Elementi figlio|Descrizione|  
|--------------------|-----------------|  
|**StorageAccount**|Account di archiviazione da usare. Gli attributi seguenti sono obbligatori:<br /><br /> - **name**: nome dell'account di archiviazione.<br /><br /> - **key**: chiave dell'account di archiviazione.<br /><br /> - **endpoint**: endpoint per accedere all'account di archiviazione.|  

## <a name="isenabled-element"></a>Elemento IsEnabled  
 Booleano. Usare `true` per abilitare la diagnostica o `false` per disabilitarla.



<!--HONumber=Jan17_HO5-->


