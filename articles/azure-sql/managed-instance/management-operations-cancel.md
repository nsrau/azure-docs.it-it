---
title: Annulla operazioni di gestione
titleSuffix: Azure SQL Managed Instance
description: Informazioni su come annullare le operazioni di gestione di Istanza gestita SQL di Azure.
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
ms.openlocfilehash: 4ec999cc35e7d18287679c74c6d45a5aa2ecb9e7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997241"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Annullamento delle operazioni di gestione di Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure offre la possibilità di annullare alcune [operazioni di gestione](management-operations-overview.md), ad esempio quando si distribuiscono una nuova istanza gestita o si aggiornano le proprietà dell'istanza. 

## <a name="overview"></a>Panoramica

 Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione istanza (creazione nuova istanza).
- Aggiornamento dell'istanza (modifica delle proprietà dell'istanza, ad esempio Vcore o archiviazione riservata).
- Eliminazione dell'istanza.

È possibile [monitorare lo stato di avanzamento e lo stato delle operazioni di gestione](management-operations-monitor.md) e annullarne alcune se necessario. 

Nella tabella seguente sono riepilogate le operazioni di gestione, se è possibile annullarle e la relativa durata complessiva tipica:

Category  |Operazione  |Annullabile  |Durata stimata annullamento  |
|---------|---------|---------|---------|
|Distribuzione |Creazione di istanze |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiornamento |Scalabilità verticale/orizzontale di archiviazione dell'istanza (per utilizzo generico) |No |  |
|Aggiornamento |Scalabilità verticale/orizzontale di archiviazione dell'istanza (business critical) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (per utilizzo generico) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiornamento |Scalabilità verticale (VCore) di calcolo dell'istanza (business critical) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiornamento |Modifica del livello di servizio dell'istanza (per utilizzo generico business critical e viceversa) |Sì |90% di operazioni terminano tra 5 minuti. |
|Elimina |Eliminazione di un'istanza |No |  |
|Elimina |Eliminazione di un cluster virtuale (operazione avviata dall'utente) |No |  |

## <a name="cancel-management-operation"></a>Annulla operazione di gestione

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per annullare le operazioni di gestione utilizzando la portale di Azure, attenersi alla seguente procedura:

1. Passare alla [portale di Azure](https://portal.azure.com)
1. Passare al pannello **Panoramica** del istanza gestita SQL. 
1. Selezionare la casella di **notifica** accanto all'operazione in corso per aprire la pagina **operazione in corso** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Selezionare la casella operazione in corso per aprire la pagina operazione in corso.":::

1. Selezionare **Annulla l'operazione** nella parte inferiore della pagina. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Selezionare Annulla per annullare l'operazione.":::

1. Confermare che si desidera annullare l'operazione. 


Se la richiesta di annullamento ha esito positivo, l'operazione di gestione viene annullata e genera un errore. Si riceverà una notifica di esito positivo o negativo dell'annullamento.

![Annullamento del risultato dell'operazione](./media/management-operations-cancel/canceling-operation-result.png)


Se la richiesta di annullamento ha esito negativo o il pulsante Annulla non è attivo, significa che l'operazione di gestione è entrata in uno stato non annullabile e che terminerà a breve.  L'esecuzione dell'operazione di gestione continuerà fino al completamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se Azure PowerShell non è ancora installato, vedere [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per annullare un'operazione di gestione, è necessario specificare il nome dell'operazione di gestione. Quindi, usare prima il comando Get per recuperare l'elenco delle operazioni e quindi annullare l'operazione specifica.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Per la spiegazione dettagliata dei comandi, vedere [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) e [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se l'interfaccia della riga di comando di Azure non è ancora installata, vedere [Install the Azure CLI](/cli/azure/install-azure-cli).

Per annullare l'operazione di gestione, è necessario specificare il nome dell'operazione di gestione. Quindi, usare prima il comando Get per recuperare l'elenco delle operazioni e quindi annullare l'operazione specifica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Per una spiegazione dettagliata dei comandi, vedere [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Richiesta di distribuzione annullata

Con l'API versione 2020-02-02, non appena viene accettata la richiesta di creazione dell'istanza, l'istanza inizia a esistere come risorsa, a prescindere dallo stato di avanzamento del processo di distribuzione (il provisioning dello stato dell'istanza gestita è **provisioning**). Se si annulla la richiesta di distribuzione dell'istanza (creazione nuova istanza), l'istanza gestita passerà dallo stato di **provisioning** a **FailedToCreate**.

Le istanze che non sono state create sono ancora presenti come risorsa e: 

- Non addebitato
- Non contare sui limiti delle risorse (subnet o quota vCore)
- Mantieni il nome dell'istanza riservata: per distribuire un'istanza con lo stesso nome, eliminare l'istanza non riuscita per rilasciare il nome


> [!NOTE]
> Per ridurre al minimo le segnalazioni non significative nell'elenco di risorse o istanze gestite, eliminare le istanze che non sono state distribuite o le istanze con distribuzioni annullate. 


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](instance-create-quickstart.md).
- Per un elenco di funzionalità e confronto, vedere [funzionalità comuni di SQL](../database/features-comparison.md).
- Per ulteriori informazioni sulla configurazione di VNet, vedere la pagina relativa alla [configurazione di SQL istanza gestita VNet](connectivity-architecture-overview.md).
- Per una guida introduttiva per la creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del servizio migrazione del database di Azure per la migrazione, vedere la pagina relativa alla [migrazione istanza gestita SQL con il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
