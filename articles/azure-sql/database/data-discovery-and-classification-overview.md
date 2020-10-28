---
title: Individuazione dati e classificazione
description: Individuazione dei dati & classificazione per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/21/2020
tags: azure-synapse
ms.openlocfilehash: ab974b0f68e831e672329f8af5ae1cb6a5fdbd4c
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672075"
---
# <a name="data-discovery--classification"></a>Individuazione dati e classificazione
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

L'individuazione dei dati & classificazione è incorporata nel database SQL di Azure, in Azure SQL Istanza gestita e in Azure sinapsi Analytics. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'assegnazione di etichette e la segnalazione dei dati sensibili nei database.

I dati più sensibili possono includere informazioni aziendali, finanziarie, sanitarie o personali. L'individuazione e la classificazione di questi dati possono svolgere un ruolo fondamentale nell'approccio di protezione delle informazioni dell'organizzazione. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:

- Supporto per soddisfare gli standard per la privacy dei dati e i requisiti per la conformità alle normative.
- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso e rafforzare la protezione dei database che contengono dati altamente sensibili.

> [!NOTE]
> Per informazioni sulle SQL Server locali, vedere [SQL Data Discovery & Classification](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Che cos'è l'individuazione dei dati & classificazione?

Individuazione dati & classificazione introduce un set di servizi avanzati e nuove funzionalità in Azure. Costituisce un nuovo paradigma di protezione delle informazioni per il database SQL, il Istanza gestita SQL e la sinapsi di Azure, finalizzata alla protezione dei dati e non solo del database. Il paradigma include:

- **Individuazione e consigli:** Il motore di classificazione esegue l'analisi del database e identifica le colonne che contengono dati potenzialmente sensibili. Viene quindi fornito un modo semplice per esaminare e applicare la classificazione consigliata tramite il portale di Azure.

- **Assegnazione di etichette:** È possibile applicare le etichette di classificazione di riservatezza in modo permanente alle colonne usando nuovi attributi di metadati aggiunti al motore di database SQL Server. Questi metadati possono quindi essere utilizzati per scenari avanzati di controllo e protezione basati sulla sensibilità.

- **Risultato della query: impostare la sensibilità:** La sensibilità di un set di risultati della query viene calcolata in tempo reale a scopo di controllo.

- **Visibilità:** È possibile visualizzare lo stato di classificazione del database in un dashboard dettagliato nel portale di Azure. Inoltre, è possibile scaricare un report in formato Excel per utilizzarlo a scopo di conformità e controllo e altre esigenze.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Individuare, classificare ed etichettare colonne sensibili

In questa sezione vengono descritti i passaggi per:

- Individuazione, classificazione e assegnazione di etichette a colonne contenenti dati sensibili nel database.
- Visualizzazione dello stato di classificazione corrente del database ed esportazione dei report.

La classificazione include due attributi di metadati:

- **Labels** : attributi di classificazione principali, usati per definire il livello di sensibilità dei dati archiviati nella colonna.  
- **Tipi di informazioni** : attributi che forniscono informazioni più granulari sul tipo di dati archiviati nella colonna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definire e personalizzare la tassonomia di classificazione

L'individuazione dei dati & classificazione viene fornita con un set predefinito di etichette di riservatezza e un set predefinito di tipi di informazioni e logica di individuazione. È ora possibile personalizzare questa tassonomia e definire, appositamente per il proprio ambiente, un set e una classificazione di pertinenza di costrutti.

È possibile definire e personalizzare la tassonomia della classificazione in un'unica posizione centralizzata per l'intera organizzazione di Azure. Tale percorso si trova nel [Centro sicurezza di Azure](../../security-center/security-center-introduction.md), come parte dei criteri di sicurezza. Questa attività può essere eseguita solo da un utente con diritti amministrativi per il gruppo di gestione radice dell'organizzazione.

Come parte della gestione dei criteri per la protezione delle informazioni, è possibile definire etichette personalizzate, classificarle e associarle a un set selezionato di tipi di informazioni. È anche possibile aggiungere tipi di informazioni personalizzate e configurarli con modelli di stringa. I modelli vengono aggiunti alla logica di individuazione per identificare questo tipo di dati nei database.

Per altre informazioni, vedere [personalizzare i criteri di SQL Information Protection nel centro sicurezza di Azure (anteprima)](../../security-center/security-center-info-protection-policy.md).

Dopo aver definito i criteri a livello di organizzazione, è possibile continuare a classificare i singoli database usando i criteri personalizzati.

### <a name="classify-your-database"></a>Classificare il database

> [!NOTE]
> Nell'esempio seguente viene usato il database SQL di Azure, ma è necessario selezionare il prodotto appropriato per cui si vuole configurare l'individuazione dei dati & classificazione.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare a **individuazione dati & classificazione** sotto l'intestazione sicurezza nel riquadro del database SQL di Azure. La scheda Panoramica include un riepilogo dello stato di classificazione corrente del database. Il riepilogo include un elenco dettagliato di tutte le colonne classificate, che è anche possibile filtrare per visualizzare solo parti dello schema, tipi di informazioni ed etichette specifici. Se non sono ancora state classificate colonne, [andare al passaggio 4](#step-4).

1. Per scaricare un report in formato Excel, scegliere **Esporta** dal menu superiore del riquadro.

1. <a id="step-4"></a>Per iniziare a classificare i dati, selezionare la scheda **classificazione** nella pagina **individuazione dati & classificazione** .

    Il motore di classificazione esegue l'analisi del database per le colonne contenenti dati potenzialmente sensibili e fornisce un elenco di classificazioni di colonna consigliate.

1. Visualizzare e applicare le raccomandazioni di classificazione:

   - Per visualizzare l'elenco delle classificazioni di colonna consigliate, selezionare il pannello raccomandazioni nella parte inferiore del riquadro.

   - Per accettare un suggerimento per una colonna specifica, selezionare la casella di controllo nella colonna sinistra della riga pertinente. Per contrassegnare tutte le raccomandazioni come accettate, selezionare la casella di controllo più a sinistra nell'intestazione della tabella consigli.

   - Per applicare le raccomandazioni selezionate, selezionare **accetta raccomandazioni selezionate** .

1. È anche possibile classificare manualmente le colonne, in alternativa o in aggiunta alla classificazione basata su consigli:

   1. Selezionare **Aggiungi classificazione** nel menu superiore del riquadro.

   1. Nella finestra del contesto visualizzata selezionare lo schema, la tabella e la colonna che si desidera classificare, nonché il tipo di informazioni e l'etichetta di riservatezza.

   1. Selezionare **Aggiungi classificazione** nella parte inferiore della finestra di contesto.

1. Per completare la classificazione e contrassegnare in modo permanente le colonne del database con i nuovi metadati di classificazione, selezionare **Salva** nel menu in alto della finestra.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Controllare l'accesso ai dati sensibili

Un aspetto importante del paradigma di protezione delle informazioni è la possibilità di monitorare l'accesso ai dati sensibili. Il [controllo SQL di Azure](../../azure-sql/database/auditing-overview.md) è stato migliorato per includere un nuovo campo nel log di controllo denominato `data_sensitivity_information` . Questo campo registra le classificazioni di riservatezza (etichette) dei dati restituiti da una query. Ecco un esempio:

![Log di controllo](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Autorizzazioni

Questi ruoli predefiniti possono leggere la classificazione dei dati di un database:

- Proprietario
- Reader
- Collaboratore
- Gestione della sicurezza SQL
- Amministratore accessi utente

Questi ruoli predefiniti possono modificare la classificazione dei dati di un database:

- Proprietario
- Collaboratore
- Gestione della sicurezza SQL

Altre informazioni sulle autorizzazioni basate sui ruoli in [RBAC di Azure](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gestire le classificazioni

È possibile usare T-SQL, un'API REST o PowerShell per gestire le classificazioni.

### <a name="use-t-sql"></a>Usare T-SQL

È possibile utilizzare T-SQL per aggiungere o rimuovere classificazioni di colonne e per recuperare tutte le classificazioni per l'intero database.

> [!NOTE]
> Quando si usa T-SQL per gestire le etichette, non esiste alcuna convalida che le etichette aggiunte a una colonna esistano nei criteri di protezione delle informazioni dell'organizzazione (il set di etichette visualizzato nelle raccomandazioni del portale). Quindi, è possibile convalidare questa operazione.

Per informazioni sull'uso di T-SQL per le classificazioni, vedere i riferimenti seguenti:

- Per aggiungere o aggiornare la classificazione di una o più colonne: [Aggiungi classificazione di riservatezza](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Per rimuovere la classificazione da una o più colonne: [Elimina classificazione di riservatezza](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Per visualizzare tutte le classificazioni nel database: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Usare i cmdlet di PowerShell
Gestire le classificazioni e le raccomandazioni per il database SQL di Azure e Istanza gestita SQL di Azure con PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlet di PowerShell per il database SQL di Azure

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Cmdlet di PowerShell per Istanza gestita SQL di Azure

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Usare l'API REST

È possibile usare l'API REST per gestire le classificazioni e le raccomandazioni a livello di codice. L'API REST pubblicata supporta le operazioni seguenti:

- [Crea o aggiorna](/rest/api/sql/sensitivitylabels/createorupdate): crea o aggiorna l'etichetta di riservatezza della colonna specificata.
- [Delete](/rest/api/sql/sensitivitylabels/delete): Elimina l'etichetta di riservatezza della colonna specificata.
- [Disabilita Raccomandazione: Disabilita](/rest/api/sql/sensitivitylabels/disablerecommendation)le raccomandazioni di riservatezza per la colonna specificata.
- [Enable recommendation: Abilita](/rest/api/sql/sensitivitylabels/enablerecommendation)le raccomandazioni di riservatezza nella colonna specificata. (Le indicazioni sono abilitate per impostazione predefinita in tutte le colonne).
- [Get](/rest/api/sql/sensitivitylabels/get): Ottiene l'etichetta di riservatezza della colonna specificata.
- [List Current by database](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): ottiene le etichette di riservatezza correnti del database specificato.
- [Elenco consigliato per database](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): ottiene le etichette di riservatezza consigliate del database specificato.

## <a name="next-steps"></a><a id="next-steps"></a>Passaggi successivi

- Prendere in considerazione la configurazione del [controllo SQL di Azure](../../azure-sql/database/auditing-overview.md) per il monitoraggio e il controllo dell'accesso ai dati riservati classificati.
- Per una presentazione che includa l'individuazione dei dati & classificazione, vedere [individuazione, classificazione, assegnazione di etichette & protezione dei dati SQL | Dati esposti](https://www.youtube.com/watch?v=itVi9bkJUNc).