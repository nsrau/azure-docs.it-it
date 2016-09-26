<properties
	pageTitle="Azure Insights: esempi di avvio rapido dell'interfaccia della riga di comando di Azure Insights | Microsoft Azure"
	description="I comandi dell'interfaccia della riga di comando di esempio consentono di accedere alle funzionalità di monitoraggio di Azure Insights. Azure Insights è un servizio di Microsoft Azure che permette di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web, di inviare notifiche di avviso o di chiamare URL Web in base ai valori dei dati di telemetria configurati."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="ashwink"/>

# Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Azure Insights

Questo articolo illustra i comandi dell'interfaccia della riga di comando di esempio per accedere alle funzionalità di monitoraggio di Azure Insights. Azure Insights consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web oltre che inviare notifiche di avviso o chiamare URL Web in base ai valori di dati di telemetria configurati.


## Prerequisiti

Se l'interfaccia della riga di comando di Azure non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Se non si ha familiarità con l'interfaccia della riga di comando di Azure, è possibile trovare altre informazioni nell'articolo [Usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


In Windows installare npm dal [sito Web Node.js](https://nodejs.org/). Dopo avere completato l'installazione, usando CMD.exe con i privilegi Esegui come amministratore, eseguire quanto segue dalla cartella di installazione di npm:

```
npm install azure-cli --global
```

Passare quindi alla cartella o al percorso preferito e digitare nella riga di comando:

```
azure help
```

## Accedere ad Azure

Il primo passaggio prevede l'accesso all'account Azure.

```
azure login
```

Dopo aver eseguito questo comando, è necessario eseguire l'accesso tramite le istruzioni visualizzate sullo schermo. Vengono quindi visualizzati l'account, l'ID tenant e l'ID sottoscrizione predefinito. Tutti i comandi operano nel contesto della sottoscrizione predefinita.

Per elencare i dettagli della sottoscrizione corrente, usare il comando seguente.

```
azure account show
```

Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente.

```
azure account set "subscription ID or subscription name"
```

Per usare i comandi di Azure Resource Manager e di Azure Insights, è necessario essere in modalità Azure Resource Manager.

```
azure config mode arm
```

Per visualizzare un elenco di tutti i comandi supportati di Azure Insights, eseguire questo comando.

```
azure insights
```

## Visualizzare i log di controllo per una sottoscrizione

Per visualizzare un elenco di log di controllo, eseguire questo comando.

```
azure insights logs list [options]
```

Per visualizzare tutte le opzioni disponibili, provare a eseguire questo comando.

```
azure insights logs list -help
```

Ecco un esempio per elencare i log in base a resourceGroup

```
azure insights logs list --resourceGroup "myrg1"
```

Esempio per elencare i log in base al chiamante

```
azure insights logs list --caller "myname@company.com"
```

Esempio per elencare i log in base al chiamante in un tipo di risorsa, compresi tra una data di inizio e una di fine

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Usare gli avvisi
È possibile usare le informazioni della sezione per lavorare con gli avvisi.

### Ottenere regole di avviso in un gruppo di risorse

```
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Creare una regola di avviso metrica

```
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Creare una regola di avviso log

```
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Creare una regola di avviso test Web

```
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Eliminare una regola di avviso

```
azure insights alerts rule delete abhingrgtest123 andy0323
```

## Profili dei log
Usare le informazioni di questa sezione per lavorare con i profili dei log.

### Acquisizione di un profilo di log

```
azure insights logprofile list
azure insights logprofile get -n default
```


### Aggiungere un profilo di log senza conservazione

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Rimozione di un profilo di log

```
azure insights logprofile delete --name default
```

### Aggiungere un profilo di log con conservazione

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Aggiungere un profilo di log con conservazione e hub eventi

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Diagnostica
Usare le informazioni di questa sezione per lavorare con le impostazioni di diagnostica.

### Ottenere un'impostazione di diagnostica

```
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Disabilitare un'impostazione di diagnostica

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Abilitare un'impostazione di diagnostica senza conservazione

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
Usare le informazioni di questa sezione per lavorare con le impostazioni di scalabilità automatica. È necessario modificare questi esempi.

### Ottenere le impostazioni di scalabilità automatica per un gruppo di risorse

```
azure insights autoscale setting list montest2
```

### Ottenere le impostazioni di scalabilità automatica per nome in un gruppo di risorse

```
azure insights autoscale setting list montest2 -n setting2
```


### Configurare le impostazioni di scalabilità automatica

```
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0914_2016-->