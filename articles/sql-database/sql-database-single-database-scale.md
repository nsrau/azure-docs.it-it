---
title: Ridimensionare le risorse di database singoli - Database SQL di Azure | Microsoft Docs
description: Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 4288eb7d051c44149a6f1970430dc8c409ca957d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572876"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Ridimensionare le risorse di database singoli nel database SQL di Azure

Questo articolo descrive come ridimensionare le risorse di calcolo e archiviazione disponibili per un singolo database nel livello di calcolo sottoposte a provisioning. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

## <a name="change-compute-size-vcores-or-dtus"></a>Modificare le dimensioni di calcolo (Vcore o Dtu)

Dopo aver selezionato inizialmente il numero di Dtu o Vcore, è possibile ridimensionare un singolo database verso l'alto o verso il basso in modo dinamico base effettiva esperienza tramite il [portale di Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), il [CLI di Azure](/cli/azure/sql/db#az-sql-db-update), oppure il [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

Il video seguente mostra come modificare in modo dinamico il livello di servizio e le dimensioni di calcolo per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impatto della modifica delle dimensioni di calcolo ridimensionamento o un livello di servizio

Modifica del servizio livello o di calcolo delle dimensioni di un database singolo principalmente prevede che il servizio eseguendo i passaggi seguenti:

1. Creare una nuova istanza di calcolo per il database  

    Viene creata una nuova istanza di calcolo per il database con le dimensioni di calcolo e il livello di servizio richiesto. Per alcune combinazioni di livello di servizio e delle modifiche alle dimensioni di calcolo, una replica del database deve essere creata in una nuova istanza di calcolo che comporta la copia dei dati e può influire pesantemente sulla latenza complessiva. Indipendentemente da ciò, il database rimane online durante questo passaggio e connessioni continuano a essere indirizzate al database nell'istanza di calcolo originale.

2. Passare il routing delle connessioni alla nuova istanza di calcolo

    Le connessioni esistenti ai database nell'istanza originale di calcolo vengono eliminate. Tutte le nuove connessioni vengono stabilite con il database nella nuova istanza di calcolo. Per alcune combinazioni di livello di servizio e delle modifiche alle dimensioni di calcolo, file di database sono scollegati e ricollegati durante l'operazione switch.  Indipendentemente da ciò, l'opzione può causare una breve interruzione del servizio quando il database non è disponibile a livello generale per meno di 30 secondi e spesso solo alcuni secondi. Se sono presenti con esecuzione prolungata le transazioni in esecuzione quando le connessioni vengono rimosse, la durata di questo passaggio può richiedere più tempo per eseguire il ripristino transazioni interrotte. [Accelerazione di recupero del Database](sql-database-accelerated-database-recovery.md) può ridurre l'impatto di interruzione in corso transazioni a esecuzione prolungata.

> [!IMPORTANT]
> Durante un qualsiasi passaggio nel flusso di lavoro viene perso alcun dato.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latenza di modifica delle dimensioni di calcolo ridimensionamento o un livello di servizio

La latenza per modificare il livello di servizio o ridimensionare le dimensioni di calcolo di un database singolo o pool elastico con i parametri come indicato di seguito:

|Livello di servizio|Database singolo Basic,</br>Standard (S0-S1)|Pool elastici Basic,</br>Standard (S2-S12), </br>Con Iperscalabilità, </br>Database singolo a scopo generale o il pool elastico|Premium o Business Critical database singolo o pool elastico|
|:---|:---|:---|:---|
|**Database singolo Basic</br> Standard (S0-S1)**|&bull; &nbsp;Latenza tempo costante indipendente dello spazio utilizzato</br>&bull; &nbsp;In genere meno di 5 minuti|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|
|**Pool elastici Basic, </br>Standard (S2-S12), </br>con Iperscalabilità e </br>generico singolo database o pool elastico**|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza tempo costante indipendente dello spazio utilizzato</br>&bull; &nbsp;In genere meno di 5 minuti|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|
|**Premium o Business Critical database singolo o pool elastico**|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Annullare le modifiche ai livelli di servizio o di ridimensionamento delle operazioni di calcolo

Un livello di servizio di calcolo ridimensionamento dell'operazione può essere annullata o modificare.

#### <a name="azure-portal"></a>Portale di Azure

