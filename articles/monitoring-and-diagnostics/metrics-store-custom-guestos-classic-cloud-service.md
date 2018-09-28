---
title: Inviare le metriche del sistema operativo guest al Servizio cloud classico dell'archivio delle metriche di Monitoraggio di Azure
description: Inviare le metriche del sistema operativo guest al Servizio cloud classico dell'archivio delle metriche di Monitoraggio di Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986915"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Inviare le metriche del sistema operativo guest al Servizio cloud classico dell'archivio delle metriche di Monitoraggio di Azure

L'[estensione Diagnostica di Azure per Windows](azure-diagnostics.md) (WAD) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di Service Fabric.  L'estensione può inviare i dati di telemetria a molti percorsi diversi elencati nell'articolo indicato in precedenza.  

Questo articolo descrive il processo di invio delle metriche sulle prestazioni del sistema operativo guest per i servizi cloud (versione classica) di Azure all'archivio delle metriche di Monitoraggio di Azure. A partire dalla versione 1.11 di WAD, è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma.  Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione WAD scrivono in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.  

Il processo illustrato in questo articolo funziona solo per i contatori delle prestazioni nei servizi cloud di Azure, mentre non funziona per altre metriche personalizzate. 
   

## <a name="pre-requisites"></a>Prerequisiti

- È necessario disporre del ruolo di [amministratore del servizio o coamministratore](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) nella sottoscrizione di Azure. 

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- È necessario aver installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) oppure è possibile usare [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 


## <a name="provision-cloud-service-and-storage-account"></a>Eseguire il provisioning del servizio cloud e dell'account di archiviazione 

1. Creare e distribuire un servizio cloud classico (un'applicazione del servizio cloud classico di esempio e la distribuzione sono reperibili [qui](../cloud-services/cloud-services-dotnet-get-started.md)). 

2. È possibile usare un account di archiviazione esistente o distribuirne uno nuovo. È consigliabile che l'account di archiviazione si trovi nella stessa area del servizio cloud classico appena creato. Nel portale di Azure passare al pannello della risorsa Account di archiviazione e scegliere **Chiavi**. Prendere nota del nome e della chiave dell'account di archiviazione, in quanto saranno necessari più avanti.

   ![Chiavi dell'account di archiviazione](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Creare un'entità servizio 

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni disponibili in ../azure/azure-resource-manager/resource-group-create-service-principal-portal. Tenere presente quanto segue durante questo processo: 
  - È possibile inserire qualsiasi URL come URL di accesso.  
  - Creare un nuovo segreto client per l'app  
  - Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.  

Assegnare all'app creata nel passaggio precedente *Monitoring Metrics Publisher* (Autore delle metriche di monitoraggio) le autorizzazioni per la risorsa di cui si desidera generare le metriche. Se si prevede di usare l'app per generare metriche personalizzate di numerose risorse, è possibile concedere queste autorizzazioni a livello di gruppo di risorse o di sottoscrizione.  

> [!NOTE]
> L'estensione Diagnostica usa l'entità servizio per eseguire l'autenticazione in Monitoraggio di Azure e per generare le metriche per il servizio cloud. 

## <a name="author-diagnostics-extension-configuration"></a>Creare la configurazione dell'estensione Diagnostica 

Preparare il file di configurazione dell'estensione di diagnostica WAD. In questo file si stabilisce quali log e contatori delle prestazioni devono essere raccolti dall'estensione Diagnostica per il servizio cloud. Di seguito è riportato un file di configurazione di diagnostica di esempio.  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

Nella sezione "SinksConfig" del file di diagnostica definire un nuovo sink di Monitoraggio di Azure: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Nella sezione del file di configurazione in cui si elencano i contatori delle prestazioni da raccogliere, aggiungere il sink di Monitoraggio di Azure. Questa voce assicura che tutti i contatori delle prestazioni specificati vengano instradati verso Monitoraggio di Azure sotto forma di metriche. È possibile aggiungere o rimuovere i contatori delle prestazioni in base alle esigenze. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Nella configurazione privata infine aggiungere una sezione per l'*account di Monitoraggio di Azure*. Immettere l'ID client dell'entità servizio e il segreto che sono stati creati nel passaggio precedente. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Salvare questo file di diagnostica in locale.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuire l'estensione Diagnostica al servizio cloud 

Avviare PowerShell e accedere ad Azure 

```PowerShell
Login-AzureRmAccount 
```

Archiviare i dettagli sull'account di archiviazione creati nel passaggio precedente in variabili usando i comandi seguenti. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Impostare anche il percorso del file di diagnostica su una variabile usando il comando seguente. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Distribuire l'estensione Diagnostica al servizio cloud con il file di diagnostica con il sink di Monitoraggio di Azure configurato usando il comando seguente. 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Resta obbligatorio specificare un account di archiviazione nell'ambito dell'installazione dell'estensione Diagnostica. Tutti i log e/o i contatori delle prestazioni specificati nel file di configurazione di diagnostica verranno scritti nell'account di archiviazione specificato.  

## <a name="plot-metrics-in-the-azure-portal"></a>Tracciare le metriche nel portale di Azure 

Passare al portale di Azure 

 ![Metriche nel portale di Azure](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. Scegliere Monitoraggio dal menu a sinistra. 

1. Nel pannello Monitoraggio fare clic sulla scheda Metriche (anteprima). 

1. Nell'elenco a discesa della risorsa selezionare Servizio cloud (versione classica). 

1. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest** 

1. Nell'elenco a discesa delle metriche selezionare *Memory\Committed Bytes in Use* (Memoria\Byte di cui è stato eseguito il commit). 

È possibile scegliere di visualizzare la memoria totale usata da un ruolo specifico e ogni istanza del ruolo usando le funzionalità di filtro e di suddivisione delle dimensioni. 

 ![Metriche nel portale di Azure](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).



