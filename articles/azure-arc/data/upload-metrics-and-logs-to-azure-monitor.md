---
title: Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure
description: Caricare l'inventario delle risorse, i dati di utilizzo, le metriche e i log in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 66b10efb6ca93bc6b4dd67d700daaf1f9049de68
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183431"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure

Periodicamente, è possibile esportare le informazioni di utilizzo per finalità di fatturazione, monitoraggio delle metriche e log e quindi caricarle in Azure. L'esportazione e il caricamento di uno di questi tre tipi di dati creeranno e aggiorneranno anche le risorse del gruppo di server per il controller di dati, l'istanza gestita di SQL e l'iperscalabilità PostgreSQL in Azure.

> [!NOTE] 
> Durante il periodo di anteprima, non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Prima di poter caricare i dati di utilizzo, le metriche o i log, è necessario:

* Installare gli strumenti 
* [Registrare il `Microsoft.AzureData` provider di risorse](#register-the-resource-provider) 
* [Creare l'entità servizio](#create-service-principal)

## <a name="install-tools"></a>Installare gli strumenti

Gli strumenti necessari includono: 
* INTERFACCIA della riga di comando di Azure (AZ) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Vedere [installare gli strumenti](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Prima di caricare le metriche o i dati utente in Azure, è necessario assicurarsi che la sottoscrizione di Azure disponga del `Microsoft.AzureData` provider di risorse registrato.

Per verificare il provider di risorse, eseguire il comando seguente:

```azurecli
az provider show -n Microsoft.AzureData -o table
```

Se il provider di risorse non è attualmente registrato nella sottoscrizione, è possibile registrarlo. Per registrarlo, eseguire il comando seguente.  Il completamento del comando potrebbe richiedere un minuto o due.

```azurecli
az provider register -n Microsoft.AzureData --wait
```

## <a name="create-service-principal"></a>Creare un'entità servizio

L'entità servizio viene usata per caricare i dati di utilizzo e di metrica.

Seguire questi comandi per creare le metriche di caricamento dell'entità servizio:

> [!NOTE]
> Per la creazione di un'entità servizio sono necessarie [determinate autorizzazioni in Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Per creare un'entità servizio, aggiornare l'esempio seguente. Sostituire `<ServicePrincipalName>` con il nome dell'entità servizio ed eseguire il comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Se l'entità servizio è stata creata in precedenza ed è sufficiente ottenere le credenziali correnti, eseguire il comando seguente per reimpostare le credenziali.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Ad esempio, per creare un'entità servizio denominata `azure-arc-metrics` , eseguire il comando seguente

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Output di esempio:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salvare i `appId` `password` valori, e `tenant` in una variabile di ambiente per usarli in seguito. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Dopo aver creato l'entità servizio, assegnare l'entità servizio al ruolo appropriato. 

## <a name="assign-roles-to-the-service-principal"></a>Assegnare i ruoli all'entità servizio

Eseguire questo comando per assegnare l'entità servizio al `Monitoring Metrics Publisher` ruolo nella sottoscrizione in cui si trovano le risorse dell'istanza del database:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Quando si esegue da un ambiente Windows, è necessario utilizzare le virgolette doppie per i nomi dei ruoli.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Output di esempio:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Con l'entità servizio assegnata al ruolo appropriato, è possibile continuare a caricare le metriche o i dati utente. 

## <a name="upload-logs-metrics-or-user-data"></a>Caricare log, metriche o dati utente

I passaggi specifici per il caricamento di log, metriche o dati utente variano in base al tipo di informazioni che si sta caricando. 

[Caricare log in Monitoraggio di Azure](upload-logs.md)

[Caricare le metriche in monitoraggio di Azure](upload-metrics.md)

[Caricare i dati di utilizzo in monitoraggio di Azure](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Indicazioni generali sull'esportazione e sul caricamento dell'utilizzo, sulle metriche

Le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) in Azure Arc Enabled Data Services vengono registrate per finalità di fatturazione e monitoraggio. Sono disponibili servizi in background che eseguono il monitoraggio di queste operazioni CRUD e calcolano il consumo in modo appropriato. Il calcolo effettivo dell'utilizzo o del consumo avviene in base a una pianificazione e viene eseguito in background. 

Durante la fase di anteprima, questo processo si verifica ogni notte. Il materiale sussidiario generale è caricare l'utilizzo solo una volta al giorno. Quando le informazioni sull'utilizzo vengono esportate e caricate più volte nello stesso periodo di 24 ore, solo l'inventario delle risorse viene aggiornato in portale di Azure ma non nell'utilizzo delle risorse.

Per il caricamento delle metriche, monitoraggio di Azure accetta solo gli ultimi 30 minuti di dati ([altre informazioni](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Le linee guida per il caricamento delle metriche sono il caricamento delle metriche immediatamente dopo la creazione del file di esportazione, in modo da poter visualizzare l'intero set di dati in portale di Azure. Ad esempio, se le metriche sono state esportate alle 2:00 PM ed è stato eseguito il comando Upload alle 2:50 PM. Poiché monitoraggio di Azure accetta solo i dati per gli ultimi 30 minuti, è possibile che non vengano visualizzati dati nel portale. 

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulle entità servizio](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md)

[Visualizzare la risorsa del controller di dati di Azure Arc in portale di Azure](view-data-controller-in-azure-portal.md)