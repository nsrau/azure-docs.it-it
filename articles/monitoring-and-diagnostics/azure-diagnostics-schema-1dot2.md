---
title: Schema di configurazione di Diagnostica di Azure 1.2 | Documentazione Microsoft
description: "Utile SOLO se si usa Azure SDK 2.5 con le macchine virtuali di Azure, il set di scalabilità di macchine virtuali, Service Fabric o servizi Cloud."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: b76ef954d8a00e190817e3d7f8e2b064210d0357
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schema di configurazione di Diagnostica Azure 1.2
> [!NOTE]
> Diagnostica di Azure è il componente usato per raccogliere i contatori delle prestazioni e altre statistiche da Macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e Servizi cloud di Azure.  Questa pagina è utile solo se si usa uno di questi servizi.
>

Lo strumento Diagnostica di Azure viene usato con altri prodotti di diagnostica Microsoft, quali Monitoraggio di Azure, Application Insights e Log Analytics.

Lo schema definisce i possibili valori da utilizzare per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica.  


 Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Per altre informazioni sulla diagnostica di Azure, vedere [Abilitazione di Diagnostica di Azure in Servizi cloud di Azure](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Esempio del file di configurazione della diagnostica  
 L'esempio seguente illustra un tipico file di configurazione della diagnostica:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Spazio dei nomi di configurazione della diagnostica  
 Lo spazio dei nomi XML per il file di configurazione della diagnostica è il seguente:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 Elemento di livello superiore del file di configurazione della diagnostica. La tabella seguente descrive gli elementi del file di configurazione.  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**WadCfg**|Obbligatorio. Impostazioni di configurazione per i dati di telemetria da raccogliere.|  
|**StorageAccount**|Nome dell'account di archiviazione di Azure in cui archiviare i dati. Può anche essere specificato come parametro quando si esegue il cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Directory nella macchina virtuale che l'agente di monitoraggio dovrà usare per archiviare i dati degli eventi. Se non impostata, verrà usata la directory predefinita:<br /><br /> Per un ruolo di lavoro/Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Per una macchina virtuale: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Gli attributi obbligatori sono i seguenti:<br /><br /> -                      **path**: directory nel sistema che dovrà essere usata da Diagnostica di Azure.<br /><br /> -                      **expandEnvironment**: definisce se le variabili di ambiente vengono espanse nel nome del percorso.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
Definisce le impostazioni di configurazione per i dati di telemetria da raccogliere. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Obbligatorio. Gli attributi facoltativi sono i seguenti:<br /><br /> -                     **overallQuotaInMB**: spazio massimo sul disco locale che può essere usato dai vari tipi di dati di diagnostica raccolti da Diagnostica di Azure. L'impostazione predefinita è 5120 MB.<br /><br /> -                     **useProxyServer**: configurare Diagnostica di Azure per l'uso delle impostazioni del server proxy definite nelle impostazioni di Internet Explorer.|  
|**CrashDumps**|Abilitare la raccolta di dump di arresto anomalo del sistema. Gli attributi facoltativi sono i seguenti:<br /><br /> -                     **containerName**: nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i dump di arresto anomalo del sistema.<br /><br /> -                     **crashDumpType**: configura Diagnostica di Azure per la raccolta di dump di arresto anomalo del sistema completi o mini.<br /><br /> -                     **directoryQuotaPercentage**: configura la percentuale di **overallQuotaInMB** da riservare per i dump di arresto anomalo del sistema nella macchina virtuale.|  
|**DiagnosticInfrastructureLogs**|Abilita la raccolta dei log generati da Diagnostica di Azure. I log dell'infrastruttura di diagnostica sono utili per la risoluzione dei problemi del sistema di diagnostica stesso. Gli attributi facoltativi sono i seguenti:<br /><br /> -                     **scheduledTransferLogLevelFilter**: consente di configurare il livello di gravità minimo dei log raccolti.<br /><br /> -                     **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**Directories**|Abilita la raccolta del contenuto di una directory, dei log delle richieste di accesso IIS non riuscite e/o dei log IIS. Attributo facoltativo:<br /><br /> **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwProviders**|Configura la raccolta di eventi ETW da EventSource e/o da provider basati su manifesti ETW.|  
|**Metriche**|Questo elemento consente di generare una tabella di contatori delle prestazioni ottimizzata per le query veloci. Ogni contatore delle prestazioni definito nell'elemento **PerformanceCounters** viene archiviato nella tabella delle metriche oltre che nella tabella dei contatori delle prestazioni. Attributo obbligatorio:<br /><br /> **resourceId**: si tratta dell'ID risorsa della macchina virtuale nella quale si distribuisce Diagnostica di Azure. Ottenere l'attributo **resourceID** dal [portale di Azure](https://portal.azure.com). Selezionare **Esplora** -> **Gruppi di risorse** -> **<Nome\>**. Fare clic sul riquadro **Proprietà** e copiare il valore del campo **ID**.|  
|**PerformanceCounters**|Abilita la raccolta dei contatori delle prestazioni. Attributo facoltativo:<br /><br /> **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**WindowsEventLog**|Abilita la raccolta dei registri eventi di Windows. Attributo facoltativo:<br /><br /> **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un ["Tipo di dati di durata" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Abilita la raccolta di dump di arresto anomalo del sistema. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Obbligatorio. Attributo obbligatorio:<br /><br /> **processName**: nome del processo per il quale Diagnostica di Azure dovrà raccogliere un dump di arresto anomalo del sistema.|  
|**crashDumpType**|Configura Diagnostica di Azure per la raccolta di dump di arresto anomalo del sistema completi o mini.|  
|**directoryQuotaPercentage**|Configura la percentuale di **overallQuotaInMB** da riservare per i dump di arresto anomalo del sistema nella macchina virtuale.|  

## <a name="directories-element"></a>Elemento Directories  
 Abilita la raccolta del contenuto di una directory, dei log delle richieste di accesso IIS non riuscite e/o dei log IIS. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DataSources**|Elenco di directory da monitorare.|  
|**FailedRequestLogs**|Con questo elemento nella configurazione è possibile raccogliere i log relativi alle richieste non riuscite per un'applicazione o un sito IIS. È anche necessario abilitare le opzioni di traccia sotto **system.WebServer** in **Web.config**.|  
|**IISLogs**|Includendo questo elemento nella configurazione viene abilitata la raccolta di log IIS:<br /><br /> **containerName**: nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i log IIS.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Elenco di directory da monitorare. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Obbligatorio. Attributo obbligatorio:<br /><br /> **containerName**: nome del contenitore BLOB dell'account di archiviazione di Azure da usare per archiviare i file log.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 **DirectoryConfiguration** può includere l'elemento **Absolute** o **LocalResource**, ma non entrambi. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**Absolute**|Percorso assoluto della directory da monitorare. Gli attributi seguenti sono obbligatori:<br /><br /> -                     **Path**: percorso assoluto della directory da monitorare.<br /><br /> -                      **expandEnvironment**: definisce se le variabili di ambiente vengono espanse in Path.|  
|**LocalResource**|Percorso relativo di una risorsa locale da monitorare. Gli attributi obbligatori sono i seguenti:<br /><br /> -                     **Name**: nome della risorsa locale che contiene la directory da monitorare<br /><br /> -                     **relativePath**: percorso relativo del nome che contiene la directory da monitorare|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura la raccolta di eventi ETW da EventSource e/o da provider basati su manifesti ETW. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura la raccolta di eventi generati dalla [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Attributo obbligatorio:<br /><br /> **provider**: nome della classe dell'evento EventSource.<br /><br /> Gli attributi facoltativi sono i seguenti:<br /><br /> -                     **scheduledTransferLogLevelFilter**: livello di gravità minimo per il trasferimento nell'account di archiviazione.<br /><br /> -                     **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un [Tipo di dati di durata XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Attributo obbligatorio:<br /><br /> **provider**: GUID del provider di eventi<br /><br /> Gli attributi facoltativi sono i seguenti:<br /><br /> - **scheduledTransferLogLevelFilter**: livello di gravità minimo per il trasferimento nell'account di archiviazione.<br /><br /> -                     **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un [Tipo di dati di durata XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura la raccolta di eventi generati dalla [classe EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DefaultEvents**|Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  
|**Event**|Attributo obbligatorio:<br /><br /> **id**: ID dell'evento.<br /><br /> Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DefaultEvents**|Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  
|**Event**|Attributo obbligatorio:<br /><br /> **id**: ID dell'evento.<br /><br /> Attributo facoltativo:<br /><br /> **eventDestination**: nome della tabella nella quale archiviare gli eventi|  

## <a name="metrics-element"></a>Elemento Metrics  
 Consente di generare una tabella di contatori delle prestazioni ottimizzata per le query veloci. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**MetricAggregation**|Attributo obbligatorio:<br /><br /> **scheduledTransferPeriod**: intervallo tra trasferimenti pianificati per l'archivio, arrotondato per eccesso al minuto più vicino. Il valore è un [Tipo di dati di durata XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Abilita la raccolta dei contatori delle prestazioni. La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Gli attributi seguenti sono obbligatori:<br /><br /> -                     **counterSpecifier**: nome del contatore delle prestazioni. Ad esempio: `\Processor(_Total)\% Processor Time`. Per ottenere un elenco di contatori delle prestazioni nell'host eseguire il comando `typeperf`.<br /><br /> -                     **sampleRate**: frequenza di campionamento del contatore.<br /><br /> Attributo facoltativo:<br /><br /> **unit**: unità di misura del contatore.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**annotation**|Attributo obbligatorio:<br /><br /> **displayName**: nome visualizzato per il contatore<br /><br /> Attributo facoltativo:<br /><br /> **locale**: impostazioni locali da usare quando si visualizza il nome del contatore|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 La tabella seguente descrive gli elementi figlio:  

|Nome dell'elemento|Descrizione|  
|------------------|-----------------|  
|**DataSource**|Registri eventi di Windows da raccogliere. Attributo obbligatorio:<br /><br /> **name**: query XPath che descrive gli eventi di Windows da raccogliere. Ad esempio:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Per raccogliere tutti gli eventi, specificare "*".|

