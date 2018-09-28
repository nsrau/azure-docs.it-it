---
title: Inviare le metriche del sistema operativo guest all'archivio dati di Monitoraggio di Azure per una macchina virtuale Windows (versione classica)
description: Inviare le metriche del sistema operativo guest all'archivio dati di Monitoraggio di Azure per una macchina virtuale Windows (versione classica)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978928"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Inviare le metriche del sistema operativo guest all'archivio dati di Monitoraggio di Azure per una macchina virtuale Windows (versione classica)

L'[estensione Diagnostica di Azure per Windows](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di Service Fabric. L'estensione può inviare i dati di telemetria a molti percorsi diversi elencati nell'articolo indicato in precedenza.

Questo articolo descrive il processo di invio delle metriche sulle prestazioni del sistema operativo guest di una macchina virtuale Windows (versione classica) all'archivio delle metriche di Monitoraggio di Azure. A partire dalla versione 1.11 di WAD, è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma.  Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione WAD scrivono in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure. 

Il processo illustrato in questo articolo funziona solo con le macchine virtuali classiche con il sistema operativo Windows.

## <a name="pre-requisites"></a>Prerequisiti

- È necessario disporre del ruolo di [amministratore del servizio o coamministratore](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) nella sottoscrizione di Azure. 

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- È necessario aver installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) oppure è possibile usare [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Creare una macchina virtuale classica e un account di archiviazione

1. Creare una macchina virtuale classica usando il portale di Azure per la ![creazione di una macchina virtuale classica](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Quando si crea questa macchina virtuale, scegliere di creare un nuovo account di archiviazione classico. Questo account di archiviazione verrà usato nei passaggi successivi.

1. Nel portale di Azure passare al pannello delle risorse dell'account di archiviazione e scegliere le **Chiavi** e annotare il nome e la chiave dell'account di archiviazione. Queste chiavi saranno necessarie nei passaggi successivi ![Chiavi di accesso alle risorse di archiviazione](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni disponibili in [Create a service principal](../azure-resource-manager/resource-group-create-service-principal-portal.md) (Creare un'entità servizio). Tenere presente quanto segue durante questo processo: 
- Creare un nuovo segreto client per l'app  
- Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.

Assegnare all'app le autorizzazioni "Monitoring Metrics Publisher" (Autore delle metriche di monitoraggio) per la risorsa di cui si desidera generare le metriche. È possibile usare un gruppo di risorse o un'intera sottoscrizione.  

> [!NOTE]
> L'Estensione di Diagnostica userà l'entità servizio per l'autenticazione in Monitoraggio di Azure e per generare le metriche per la macchina virtuale classica.

## <a name="author-diagnostics-extension-configuration"></a>Creare la configurazione dell'estensione Diagnostica

1. Preparare il file di configurazione dell'estensione di diagnostica WAD. Questo file determina quali log e contatori delle prestazioni deve raccogliere l'estensione di diagnostica per la macchina virtuale classica. Di seguito è riportato un esempio.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. Nella sezione "SinksConfig" del file di diagnostica definire un nuovo sink di Monitoraggio di Azure:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Nella sezione del file di configurazione in cui è presente l'elenco dei contatori delle prestazioni da raccogliere, indirizzare i contatori delle prestazioni al sink di Monitoraggio di Azure "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Nella configurazione privata definire l'account di Monitoraggio di Azure e aggiungere le informazioni dell'entità servizio da usare per generare le metriche.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Salvare il file in locale.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Distribuire l'Estensione di Diagnostica al servizio Cloud

1. Avviare PowerShell ed eseguire l'accesso

    ```powershell
    Login-AzureRmAccount
    ```

1. Iniziare impostando il contesto per la VM classica

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Impostare il contesto dell'account di archiviazione classico creato con la macchina virtuale.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Impostare il percorso del file di diagnostica su una variabile usando il comando seguente.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Preparare l'aggiornamento alla VM classica con il file di diagnostica con il sink di Monitoraggio di Azure configurato

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuire l'aggiornamento alla VM eseguendo il comando seguente

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Resta obbligatorio specificare un account di archiviazione nell'ambito dell'installazione dell'estensione Diagnostica. Tutti i log e/o i contatori delle prestazioni specificati nel file di configurazione di diagnostica verranno scritti nell'account di archiviazione specificato.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Tracciare le metriche nel portale di Azure

1.  Passare al portale di Azure

1.  Fare clic su Monitoraggio nel menu a sinistra

1.  Nel pannello Monitoraggio fare clic su **Metrica**
   ![Navigate metrics](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png) (Vai alla metrica)

1. Nell'elenco a discesa delle risorse selezionare la macchina virtuale classica

1. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest**

1. Nell'elenco a discesa delle metriche selezionare **Memory\Committed Bytes in Use**
   ![Plot metrics](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png) (Memoria\Byte di cui è stato eseguito il commit in uso>Traccia metriche)


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).
