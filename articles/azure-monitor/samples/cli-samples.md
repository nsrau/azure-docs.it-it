---
title: Esempi dell'interfaccia della riga di comando di Monitoraggio di Azure
description: Comandi dell'interfaccia della riga di comando di esempio per le funzionalità di Monitoraggio di Azure. Monitoraggio di Azure è un servizio di Microsoft Azure che consente di inviare notifiche di avviso, chiamare URL Web in base ai valori dei dati di telemetria configurati e ridimensionare automaticamente servizi cloud, macchine virtuali e app Web.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: abfac23ad37d750a6d7ef84e5dec2559904ebb1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87496305"
---
# <a name="azure-monitor-cli-samples"></a>Esempi dell'interfaccia della riga di comando di Monitoraggio di Azure
Questo articolo illustra i comandi dell'interfaccia della riga di comando di esempio per accedere alle funzionalità di Monitoraggio di Azure. Monitoraggio di Azure consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web e di inviare notifiche di avviso o chiamare URL Web in base ai valori dei dati di telemetria configurati.

## <a name="prerequisites"></a>Prerequisiti

Se l'interfaccia della riga di comando di Azure non è stata ancora installata, vedere le istruzioni riportate in [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). È anche possibile usare [Azure Cloud Shell](/azure/cloud-shell) per eseguire l'interfaccia della riga di comando in modalità interattiva nel browser. Vedere un riferimento completo di tutti i comandi disponibili nel [riferimento dell'interfaccia della riga di comando di Monitoraggio di Azure](/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Accedere ad Azure
Il primo passaggio consiste nell'eseguire l'accesso all'account Azure.

```azurecli
az login
```

Dopo aver eseguito questo comando, è necessario eseguire l'accesso tramite le istruzioni visualizzate sullo schermo. Tutti i comandi operano nel contesto della sottoscrizione predefinita.

Visualizzare i dettagli della sottoscrizione corrente.

```azurecli
az account show
```

Cambiare il contesto di lavoro impostandolo su una sottoscrizione diversa.

```azurecli
az account set -s <Subscription ID or name>
```

Visualizzare un elenco di tutti i comandi di Monitoraggio di Azure supportati.

```azurecli
az monitor -h
```

## <a name="view-activity-log"></a>Visualizzare il log attività

Visualizzare un elenco di eventi del log attività.

```azurecli
az monitor activity-log list
```

Visualizzare tutte le opzioni disponibili.

```azurecli
az monitor activity-log list -h
```

Elencare i log in base a un gruppo di risorse.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Elencare i log in base al chiamante.

```azurecli
az monitor activity-log list --caller myname@company.com
```

Elencare i log in base al chiamante o a un tipo di risorsa, entro un intervallo di date.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Usare gli avvisi 
> [!NOTE]
> In questa fase sono supportati solo gli avvisi (classici) nell'interfaccia della riga di comando. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Ottenere regole di avviso (classico) in un gruppo di risorse

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Creare una regola di avviso (classico) metrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Eliminare una regola di avviso (classico)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profili dei log

Usare le informazioni di questa sezione per lavorare con i profili dei log.

### <a name="get-a-log-profile"></a>Acquisizione di un profilo di log

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Aggiungere un profilo di log con conservazione

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Aggiungere un profilo di log con conservazione e hub eventi

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostica

Usare le informazioni di questa sezione per lavorare con le impostazioni di diagnostica.

### <a name="get-a-diagnostic-setting"></a>Ottenere un'impostazione di diagnostica

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Eliminare un'impostazione di diagnostica

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Autoscale

Usare le informazioni di questa sezione per lavorare con le impostazioni di scalabilità automatica. È necessario modificare questi esempi.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Ottenere le impostazioni di scalabilità automatica per un gruppo di risorse

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Ottenere le impostazioni di scalabilità automatica per nome in un gruppo di risorse

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Configurare impostazioni di scalabilità automatica

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
