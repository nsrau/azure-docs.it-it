---
title: Caricare le metriche in monitoraggio di Azure
description: Caricare le metriche dei servizi dati abilitati per Azure Arc in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f31cf22ae8814975dcee92c33026d223275cf121
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378128"
---
# <a name="upload-metrics-to-azure-monitor"></a>Caricare le metriche in monitoraggio di Azure

Periodicamente, è possibile esportare le metriche di monitoraggio e quindi caricarle in Azure. L'esportazione e il caricamento dei dati consente inoltre di creare e aggiornare le risorse del gruppo di server per il controller di dati, l'istanza gestita di SQL e PostgreSQL in Azure.

> [!NOTE] 
> Durante il periodo di anteprima, non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di aver creato l'entità servizio richiesta e di averla assegnata a un ruolo appropriato. Per informazioni dettagliate, vedere:
* [Creare un'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Assegnare i ruoli all'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Caricare le metriche

Con Azure Arc Data Services è possibile caricare le metriche in monitoraggio di Azure in modo da poter aggregare e analizzare le metriche, generare avvisi, inviare notifiche o attivare azioni automatizzate. 

L'invio dei dati a monitoraggio di Azure consente anche di archiviare i dati di metrica fuori sede e su vasta scala, abilitando l'archiviazione a lungo termine dei dati per l'analisi avanzata.

Se sono presenti più siti con Azure Arc Data Services, è possibile usare monitoraggio di Azure come posizione centralizzata per raccogliere tutti i log e le metriche nei siti.

## <a name="set-final-environment-variables-and-confirm"></a>Imposta le variabili di ambiente finali e conferma

Impostare l'URL dell'autorità SPN in una variabile di ambiente:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Verificare che tutte le variabili di ambiente necessarie siano impostate se si desidera:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Caricare le metriche in monitoraggio di Azure

Per caricare le metriche per le istanze gestite di SQL Azure Arc abilitate e i gruppi di server con iperscalabilità di Azure Arc abilitati, eseguire i comandi dell'interfaccia della riga di comando seguenti:

1. Accedere al controller dati con `azdata` .
 
1. Esporta tutte le metriche nel file specificato:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Caricare le metriche in monitoraggio di Azure:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Attendere almeno 30 minuti dopo la creazione di istanze di dati abilitate per Azure Arc per il primo caricamento.
   >
   >Assicurarsi che `upload` la metrica immediatamente dopo `export` come monitoraggio di Azure accetti solo le metriche per gli ultimi 30 minuti. [Altre informazioni](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Se viene visualizzato un errore che indica l'impossibilità di ottenere le metriche durante l'esportazione, verificare che la raccolta dati sia impostata su `true` eseguendo il comando seguente:

```console
azdata arc dc config show
```

Vedere la sezione "sicurezza"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verificare se le `allowNodeMetricsCollection` `allowPodMetricsCollection` proprietà e sono impostate su `true` .

## <a name="view-the-metrics-in-the-portal"></a>Visualizzare le metriche nel portale

Una volta caricate le metriche, è possibile visualizzarle dalla portale di Azure.
> [!NOTE]
> Si noti che potrebbero essere necessari alcuni minuti prima che i dati caricati vengano elaborati prima di poter visualizzare le metriche nel portale.


Per visualizzare le metriche nel portale, usare questo collegamento per aprire il portale: <https://portal.azure.com> quindi cercare l'istanza del database in base al nome nella barra di ricerca:

È possibile visualizzare l'utilizzo della CPU nella pagina panoramica oppure, se si desiderano metriche più dettagliate, è possibile fare clic su metriche nel riquadro di spostamento a sinistra

Scegliere SQL Server come spazio dei nomi della metrica:

Selezionare la metrica che si vuole visualizzare (è anche possibile selezionare più elementi):

Modificare la frequenza con gli ultimi 30 minuti:

> [!NOTE]
> È possibile caricare solo le metriche solo per gli ultimi 30 minuti. Monitoraggio di Azure rifiuta le metriche precedenti a 30 minuti.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Indicazioni generali sull'esportazione e sul caricamento dell'utilizzo, sulle metriche

Le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) in Azure Arc Enabled Data Services vengono registrate per finalità di fatturazione e monitoraggio. Sono disponibili servizi in background che eseguono il monitoraggio di queste operazioni CRUD e calcolano il consumo in modo appropriato. Il calcolo effettivo dell'utilizzo o del consumo avviene in base a una pianificazione e viene eseguito in background. 

Durante la fase di anteprima, questo processo si verifica ogni notte. Il materiale sussidiario generale è caricare l'utilizzo solo una volta al giorno. Quando le informazioni sull'utilizzo vengono esportate e caricate più volte nello stesso periodo di 24 ore, solo l'inventario delle risorse viene aggiornato in portale di Azure ma non nell'utilizzo delle risorse.

Per il caricamento delle metriche, monitoraggio di Azure accetta solo gli ultimi 30 minuti di dati ([altre informazioni](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Le linee guida per il caricamento delle metriche sono il caricamento delle metriche immediatamente dopo la creazione del file di esportazione, in modo da poter visualizzare l'intero set di dati in portale di Azure. Ad esempio, se le metriche sono state esportate alle 2:00 PM ed è stato eseguito il comando Upload alle 2:50 PM. Poiché monitoraggio di Azure accetta solo i dati per gli ultimi 30 minuti, è possibile che non vengano visualizzati dati nel portale. 

## <a name="next-steps"></a>Passaggi successivi

[Caricare log in Monitoraggio di Azure](upload-logs.md)

[Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure](upload-usage-data.md)

[Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md)

[Visualizzare la risorsa del controller di dati di Azure Arc in portale di Azure](view-data-controller-in-azure-portal.md)
