---
title: Individuazione dati e classificazione
description: Individuazione dei dati & classificazione per il database SQL di Azure e Azure Synapse AnalyticsData Discovery & Classification for Azure SQL Database and Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616768"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Individuazione dei dati & classificazione per il database SQL di Azure e Azure Synapse AnalyticsData Discovery & Classification for Azure SQL Database and Azure Synapse Analytics

La classificazione & di individuazione dei dati è incorporata nel database SQL di Azure.Data Discovery & Classification is built into Azure SQL Database. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'etichettatura e la segnalazione dei dati sensibili nei database.

I tuoi dati più sensibili potrebbero includere informazioni aziendali, finanziarie, sanitarie o personali. L'individuazione e la classificazione di questi dati possono svolgere un ruolo fondamentale nell'approccio di protezione delle informazioni dell'organizzazione. Possono costituire l'infrastruttura per:

- Contribuire a soddisfare gli standard per la privacy dei dati e i requisiti per la conformità alle normative.
- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllo dell'accesso e protezione avanzata della sicurezza dei database che contengono dati altamente sensibili.

Data Discovery & Classification fa parte dell'offerta [Advanced Data Security,](sql-database-advanced-data-security.md) un pacchetto unificato per funzionalità di sicurezza SQL avanzate. È possibile accedere e gestire la classificazione & di individuazione dei dati tramite la sezione **centrale SQL Advanced Data Security** del portale di Azure.You can access and manage Data Discovery & Classification via the central SQL Advanced Data Security section of the Azure portal.

