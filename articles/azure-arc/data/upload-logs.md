---
title: Caricare log in Monitoraggio di Azure
description: Caricare i log per i servizi dati abilitati per Azure Arc in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378142"
---
# <a name="upload-logs-to-azure-monitor"></a>Caricare log in Monitoraggio di Azure

Periodicamente, è possibile esportare i log e quindi caricarli in Azure. L'esportazione e il caricamento dei log consente inoltre di creare e aggiornare le risorse per il controller di dati, l'istanza gestita di SQL e il gruppo di server di scalabilità PostgreSQL in Azure.

> [!NOTE] 
> Durante il periodo di anteprima, non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter caricare i log, è necessario: 

1. [Creare un'area di lavoro di log Analytics](#create-a-log-analytics-workspace)
1. [Assegnare l'ID e la chiave condivisa alle variabili di ambiente](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro di log Analytics

Per creare un'area di lavoro di log Analytics, eseguire questi comandi per creare un'area di lavoro Log Analytics e impostare le informazioni di accesso nelle variabili di ambiente.

> [!NOTE]
> Ignorare questo passaggio se si dispone già di un'area di lavoro.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Output di esempio:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Assegnare l'ID e la chiave condivisa alle variabili di ambiente

Salvare l'analisi dell'area `customerId` di lavoro log come variabile di ambiente da usare in un secondo momento:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Questo comando restituisce le chiavi di accesso necessarie per connettersi all'area di lavoro di log Analytics:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Output di esempio:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Salvare la chiave primaria in una variabile di ambiente da usare in un secondo momento:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Imposta le variabili di ambiente finali e conferma

Impostare l'URL dell'autorità SPN in una variabile di ambiente:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Verificare le variabili di ambiente

Verificare che tutte le variabili di ambiente necessarie siano impostate se si desidera:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Con le variabili di ambiente impostate, è possibile caricare i log nell'area di lavoro log. 

## <a name="upload-logs-to-azure-monitor"></a>Caricare log in Monitoraggio di Azure

 Per caricare i log per le istanze di SQL gestito abilitate per Azure Arc e i gruppi di server con iperscalabilità di PostgreSQL abilitati per AzureArc, eseguire i comandi CLI seguenti

1. Accedere al controller dati di Azure Arc con [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Seguire le istruzioni per impostare lo spazio dei nomi, il nome utente dell'amministratore e la password. 

1. Esporta tutti i log nel file specificato:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Caricare i log in un'area di lavoro di log Analytics di monitoraggio di Azure:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Visualizzare i log in portale di Azure

Dopo aver caricato i log, dovrebbe essere possibile eseguire una query su di essi usando la finestra di esplorazione delle query sui log nel modo seguente:

1. Aprire il portale di Azure, quindi cercare l'area di lavoro in base al nome nella barra di ricerca nella parte superiore e quindi selezionarla.
2. Selezionare logs (registri) nel pannello di sinistra.
3. Selezionare Get Started (oppure selezionare i collegamenti nella pagina Introduzione per altre informazioni sulle Log Analytics se non si ha familiarità con esso).
4. Seguire l'esercitazione per altre informazioni su Log Analytics se è la prima volta che si usa Log Analytics.
5. Espandere Log personalizzati nella parte inferiore dell'elenco delle tabelle; viene visualizzata una tabella denominata "sql_instance_logs_CL".
6. Selezionare l'icona "occhio" accanto al nome della tabella.
7. Selezionare il pulsante "Visualizza nell'editor di query".
8. Nell'editor di query sarà presente una query che mostra i 10 eventi più recenti nel log.
9. Da qui è possibile provare a eseguire query sui log usando l'editor di query, impostare gli avvisi e così via.

## <a name="automating-uploads-optional"></a>Automazione di caricamenti (facoltativo)

Se si vuole caricare le metriche e i log in base a una pianificazione, è possibile creare uno script ed eseguirlo su un timer ogni pochi minuti. Di seguito è riportato un esempio di automazione dei caricamenti usando uno script della shell Linux.

Nell'editor di testo/codice preferito aggiungere lo script seguente al file e salvarlo come file eseguibile di script, ad esempio. sh (Linux/Mac) o. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Rendere eseguibile il file di script

```console
chmod +x myuploadscript.sh
```

Eseguire lo script ogni 20 minuti:

```console
watch -n 1200 ./myuploadscript.sh
```

È anche possibile usare un'utilità di pianificazione dei processi come cron o Windows Utilità di pianificazione o un agente di orchestrazione come Ansible, Puppet o chef.

## <a name="next-steps"></a>Passaggi successivi

[Caricare le metriche e i log in monitoraggio di Azure](upload-metrics.md)

[Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure](upload-usage-data.md)

[Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md)

[Visualizzare la risorsa del controller di dati di Azure Arc in portale di Azure](view-data-controller-in-azure-portal.md)
