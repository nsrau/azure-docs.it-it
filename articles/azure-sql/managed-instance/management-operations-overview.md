---
title: Operazioni di gestione
titleSuffix: Azure SQL Managed Instance
description: Informazioni sulle procedure consigliate e sulla durata delle operazioni di gestione Istanza gestita di Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531488"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Panoramica delle operazioni di gestione di Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Che cosa sono le operazioni di gestione?
Istanza gestita SQL di Azure fornisce operazioni di gestione che è possibile usare per distribuire automaticamente nuove istanze gestite, aggiornare le proprietà dell'istanza ed eliminare istanze quando non sono più necessarie.

Per supportare le [distribuzioni nelle reti virtuali di Azure](../../virtual-network/virtual-network-for-azure-services.md) e garantire l'isolamento e la sicurezza per i clienti, SQL istanza gestita si basa su [cluster virtuali](connectivity-architecture-overview.md#high-level-connectivity-architecture). Cluster virtuale rappresenta un set dedicato di macchine virtuali isolate distribuite all'interno della subnet della rete virtuale del cliente. In pratica, tutte le distribuzioni di istanze gestite in una subnet vuota generano una nuova compilazione del cluster virtuale.

Anche le operazioni successive sulle istanze gestite distribuite potrebbero avere effetti sul cluster virtuale sottostante. Queste operazioni influiscono sulla durata delle operazioni di gestione, in quanto la distribuzione di macchine virtuali aggiuntive comporta un sovraccarico da tenere in considerazione quando si pianificano nuove distribuzioni o aggiornamenti per le istanze gestite esistenti.

Tutte le operazioni di gestione possono essere suddivise per categoria, come indicato di seguito:

- Distribuzione istanza (creazione nuova istanza).
- Aggiornamento dell'istanza (modifica delle proprietà dell'istanza, ad esempio Vcore o archiviazione riservata).
- Eliminazione dell'istanza.

## <a name="management-operations-duration"></a>Durata delle operazioni di gestione
In genere, le operazioni sui cluster virtuali hanno più tempo. La durata delle operazioni nei cluster virtuali varia: di seguito sono riportati i valori che in genere è possibile prevedere, in base ai dati di telemetria del servizio esistenti:

- **Creazione di un cluster virtuale**: la creazione è un passaggio sincrono nelle operazioni di gestione delle istanze. **90% delle operazioni terminano tra 4 ore**.
- **Ridimensionamento di cluster virtuali (espansione o compattazione)**: l'espansione è un passaggio sincrono, mentre la compattazione viene eseguita in modo asincrono (senza alcun effetto sulla durata delle operazioni di gestione dell'istanza). **90% delle espansioni del cluster completate in meno di 2,5 ore**.
- **Eliminazione di un cluster virtuale**: l'eliminazione è un passaggio asincrono, ma può anche essere [avviata manualmente](virtual-cluster-delete.md) in un cluster virtuale vuoto, nel qual caso viene eseguita in modo sincrono. **90% delle eliminazioni di cluster virtuali completate tra 1,5 ore**.

Inoltre, la gestione delle istanze può includere anche una delle operazioni sui database ospitati, che determinano durate più lunghe:

- **Collegamento di file di database da archiviazione di Azure**: un passaggio sincrono, ad esempio il calcolo (VCore), o la scalabilità dell'archiviazione verso l'alto o verso il basso nel per utilizzo generico livello di servizio. **90% di queste operazioni vengono completate in 5 minuti**.
- **Always on il seeding del gruppo di disponibilità**: un passaggio sincrono, ad esempio calcolo (VCore), o la scalabilità di archiviazione nel livello di servizio business critical, nonché la modifica del livello di servizio da per utilizzo generico a business critical (o viceversa). La durata dell'operazione è proporzionale alle dimensioni totali del database e all'attività del database corrente (numero di transazioni attive). L'attività del database durante l'aggiornamento di un'istanza può introdurre una varianza significativa per la durata totale. il **90% di queste operazioni viene eseguito a 220 GB/ora o superiore**.

Nella tabella seguente sono riepilogate le operazioni e le durate generali tipiche:

|Category  |Operazione  |Segmento con esecuzione prolungata  |Durata stimata  |
|---------|---------|---------|---------|
|**Distribuzione** |Prima istanza in una subnet vuota|Creazione di un cluster virtuale|90% delle operazioni terminano tra 4 ore.|
|Distribuzione |Prima istanza di un'altra generazione di hardware in una subnet non vuota (ad esempio, la prima istanza di generazione 5 in una subnet con istanze di generazione 4)|Creazione di un cluster virtuale *|90% delle operazioni terminano tra 4 ore.|
|Distribuzione |Creazione della prima istanza di 4 Vcore in una subnet vuota o non vuota|Creazione di un cluster virtuale * *|90% delle operazioni terminano tra 4 ore.|
|Distribuzione |Creazione dell'istanza successiva all'interno della subnet non vuota (2a, terza e così via)|Ridimensionamento di cluster virtuali|90% di operazioni terminano tra 2,5 ore.|
|**Aggiornamento** |Modifica proprietà istanza (password amministratore, accesso Azure AD, flag Vantaggio Azure Hybrid)|N/D|Fino a 1 minuto.|
|Aggiorna |Scalabilità verticale/orizzontale per l'archiviazione delle istanze (per utilizzo generico livello di servizio)|Associazione di file di database|90% di operazioni terminano tra 5 minuti.|
|Aggiorna |Scalabilità verticale/orizzontale per l'archiviazione delle istanze (business critical livello di servizio)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni terminano tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora).|
|Aggiorna |Scalabilità verticale (VCore) di calcolo dell'istanza (per utilizzo generico)|-Ridimensionamento cluster virtuale<br>-Connessione dei file di database|90% di operazioni terminano tra 2,5 ore.|
|Aggiorna |Scalabilità verticale (VCore) di calcolo dell'istanza (business critical)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni terminano tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora).|
|Aggiorna |Modifica del livello di servizio dell'istanza (per utilizzo generico business critical e viceversa)|-Ridimensionamento cluster virtuale<br>-Always On seeding del gruppo di disponibilità|90% di operazioni terminano tra 2,5 ore + tempo per il seeding di tutti i database (220 GB/ora).|
|**Eliminazione**|Eliminazione di un'istanza|Backup della parte finale del log per tutti i database|90% le operazioni vengono completate fino a un minuto.<br>Nota: se viene eliminata l'ultima istanza della subnet, questa operazione pianifica l'eliminazione del cluster virtuale dopo 12 ore. * *|
|Eliminazione|Eliminazione di un cluster virtuale (operazione avviata dall'utente)|Eliminazione cluster virtuale|90% di operazioni vengono completate in un massimo di 1,5 ore.|

\*Il cluster virtuale è compilato per ogni generazione di hardware.

\*\*12 ore è la configurazione corrente, ma potrebbe cambiare in futuro, quindi non assumere una dipendenza difficile. Se è necessario eliminare un cluster virtuale in precedenza (ad esempio per rilasciare la subnet), vedere [eliminare una subnet dopo l'eliminazione di un'istanza gestita](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Disponibilità dell'istanza durante le operazioni di gestione

SQL Istanza gestita **è disponibile durante le operazioni di aggiornamento**, ad eccezione di un breve tempo di inattività causato dal failover che si verifica al termine dell'aggiornamento. In genere, dura fino a 10 secondi anche in caso di transazioni a esecuzione prolungata interrotte, grazie al [recupero accelerato del database](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Non è consigliabile ridimensionare il calcolo o l'archiviazione di Istanza gestita SQL di Azure o per modificare il livello di servizio contemporaneamente con le transazioni a esecuzione prolungata (importazione di dati, processi di elaborazione dei dati, ricompilazione dell'indice e così via). Il failover del database che verrà eseguito al termine dell'operazione cancellerà tutte le transazioni in corso.

SQL Istanza gestita non è disponibile per le applicazioni client durante le operazioni di distribuzione ed eliminazione.

## <a name="management-operations-cross-impact"></a>Effetti incrociati sulle operazioni di gestione

Le operazioni di gestione in un'istanza gestita possono influire su altre operazioni di gestione delle istanze posizionate all'interno dello stesso cluster virtuale:

- **Le operazioni di ripristino con esecuzione prolungata** in un cluster virtuale manterranno le altre operazioni di creazione o ridimensionamento delle istanze nella stessa subnet.<br/>**Esempio:** Se è presente un'operazione di ripristino con esecuzione prolungata ed è presente una richiesta di creazione o ridimensionamento nella stessa subnet, questa richiesta richiederà più tempo perché attenderà il completamento dell'operazione di ripristino prima di continuare.
    
- **Una successiva operazione di creazione o ridimensionamento** di un'istanza viene messa in attesa da una creazione di istanza avviata in precedenza o da una scala dell'istanza che ha avviato il ridimensionamento del cluster virtuale.<br/>**Esempio:** Se sono presenti più richieste di creazione e/o scalabilità nella stessa subnet nello stesso cluster virtuale e una di esse avvia il ridimensionamento di un cluster virtuale, tutte le richieste inviate 5 + minuti dopo quella che ha richiesto il ridimensionamento del cluster virtuale avranno più tempo del previsto, dal momento che queste richieste dovranno attendere il completamento del ridimensionamento prima della ripresa.

- **Le operazioni di creazione/ridimensionamento inviate in un intervallo di 5 minuti** verranno eseguite in batch ed eseguite in parallelo.<br/>**Esempio:** Verrà eseguito un solo ridimensionamento del cluster virtuale per tutte le operazioni inviate in un intervallo di 5 minuti, misurato dal momento in cui si esegue la prima richiesta di operazione. Se un'altra richiesta viene inviata più di 5 minuti dopo l'invio del primo, attenderà il completamento del ridimensionamento del cluster virtuale prima dell'avvio dell'esecuzione.

> [!IMPORTANT]
> Le operazioni di gestione messe in attesa a causa di un'altra operazione in corso verranno riavviate automaticamente una volta soddisfatte le condizioni per continuare. Non è necessaria alcuna azione da eseguire per riprendere temporaneamente le operazioni di gestione sospese.

## <a name="canceling-management-operations"></a>Annullamento di operazioni di gestione

Nella tabella seguente è riepilogata la possibilità di annullare operazioni di gestione specifiche e le durate generali tipiche:

Category  |Operazione  |Annullabile  |Durata stimata annullamento  |
|---------|---------|---------|---------|
|Distribuzione |Creazione di istanze |No |  |
|Aggiorna |Scalabilità verticale/orizzontale di archiviazione dell'istanza (per utilizzo generico) |No |  |
|Aggiorna |Scalabilità verticale/orizzontale di archiviazione dell'istanza (business critical) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiorna |Scalabilità verticale (VCore) di calcolo dell'istanza (per utilizzo generico) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiorna |Scalabilità verticale (VCore) di calcolo dell'istanza (business critical) |Sì |90% di operazioni terminano tra 5 minuti. |
|Aggiorna |Modifica del livello di servizio dell'istanza (per utilizzo generico business critical e viceversa) |Sì |90% di operazioni terminano tra 5 minuti. |
|Delete |Eliminazione di un'istanza |No |  |
|Delete |Eliminazione di un cluster virtuale (operazione avviata dall'utente) |No |  |

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per annullare l'operazione di gestione, passare al pannello panoramica e fare clic sulla casella di notifica dell'operazione in corso. Dal lato destro verrà visualizzata una schermata con l'operazione in corso e sarà presente un pulsante per annullare l'operazione. Dopo il primo clic, verrà chiesto di fare nuovamente clic e confermare che si desidera annullare l'operazione.

[![Annulla operazione](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Dopo che una richiesta di annullamento è stata inviata ed elaborata, si riceverà una notifica se l'invio dell'annullamento ha avuto esito positivo o negativo.

Se la richiesta di annullamento inviata ha esito positivo, l'operazione di gestione verrà annullata in un paio di minuti, causando un errore.

![Annullamento del risultato dell'operazione](./media/management-operations-overview/canceling-operation-result.png)

Se la richiesta di annullamento ha esito negativo o il pulsante Annulla non è attivo, significa che l'operazione di gestione è entrata in uno stato non annullabile e che verrà completata in pochi minuti. L'esecuzione dell'operazione di gestione continuerà fino al completamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se Azure PowerShell non è ancora installato, vedere [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per annullare l'operazione di gestione, è necessario specificare il nome dell'operazione di gestione. Quindi, usare prima di tutto il comando Get per recuperare l'elenco delle operazioni e quindi annullare un'operazione specifica.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

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

Se l'interfaccia della riga di comando di Azure non è ancora installata, vedere [Install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Per annullare l'operazione di gestione, è necessario specificare il nome dell'operazione di gestione. Quindi, usare prima di tutto il comando Get per recuperare l'elenco delle operazioni e quindi annullare un'operazione specifica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Per una spiegazione dettagliata dei comandi, vedere [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](instance-create-quickstart.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](../database/features-comparison.md).
- Per ulteriori informazioni sulla configurazione di VNet, vedere la pagina relativa alla [configurazione di SQL istanza gestita VNet](connectivity-architecture-overview.md).
- Per una guida introduttiva per la creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del servizio migrazione del database di Azure per la migrazione, vedere la pagina relativa alla [migrazione istanza gestita SQL con il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