> [!NOTE]
> Questo articolo si riferisce al database SQL di Azure e ad Azure Synapse Analytics.This article relates to Azure SQL Database and Azure Synapse Analytics. Per semplicità, è possibile usare *il database SQL* per fare riferimento sia al database SQL che a Azure Synapse.For simplicity, we use SQL Database here to refer to both SQL Database and Azure Synapse. Per informazioni su SQL Server (locale), vedere [Individuazione e classificazione dei dati SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Che cos'è la classificazione & di individuazione dei dati?

Data Discovery & Classification introduce una serie di servizi avanzati e nuove funzionalità del database SQL. Forma un nuovo paradigma di protezione delle informazioni per il database SQL, volto a proteggere i dati e non solo il database. Il paradigma include:

- **Scoperta e raccomandazioni:** Il motore di classificazione analizza il database e identifica le colonne che contengono dati potenzialmente sensibili. Fornisce quindi un modo semplice per esaminare e applicare la classificazione consigliata tramite il portale di Azure.It then provides you with an easy way to review and apply recommended classification via the Azure portal.

- **Etichettatura:** È possibile applicare le etichette di classificazione di riservatezza in modo permanente alle colonne utilizzando nuovi attributi dei metadati che sono stati aggiunti al motore di database SQL. Questi metadati possono quindi essere utilizzati per scenari avanzati di controllo e protezione basati sulla sensibilità.

- **Sensibilità del set di risultati della query:Query result-set sensitivity:** La riservatezza di un set di risultati di query viene calcolata in tempo reale ai fini del controllo.

- **Visibilità:** È possibile visualizzare lo stato di classificazione del database in un dashboard dettagliato nel portale di Azure.You can view the database-classification state in a detailed dashboard in the Azure portal. Inoltre, è possibile scaricare un report in formato Excel da utilizzare per scopi di conformità e controllo e altre esigenze.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Individuare, classificare ed etichettare le colonne sensibili

In questa sezione vengono descritti i passaggi per:

- Individuazione, classificazione ed etichettatura delle colonne che contengono dati sensibili nel database.
- Visualizzazione dello stato di classificazione corrente del database ed esportazione dei report.

La classificazione include due attributi di metadati:

- **Etichette**: Gli attributi di classificazione principali, utilizzati per definire il livello di sensibilità dei dati memorizzati nella colonna.  
- **Tipi di informazioni**: attributi che forniscono informazioni più granulari sul tipo di dati archiviati nella colonna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definire e personalizzare la tassonomia di classificazione

Data Discovery & Classification è dotato di un set incorporato di etichette di riservatezza e di un set incorporato di tipi di informazioni e logica di individuazione. È ora possibile personalizzare questa tassonomia e definire, appositamente per il proprio ambiente, un set e una classificazione di pertinenza di costrutti.

È possibile definire e personalizzare la tassonomia di classificazione in un'unica posizione centrale per l'intera organizzazione di Azure.You define and customize of your classification taxonomy in one central place for your entire Azure organization. Tale percorso si trova nel [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), come parte dei criteri di sicurezza. Solo un utente con diritti amministrativi sul gruppo di gestione radice dell'organizzazione può eseguire questa attività.

Come parte della gestione dei criteri per SQL Information Protection, è possibile definire etichette personalizzate, classificarle e associarle a un set selezionato di tipi di informazioni. È inoltre possibile aggiungere tipi di informazioni personalizzati e configurarli con modelli di stringa. I modelli vengono aggiunti alla logica di individuazione per identificare questo tipo di dati nei database.

Per ulteriori informazioni sulla personalizzazione e la gestione dei criteri, vedere la guida alle procedure per i criteri di [SQL Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Dopo aver definito i criteri a livello di organizzazione, è possibile continuare a classificare i singoli database utilizzando i criteri personalizzati.

### <a name="classify-your-sql-database"></a>Classificare il database SQL

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare a **Sicurezza avanzata dei dati** sotto l'intestazione Sicurezza nel riquadro Database SQL di Azure.Go to Advanced Data Security under the **Security** heading in your Azure SQL Database pane. Selezionare **Sicurezza avanzata dei dati**e quindi selezionare la scheda Individuazione dati & **Classificazione.**

   ![Riquadro Sicurezza avanzata dei dati nel portale di AzureAdvanced Data Security pane in Azure portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Nella pagina **Individuazione dati & classificazione,** la scheda **Panoramica** include un riepilogo dello stato di classificazione corrente del database. Il riepilogo include un elenco dettagliato di tutte le colonne classificate, che è anche possibile filtrare per visualizzare solo specifiche parti dello schema, tipi di informazioni ed etichette. Se non è ancora stata classificata alcuna colonna, [andare al passaggio 5](#step-5).

   ![Riepilogo dello stato corrente di classificazione](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Per scaricare un report in formato Excel, selezionare **Esporta** nel menu superiore del riquadro.

5. <a id="step-5"></a>Per iniziare a classificare i dati, selezionare la scheda **Classificazione** nella pagina **Classificazione & archiviazione dati.**

    Il motore di classificazione analizza il database alla ricerca di colonne contenenti dati potenzialmente sensibili e fornisce un elenco delle classificazioni di colonna consigliate.

6. Visualizzare e applicare i suggerimenti per la classificazione:

   - Per visualizzare l'elenco delle classificazioni di colonna consigliate, selezionare il pannello dei suggerimenti nella parte inferiore del riquadro.

   - Per accettare un suggerimento per una colonna specifica, selezionare la casella di controllo nella colonna sinistra della riga pertinente. Per contrassegnare tutti i suggerimenti come accettati, selezionare la casella di controllo all'estrema sinistra nell'intestazione della tabella dei suggerimenti.

       ![Esaminare e selezionare dall'elenco di raccomandazioni per la classificazione](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Per applicare i suggerimenti selezionati, selezionare **Accetta raccomandazioni selezionate**.

7. È inoltre possibile classificare le colonne manualmente, in alternativa o in aggiunta alla classificazione basata su raccomandazioni:You can also classify columns manually, as an alternative or in addition to the recommendation-based classification:

   1. Selezionare **Aggiungi classificazione** nel menu superiore del riquadro.

   1. Nella finestra di contesto visualizzata selezionare lo schema, la tabella e la colonna che si desidera classificare, nonano il tipo di informazioni e l'etichetta di riservatezza.

   1. Selezionare **Aggiungi classificazione** nella parte inferiore della finestra di contesto.

      ![Selezionare una colonna da classificare](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Per completare la classificazione ed etichettare in modo permanente (tag) le colonne del database con i nuovi metadati di classificazione, selezionare **Salva** nel menu superiore della finestra.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Controllare l'accesso ai dati sensibili

Un aspetto importante del paradigma di protezione delle informazioni è la capacità di monitorare l'accesso ai dati sensibili. [Il controllo del database SQL](sql-database-auditing.md) di Azure è stato `data_sensitivity_information`migliorato per includere un nuovo campo nel log di controllo denominato . Questo campo registra le classificazioni di riservatezza (etichette) dei dati restituiti da una query. Ad esempio:

![Log di controllo](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Autorizzazioni

Questi ruoli predefiniti possono leggere la classificazione dei dati di un database SQL di Azure:These built-in roles can read the data classification of an Azure SQL database:

- Proprietario
- Reader
- Collaboratore
- Gestione della sicurezza SQL
- Amministratore accessi utente

Questi ruoli predefiniti possono modificare la classificazione dei dati di un database SQL di Azure:These built-in roles can modify the data classification of an Azure SQL database:

- Proprietario
- Collaboratore
- Gestione della sicurezza SQL

Altre informazioni sulle autorizzazioni basate sui ruoli in RBAC per le risorse di Azure.Learn more about role-based permissions in [RBAC for Azure resources](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gestire le classificazioni

È possibile usare T-SQL, un'API REST o PowerShell per gestire le classificazioni.

### <a name="use-t-sql"></a>Usare T-SQL

È possibile utilizzare T-SQL per aggiungere o rimuovere classificazioni di colonne e per recuperare tutte le classificazioni per l'intero database.

> [!NOTE]
> Quando si utilizza T-SQL per gestire le etichette, non esiste alcuna convalida che le etichette aggiunte a una colonna esistano nei criteri di protezione delle informazioni dell'organizzazione (il set di etichette visualizzate nei consigli del portale). Quindi, sta a voi convalidare questo.

Per informazioni sull'utilizzo di T-SQL per le classificazioni, vedere i riferimenti seguenti:For information about using T-SQL for classifications, see the following references:

- Per aggiungere o aggiornare la classificazione di una o più colonne: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Per rimuovere la classificazione da una o più colonne: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Per visualizzare tutte le classificazioni nel database: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Usare l'API RestUse the Rest API

È possibile usare l'API REST per gestire a livello di codice classificazioni e consigli. L'API REST pubblicata supporta le operazioni seguenti:The published REST API supports the following operations:

- [Crea o aggiorna](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Crea o aggiorna l'etichetta di riservatezza della colonna specificata.
- [Elimina](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Elimina l'etichetta di sensibilità della colonna specificata.
- [Disabilita raccomandazione](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Disabilita le raccomandazioni di sensibilità sulla colonna specificata.
- [Abilita raccomandazione](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Abilita i suggerimenti di sensibilità sulla colonna specificata. (Le raccomandazioni sono abilitate per impostazione predefinita in tutte le colonne.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Ottiene l'etichetta di sensibilità della colonna specificata.
- [List Current By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Ottiene le etichette di sensibilità correnti del database specificato.
- [List Recommended By Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Ottiene le etichette di sensibilità consigliate del database specificato.

### <a name="use-powershell-cmdlets"></a>Usare i cmdlet di PowerShell
È possibile usare PowerShell per gestire le classificazioni e i consigli per il database SQL di Azure e le istanze gestite.

#### <a name="powershell-cmdlets-for-sql-database"></a>Cmdlet di PowerShell per il database SQL

- [Get-AzSqlDatabaseSensitivityClassificationGet-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Abilitazione-AzSqlDatabaSesensitivityrecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendationDisable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Cmdlet di PowerShell per le istanze gestite

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendationDisable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Advanced Data Security](sql-database-advanced-data-security.md).
- Valutare l'opportunità di configurare [il servizio di controllo del database SQL di Azure](sql-database-auditing.md) per il monitoraggio e il controllo dell'accesso ai dati sensibili classificati.
- Per una presentazione che include l'individuazione e la classificazione dei dati, vedere [Individuazione, classificazione, etichettatura & protezione dei dati SQL. Dati esposti](https://www.youtube.com/watch?v=itVi9bkJUNc).
