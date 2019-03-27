---
title: Inviare le metriche del sistema operativo guest ai Servizi cloud classici dell'archivio delle metriche di Monitoraggio di Azure
description: Inviare le metriche del sistema operativo guest ai Servizi cloud dell'archivio delle metriche di Monitoraggio di Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 90e841628d989a16f504d2efd7a2c7b18335ff48
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482624"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Inviare le metriche del sistema operativo guest ai Servizi cloud classici dell'archivio delle metriche di Monitoraggio di Azure 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L'[estensione Diagnostica](diagnostics-extension-overview.md) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di un cluster di Service Fabric. L'estensione può inviare i dati di telemetria a [molti percorsi diversi](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

Questo articolo descrive il processo di invio delle metriche sulle prestazioni del sistema operativo guest per i Servizi cloud classici di Azure all'archivio delle metriche di Monitoraggio di Azure. A partire dalla versione 1.11 della diagnostica è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. 

L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma. Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione di diagnostica scrivono in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.  

Il processo illustrato in questo articolo funziona solo per i contatori delle prestazioni nei Servizi cloud di Azure, mentre non funziona per altre metriche personalizzate. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre del ruolo di [amministratore del servizio o coamministratore](~/articles/billing/billing-add-change-azure-subscription-administrator.md) nella sottoscrizione di Azure. 

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- È necessario aver installato [Azure PowerShell](/powershell/azure) oppure [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="provision-a-cloud-service-and-storage-account"></a>Eseguire il provisioning del servizio cloud e dell'account di archiviazione 

1. Creare e distribuire un servizio cloud classico. Un esempio dell'applicazione di Servizi cloud classica e della distribuzione è reperibile in [Introduzione a Servizi cloud di Azure e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. È possibile usare un account di archiviazione esistente o distribuirne uno nuovo. È consigliabile che l'account di archiviazione si trovi nella stessa area del servizio cloud classico appena creato. Nel portale di Azure passare al pannello della risorsa **Account di archiviazione** e scegliere **Chiavi**. Annotare il nome dell'account di archiviazione e della chiave dell'account di archiviazione. Queste informazioni sono necessarie nei passaggi successivi.

   ![Chiavi dell'account di archiviazione](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Creare un'entità servizio 

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni riportate in [Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Tenere presente quanto segue durante questo processo: 

- È possibile inserire qualsiasi URL come URL di accesso.  
- Creare un nuovo segreto client per l'app.  
- Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.  

Assegnare all'app creata nel passaggio precedente *Monitoring Metrics Publisher* (Autore delle metriche di monitoraggio) le autorizzazioni per la risorsa di cui si desidera generare le metriche. Se si prevede di usare l'app per generare metriche personalizzate di numerose risorse, è possibile concedere queste autorizzazioni a livello di gruppo di risorse o di sottoscrizione.  

> [!NOTE]
> L'estensione Diagnostica usa l'entità servizio per eseguire l'autenticazione in Monitoraggio di Azure e per generare le metriche per il servizio cloud.

## <a name="author-diagnostics-extension-configuration"></a>Creare la configurazione dell'estensione Diagnostica 

Preparare il file di configurazione dell'estensione Diagnostica. In questo file si stabilisce quali log e contatori delle prestazioni devono essere raccolti dall'estensione Diagnostica per il servizio cloud. Di seguito è riportato un file di configurazione di Diagnostica di esempio:  

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

Nella sezione del file di configurazione in cui si elencano i contatori delle prestazioni da raccogliere, aggiungere il sink di Monitoraggio di Azure. Questa voce assicura che tutti i contatori delle prestazioni specificati vengano instradati verso Monitoraggio di Azure sotto forma di metriche. È possibile aggiungere o rimuovere contatori delle prestazioni in base alle esigenze. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
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

Avviare PowerShell e accedere ad Azure. 

```powershell
Login-AzAccount 
```

Usare i comandi seguenti per archiviare i dettagli dell'account di archiviazione creato in precedenza. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Impostare anche il percorso del file di diagnostica su una variabile usando il comando seguente:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Distribuire l'estensione Diagnostica al servizio cloud con il file di diagnostica con il sink di Monitoraggio di Azure configurato usando il comando seguente:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Resta obbligatorio specificare un account di archiviazione nell'ambito dell'installazione dell'estensione Diagnostica. Tutti i log o i contatori delle prestazioni specificati nel file di configurazione di diagnostica vengono scritti nell'account di archiviazione specificato.  

## <a name="plot-metrics-in-the-azure-portal"></a>Tracciare le metriche nel portale di Azure 

1. Accedere al portale di Azure. 

   ![Metriche nel portale di Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Nel menu a sinistra selezionare **Monitoraggio**.

3. Nel pannello **Monitoraggio** fare clic sulla scheda **Metriche (anteprima)**.

4. Nell'elenco a discesa della risorsa selezionare Servizio cloud (versione classica).

5. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest**. 

6. Nell'elenco a discesa delle metriche selezionare **Memory\Committed Bytes in Use** (Memoria\Byte di cui è stato eseguito il commit). 

È possibile scegliere di visualizzare la memoria totale usata da un ruolo specifico e ogni istanza del ruolo usando le funzionalità di filtro e di suddivisione delle dimensioni. 

 ![Metriche nel portale di Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).

