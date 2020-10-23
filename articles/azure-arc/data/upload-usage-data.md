---
title: Caricare i dati di utilizzo in monitoraggio di Azure
description: Caricare i dati dei servizi dati abilitati per l'utilizzo di Azure Arc in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378127"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Caricare i dati di utilizzo in monitoraggio di Azure

Periodicamente, è possibile esportare le informazioni sull'utilizzo. Con l'esportazione e il caricamento di queste informazioni vengono create e aggiornate le risorse del gruppo di server per il controller di dati, l'istanza gestita di SQL e l'iperscalabilità PostgreSQL in Azure.

> [!NOTE] 
> Durante il periodo di anteprima, non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Attendere almeno 24 ore dopo aver creato il controller dati di Azure Arc prima di caricare i dati di utilizzo.

## <a name="create-service-principal-and-assign-roles"></a>Creare un'entità servizio e assegnare ruoli

Prima di procedere, assicurarsi di aver creato l'entità servizio richiesta e di averla assegnata a un ruolo appropriato. Per informazioni dettagliate, vedere:
* [Creare un'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Assegnare i ruoli all'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Caricare i dati di utilizzo

Le informazioni sull'utilizzo, ad esempio l'inventario e l'utilizzo delle risorse, possono essere caricate in Azure nei due passaggi seguenti:

1. Accedere al controller dati. Immettere i valori al prompt. 

   ```console
   azdata login
   ```

1. Esportare i dati di utilizzo usando il `azdata arc dc export` comando, come indicato di seguito:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Questo comando crea un `usage.json` file con tutte le risorse di dati abilitate per Azure Arc, ad esempio le istanze gestite di SQL e le istanze di iperscala PostgreSQL e così via, create nel controller dei dati.

2. Caricare i dati di utilizzo usando il ```azdata upload``` comando

   ```console
   azdata arc dc upload --path usage.json
   ```

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

[Caricare log in Monitoraggio di Azure](upload-logs.md)

[Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md)

[Visualizzare la risorsa del controller di dati di Azure Arc in portale di Azure](view-data-controller-in-azure-portal.md)
