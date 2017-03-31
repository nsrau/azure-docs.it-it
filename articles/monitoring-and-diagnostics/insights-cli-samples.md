---
title: Esempi di avvio rapido dell&quot;interfaccia della riga di comando di Monitoraggio di Azure. | Microsoft Docs
description: "Comandi dell&quot;interfaccia della riga di comando di esempio per le funzionalità di Monitoraggio di Azure. Monitoraggio di Azure è un servizio di Microsoft Azure che permette di inviare notifiche di avviso, chiamare URL Web in base ai valori dei dati di telemetria configurati e ridimensionare automaticamente servizi cloud, macchine virtuali e app Web."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 0e629dac553f576f2dd3059453b00d6b10e48fd7
ms.lasthandoff: 03/21/2017


---
# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Monitoraggio di Azure
Questo articolo illustra i comandi dell'interfaccia della riga di comando di esempio per accedere alle funzionalità di Monitoraggio di Azure. Monitoraggio di Azure consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web e di inviare notifiche di avviso o chiamare URL Web in base ai valori dei dati di telemetria configurati.

> [!NOTE]
> Dal 25 settembre 2016 Monitoraggio di Azure è il nuovo nome di "Azure Insights". Tuttavia, gli spazi dei nomi e quindi i comandi seguenti contengono ancora il termine "insights".
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Se l'interfaccia della riga di comando di Azure non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md). Se non si ha familiarità con l'interfaccia della riga di comando di Azure, è possibile trovare altre informazioni nell'articolo [Usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

In Windows installare npm dal [sito Web Node.js](https://nodejs.org/). Dopo avere completato l'installazione, usando CMD.exe con i privilegi Esegui come amministratore, eseguire quanto segue dalla cartella di installazione di npm:

```console
npm install azure-cli --global
```

Passare quindi alla cartella o al percorso preferito e digitare nella riga di comando:

```console
azure help
```

## <a name="log-in-to-azure"></a>Accedere ad Azure
Il primo passaggio prevede l'accesso all'account Azure.

```console
azure login
```

Dopo aver eseguito questo comando, è necessario eseguire l'accesso tramite le istruzioni visualizzate sullo schermo. Vengono quindi visualizzati l'account, l'ID tenant e l'ID sottoscrizione predefinito. Tutti i comandi operano nel contesto della sottoscrizione predefinita.

Per elencare i dettagli della sottoscrizione corrente, usare il comando seguente.

```console
azure account show
```

Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente.

```console
azure account set "subscription ID or subscription name"
```

Per usare i comandi di Azure Resource Manager e di Monitoraggio di Azure, è necessario essere in modalità Azure Resource Manager.

```console
azure config mode arm
```

Per visualizzare un elenco di tutti i comandi di Monitoraggio di Azure supportati, eseguire il comando seguente.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Visualizzare il registro attività di una sottoscrizione
Per visualizzare un elenco degli eventi del registro attività, eseguire questo comando.

```console
azure insights logs list [options]
```

Per visualizzare tutte le opzioni disponibili, provare a eseguire questo comando.

```console
azure insights logs list -help
```

Ecco un esempio per elencare i log in base a resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Esempio per elencare i log in base al chiamante

```console
azure insights logs list --caller "myname@company.com"
```

Esempio per elencare i log in base al chiamante in un tipo di risorsa, compresi tra una data di inizio e una di fine

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Usare gli avvisi
È possibile usare le informazioni della sezione per lavorare con gli avvisi.

### <a name="get-alert-rules-in-a-resource-group"></a>Ottenere regole di avviso in un gruppo di risorse
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Creare una regola di avviso metrica
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Creare una regola di avviso log
```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Creare una regola di avviso test Web
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Eliminare una regola di avviso
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Profili dei log
Usare le informazioni di questa sezione per lavorare con i profili dei log.

### <a name="get-a-log-profile"></a>Acquisizione di un profilo di log
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Aggiungere un profilo di log senza conservazione
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Aggiungere un profilo di log con conservazione
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Aggiungere un profilo di log con conservazione e hub eventi
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostica
Usare le informazioni di questa sezione per lavorare con le impostazioni di diagnostica.

### <a name="get-a-diagnostic-setting"></a>Ottenere un'impostazione di diagnostica
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Disabilitare un'impostazione di diagnostica
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Abilitare un'impostazione di diagnostica senza conservazione
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Usare le informazioni di questa sezione per lavorare con le impostazioni di scalabilità automatica. È necessario modificare questi esempi.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Ottenere le impostazioni di scalabilità automatica per un gruppo di risorse
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Ottenere le impostazioni di scalabilità automatica per nome in un gruppo di risorse
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Configurare le impostazioni di scalabilità automatica
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

