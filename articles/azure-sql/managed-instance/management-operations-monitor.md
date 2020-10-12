---
title: Monitorare le operazioni di gestione
titleSuffix: Azure SQL Managed Instance
description: Informazioni sui diversi modi per monitorare le operazioni di gestione di Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996842"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Monitoraggio delle operazioni di gestione Istanza gestita di Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure fornisce il monitoraggio delle [operazioni di gestione](management-operations-overview.md) utilizzate per distribuire nuove istanze gestite, aggiornare le proprietà dell'istanza o eliminare istanze quando non sono più necessarie. 

## <a name="overview"></a>Panoramica

Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione istanza (creazione nuova istanza).
- Aggiornamento dell'istanza (modifica delle proprietà dell'istanza, ad esempio Vcore o archiviazione riservata).
- Eliminazione dell'istanza.

La maggior parte delle operazioni di gestione è a [esecuzione prolungata](management-operations-overview.md#duration). È pertanto necessario monitorare lo stato o seguire lo stato di avanzamento dei passaggi dell'operazione. 

Sono disponibili diversi modi per monitorare le operazioni di gestione di istanze gestite:

- [Distribuzioni di gruppi di risorse](../../azure-resource-manager/templates/deployment-history.md)
- [Log attività](../../azure-monitor/platform/activity-log.md)
- [API delle operazioni dell'istanza gestita](#managed-instance-operations-api)


Nella tabella seguente vengono confrontate le opzioni di monitoraggio delle operazioni di gestione: 

| Opzione | Conservazione | Supporta l'annullamento | Crea | Aggiornamento | Delete | Annulla | Passaggi |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Distribuzioni di gruppi di risorse | Infinito<sup>1</sup> | No<sup>2</sup> | Visible | Visible | Non visibile | Visible | Non visibile |
| Log attività | 90 giorni | No | Visible | Visible | Visible | Visible |  Non visibile |
| API delle operazioni dell'istanza gestita | 24 ore | [Sì](management-operations-cancel.md) | Visible | Visible | Visible | Visible | Visible |
|  |  |  |  |  |  |  | |

<sup>1</sup> la cronologia di distribuzione per un gruppo di risorse è limitata a 800 distribuzioni.

<sup>2</sup> le distribuzioni di gruppi di risorse supportano l'operazione di annullamento. Tuttavia, a causa della logica di annullamento, viene annullata solo un'operazione pianificata per la distribuzione dopo l'esecuzione dell'azione di annullamento. La distribuzione in corso non viene annullata quando viene annullata la distribuzione del gruppo di risorse. Poiché la distribuzione dell'istanza gestita è costituita da un passaggio a esecuzione prolungata (dal punto di vista di Azure Resource Manager), l'annullamento della distribuzione del gruppo di risorse non annulla la distribuzione dell'istanza gestita e l'operazione viene completata. 

## <a name="managed-instance-operations-api"></a>API delle operazioni dell'istanza gestita

Le API delle operazioni di gestione sono appositamente progettate per monitorare le operazioni. Il monitoraggio delle operazioni dell'istanza gestita può fornire informazioni dettagliate sui parametri dell'operazione e sui passaggi dell'operazione, nonché [annullare operazioni specifiche](management-operations-cancel.md). Oltre ai dettagli dell'operazione e al comando Cancel, questa API può essere usata negli script di automazione con distribuzioni di più risorse, in base al passaggio di avanzamento, è possibile avviare alcune distribuzioni di risorse dipendenti.

Queste sono le API: 

| Comando | Descrizione |
| --- | --- |
|[Operazioni di Istanza gestita-Get](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|Ottiene un'operazione di gestione su un'istanza gestita.|
|[Operazioni di Istanza gestita-Annulla](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|Annulla l'operazione asincrona nell'istanza gestita.|
|[Operazioni Istanza gestita-list by Istanza gestita](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Ottiene un elenco di operazioni eseguite nell'istanza gestita.|

> [!NOTE]
> Usare l'API versione 2020-02-02 per visualizzare l'operazione di creazione dell'istanza gestita nell'elenco delle operazioni. Questa è la versione predefinita usata nel portale di Azure e nei pacchetti più recenti di PowerShell e dell'interfaccia della riga di comando di Azure.

## <a name="monitor-operations"></a>Monitorare le operazioni

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nella portale di Azure utilizzare la pagina **Panoramica** istanza gestita per monitorare le operazioni dell'istanza gestita. 

Ad esempio, l' **operazione** di creazione è visibile all'inizio del processo di creazione nella pagina **Panoramica** : 

![Stato di creazione istanza gestita](./media/management-operations-monitor/monitoring-create-operation.png)

Selezionare **operazione in corso** per aprire la **pagina operazione in corso** e visualizzare le operazioni di **creazione** o **aggiornamento** . È anche possibile [annullare](management-operations-cancel.md) le operazioni da questa pagina.  

![Dettagli operazione istanza gestita](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Le operazioni di creazione inviate tramite portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o altri strumenti con l'API REST versione 2020-02-02 [possono essere annullate](management-operations-cancel.md). Le versioni dell'API REST precedenti alla 2020-02-02 usate per inviare un'operazione di creazione avvierà la distribuzione dell'istanza, ma la distribuzione non verrà elencata nell'API delle operazioni e non potrà essere annullata.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Il cmdlet Get-AzSqlInstanceOperation ottiene informazioni sulle operazioni in un'istanza gestita. È possibile visualizzare tutte le operazioni in un'istanza gestita o visualizzare un'operazione specifica fornendo il nome dell'operazione.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Per la spiegazione dettagliata dei comandi, vedere [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L'elenco AZ SQL mi op ottiene un elenco di operazioni eseguite nell'istanza gestita. Se l'interfaccia della riga di comando di Azure non è ancora installata, vedere [Install the Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Per una spiegazione dettagliata dei comandi, vedere [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida di avvio rapido](instance-create-quickstart.md).
- Per un elenco di funzionalità e confronto, vedere [funzionalità comuni di SQL](../database/features-comparison.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere [Configurazione di una rete virtuale per Istanza gestita di SQL](connectivity-architecture-overview.md).
- Per una guida di avvio rapido sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del Servizio Migrazione del database di Azure, vedere [Migrazione a Istanza gestita di SQL con il Servizio Migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