Nel Pannello di panoramica del database, passare a **notifiche** e fare clic sul riquadro che indica l'assenza di un'operazione in corso:

![Operazione in corso](media/sql-database-single-database-scale/ongoing-operations.png)

Successivamente, fare clic sul pulsante con etichettato **annullare questa operazione**.

![Annullare l'operazione in corso](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Da un prompt dei comandi di PowerShell, impostare il `$ResourceGroupName`, `$ServerName`, e `$DatabaseName`, quindi eseguire il comando seguente:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerazioni aggiuntive durante la modifica del servizio livello o ridimensionamento delle dimensioni di calcolo

- Se si esegue l'aggiornamento a un livello di servizio o dimensioni di calcolo superiori, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
- Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e le dimensioni di calcolo di destinazione.
- Quando si effettua il downgrade dal livello **Premium** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nelle dimensioni di calcolo di destinazione e (2) le dimensioni massime superano lo spazio di archiviazione incluso delle dimensioni di calcolo di destinazione. Se ad esempio un database P1 con una dimensione massima di 500 GB viene ridotto a S3, viene applicato un costo per le risorse di archiviazione extra, poiché S3 supporta una dimensione massima di 500 GB e lo spazio di archiviazione incluso è solo di 250 GB. Lo spazio di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di servizio e alle dimensioni di calcolo desiderati prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
- Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di servizio e alle dimensioni di calcolo desiderati prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere l'articolo relativo ai [backup del database SQL di Azure](sql-database-automated-backups.md).
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

### <a name="billing-during-compute-rescaling"></a>Accreditamento durante il ridimensionamento di calcolo

Viene fatturata ogni ora per cui un database esiste usando il livello di servizio più elevato e le dimensioni di calcolo applicati in quell'ora, indipendentemente dall'uso o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-storage-size"></a>modifica delle dimensioni di archiviazione

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo delle dimensioni tramite incrementi di 1 GB. Lo spazio di archiviazione dei dati minimo configurabile è 5 GB
- Il provisioning dell'archiviazione per un database singolo può essere effettato aumentandone o diminuendone le dimensioni massime tramite il [portale di Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il database SQL alloca automaticamente il 30% di archiviazione aggiuntiva per i file di log e 32 GB per ogni vCore per TempDB, ma senza superare 384 GB. TempDB è disponibile in un'unità SSD collegata in tutti i livelli di servizio.
- Il prezzo dell'archiviazione per un database singolo corrisponde alla somma delle dimensioni di archiviazione dei dati e degli spazi di archiviazione dei log moltiplicata per il prezzo unitario dell'archiviazione del livello di servizio. Il costo di TempDB è compreso nel prezzo dei vCore. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per gli spazi di archiviazione inclusi e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e dimensioni di calcolo](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Il provisioning delle risorse di archiviazione extra per un database singolo può essere effettuato aumentandone le dimensioni massime tramite il portale di Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>I vincoli di P11 e P15 quando massima dimensione è maggiore di 1 TB

Nel livello Premium è attualmente disponibile uno spazio di archiviazione superiore a 1 TB in tutte le aree tranne Cina orientale, Cina settentrionale, Germania centrale, Germania nord-orientale, Stati Uniti centro-occidentali, aree US DoD e US Government (area centrale). In queste aree la quantità massima di spazio di archiviazione nel livello Premium è limitata a 1 TB. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- Se le dimensioni massime per un database P11 o P15 è stata impostata sempre su un valore maggiore di 1 TB, quindi solo ripristinato o copiato in un database P11 o P15.  Successivamente, il database può ridimensionato a una dimensione di calcolo diverse purché la quantità di spazio allocato al momento dell'operazione di ridimensionamento non superi i limiti di dimensioni massime le nuove dimensioni di calcolo.
- Per gli scenari di replica geografica attiva:
  - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. Dimensioni di calcolo inferiori non vengono accettate come database secondari in quanto non supportano l'opzione superiore a 1 TB.
  - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta l'opzione da oltre 1 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 con oltre 1 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore del database SQL - database singoli](sql-database-vcore-resource-limits-single-databases.md), [Limiti delle risorse basate su DTU del database SQL - pool elastici](sql-database-dtu-resource-limits-single-databases.md).
