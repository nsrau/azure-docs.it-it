---
title: Esempi di avvio rapido dell'interfaccia della riga di comando 2.0 per Monitoraggio di Azure. | Microsoft Docs
description: Comandi dell'interfaccia della riga di comando 2.0 di esempio per le funzionalità di Monitoraggio di Azure. Monitoraggio di Azure è un servizio di Microsoft Azure che permette di inviare notifiche di avviso, chiamare URL Web in base ai valori dei dati di telemetria configurati e ridimensionare automaticamente servizi cloud, macchine virtuali e app Web.
author: kamathashwin
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: ashwink
ms.openlocfilehash: e429ba460a97daed4a7bdf71895fe24c1619a645
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Esempi di avvio rapido dell'interfaccia della riga di comando 2.0 per Monitoraggio di Azure
Questo articolo illustra i comandi dell'interfaccia della riga di comando di esempio per accedere alle funzionalità di Monitoraggio di Azure. Monitoraggio di Azure consente di ridimensionare automaticamente servizi cloud, macchine virtuali e app Web e di inviare notifiche di avviso o chiamare URL Web in base ai valori dei dati di telemetria configurati.

## <a name="prerequisites"></a>prerequisiti

Se l'interfaccia della riga di comando di Azure non è stata ancora installata, vedere le istruzioni riportate in [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). È anche possibile usare [Azure Cloud Shell](/azure/cloud-shell) per eseguire l'interfaccia della riga di comando in modalità interattiva nel browser. 

## <a name="log-in-to-azure"></a>Accedere ad Azure
Il primo passaggio prevede l'accesso all'account Azure.

```azurecli
az login
```

Dopo aver eseguito questo comando, è necessario eseguire l'accesso tramite le istruzioni visualizzate sullo schermo. Tutti i comandi operano nel contesto della sottoscrizione predefinita.

Per elencare i dettagli della sottoscrizione corrente, usare il comando seguente.

```azurecli
az account show
```

Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente.

```azurecli
az account set -s <Subscription ID or name>
```

Per visualizzare un elenco di tutti i comandi di Monitoraggio di Azure supportati, eseguire il comando seguente.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visualizzare il registro attività di una sottoscrizione

Per visualizzare un elenco degli eventi del registro attività, eseguire questo comando.

```azurecli
az monitor activity-log list
```

Per visualizzare tutte le opzioni disponibili, provare a eseguire questo comando.

```azurecli
az monitor activity-log list -h
```

Ecco un esempio per elencare i log in base a resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Esempio per elencare i log in base al chiamante

```azurecli
az monitor activity-log list --caller myname@company.com
```

Esempio per elencare i log in base al chiamante in un tipo di risorsa, in un intervallo di date

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Usare gli avvisi

È possibile usare le informazioni della sezione per lavorare con gli avvisi.

### <a name="get-alert-rules-in-a-resource-group"></a>Ottenere regole di avviso in un gruppo di risorse

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-rule"></a>Creare una regola di avviso metrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-rule"></a>Eliminare una regola di avviso

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

### <a name="create-a-diagnostic-log-setting"></a>Creare un'impostazione per il log di diagnostica 

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

### <a name="set-auotoscale-settings"></a>Configurare le impostazioni di scalabilità automatica

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
