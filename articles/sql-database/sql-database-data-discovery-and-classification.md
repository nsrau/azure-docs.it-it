---
title: Individuazione dati e classificazione
description: Individuazione dei dati & classificazione per il database SQL di Azure e Azure sinapsi Analytics
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
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: 27989687934719be5f1d18b85d3ead92f28b3f60
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123854"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Individuazione dei dati & classificazione per il database SQL di Azure e Azure sinapsi Analytics

L'individuazione dei dati & classificazione è incorporata nel database SQL di Azure. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'assegnazione di etichette e la segnalazione dei dati sensibili nei database.

I dati più sensibili possono includere informazioni aziendali, finanziarie, sanitarie o personali. L'individuazione e la classificazione di questi dati possono svolgere un ruolo fondamentale nell'approccio di protezione delle informazioni dell'organizzazione. Possono costituire l'infrastruttura per:

- Supporto per soddisfare gli standard per la privacy dei dati e i requisiti per la conformità alle normative.
- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso e rafforzare la protezione dei database che contengono dati altamente sensibili.

L'individuazione dei dati & classificazione fa parte dell'offerta [Advanced Data Security](sql-database-advanced-data-security.md) , che è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere e gestire l'individuazione dei dati & classificazione tramite la sezione **sicurezza avanzata dei dati di SQL** centrale del portale di Azure.

> [!NOTE]
> Questo articolo si riferisce al database SQL di Azure e ad analisi delle sinapsi di Azure. Per semplicità, si usa il *database SQL* qui per fare riferimento sia al database SQL che alla sinapsi di Azure. Per informazioni sulle SQL Server (in locale), vedere [individuazione e classificazione dei dati SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Che cos'è l'individuazione dei dati & classificazione?

Individuazione dati & classificazione introduce un set di servizi avanzati e nuove funzionalità di database SQL. Costituisce un nuovo paradigma di protezione delle informazioni per il database SQL, finalizzato alla protezione dei dati e non solo del database. Il paradigma include:

- **Individuazione e consigli:** Il motore di classificazione esegue l'analisi del database e identifica le colonne che contengono dati potenzialmente sensibili. Viene quindi fornito un modo semplice per esaminare e applicare la classificazione consigliata tramite il portale di Azure.

- **Assegnazione di etichette:** È possibile applicare le etichette di classificazione di riservatezza in modo permanente alle colonne usando nuovi attributi di metadati aggiunti al motore di database SQL. Questi metadati possono quindi essere utilizzati per scenari avanzati di controllo e protezione basati sulla sensibilità.

- **Risultato della query: impostare la sensibilità:** La sensibilità di un set di risultati della query viene calcolata in tempo reale a scopo di controllo.

- **Visibilità:** È possibile visualizzare lo stato di classificazione del database in un dashboard dettagliato nel portale di Azure. Inoltre, è possibile scaricare un report in formato Excel per utilizzarlo a scopo di conformità e controllo e altre esigenze.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Individuare, classificare ed etichettare colonne sensibili

In questa sezione vengono descritti i passaggi per:

- Individuazione, classificazione e assegnazione di etichette a colonne contenenti dati sensibili nel database.
- Visualizzazione dello stato di classificazione corrente del database ed esportazione dei report.

La classificazione include due attributi di metadati:

- **Labels**: attributi di classificazione principali, usati per definire il livello di sensibilità dei dati archiviati nella colonna.  
- **Tipi di informazioni**: attributi che forniscono informazioni più granulari sul tipo di dati archiviati nella colonna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definire e personalizzare la tassonomia di classificazione

L'individuazione dei dati & classificazione viene fornita con un set predefinito di etichette di riservatezza e un set predefinito di tipi di informazioni e logica di individuazione. È ora possibile personalizzare questa tassonomia e definire, appositamente per il proprio ambiente, un set e una classificazione di pertinenza di costrutti.

È possibile definire e personalizzare la tassonomia della classificazione in un'unica posizione centralizzata per l'intera organizzazione di Azure. Tale percorso si trova nel [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), come parte dei criteri di sicurezza. Questa attività può essere eseguita solo da un utente con diritti amministrativi per il gruppo di gestione radice dell'organizzazione.

Come parte della gestione dei criteri per SQL Information Protection, è possibile definire etichette personalizzate, classificarle e associarle a un set selezionato di tipi di informazioni. È anche possibile aggiungere tipi di informazioni personalizzate e configurarli con modelli di stringa. I modelli vengono aggiunti alla logica di individuazione per identificare questo tipo di dati nei database.

Per ulteriori informazioni sulla personalizzazione e la gestione dei criteri, vedere la guida dettagliata ai [criteri di SQL Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Dopo aver definito i criteri a livello di organizzazione, è possibile continuare a classificare i singoli database usando i criteri personalizzati.

### <a name="classify-your-sql-database"></a>Classificare il database SQL

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare a **Advanced Data Security** nell'intestazione **sicurezza** nel riquadro del database SQL di Azure. Selezionare **sicurezza dati avanzata**e quindi selezionare la scheda di **classificazione individuazione dati &** .

   ![Riquadro sicurezza dati avanzata in portale di Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. Nella pagina **individuazione dati & classificazione** la scheda **Panoramica** include un riepilogo dello stato di classificazione corrente del database. Il riepilogo include un elenco dettagliato di tutte le colonne classificate, che è anche possibile filtrare per visualizzare solo parti dello schema, tipi di informazioni ed etichette specifici. Se non sono ancora state classificate colonne, [andare al passaggio 5](#step-5).

   ![Riepilogo dello stato corrente di classificazione](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Per scaricare un report in formato Excel, scegliere **Esporta** dal menu superiore del riquadro.

5. <a id="step-5"></a>Per iniziare a classificare i dati, selezionare la scheda **classificazione** nella pagina **individuazione dati & classificazione** .

    Il motore di classificazione esegue l'analisi del database per le colonne contenenti dati potenzialmente sensibili e fornisce un elenco di classificazioni di colonna consigliate.

6. Visualizzare e applicare le raccomandazioni di classificazione:

   - Per visualizzare l'elenco delle classificazioni di colonna consigliate, selezionare il pannello raccomandazioni nella parte inferiore del riquadro.

   - Per accettare un suggerimento per una colonna specifica, selezionare la casella di controllo nella colonna sinistra della riga pertinente. Per contrassegnare tutte le raccomandazioni come accettate, selezionare la casella di controllo più a sinistra nell'intestazione della tabella consigli.

       ![Esaminare e selezionare un elenco di consigli per la classificazione](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Per applicare le raccomandazioni selezionate, selezionare **accetta raccomandazioni selezionate**.

7. È anche possibile classificare manualmente le colonne, in alternativa o in aggiunta alla classificazione basata su consigli:

   1. Selezionare **Aggiungi classificazione** nel menu superiore del riquadro.

   1. Nella finestra del contesto visualizzata selezionare lo schema, la tabella e la colonna che si desidera classificare, nonché il tipo di informazioni e l'etichetta di riservatezza.

   1. Selezionare **Aggiungi classificazione** nella parte inferiore della finestra di contesto.

      ![Selezionare una colonna da classificare](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Per completare la classificazione e contrassegnare in modo permanente le colonne del database con i nuovi metadati di classificazione, selezionare **Salva** nel menu in alto della finestra.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Controllare l'accesso ai dati sensibili

Un aspetto importante del paradigma di protezione delle informazioni è la possibilità di monitorare l'accesso ai dati sensibili. Il [controllo del database SQL di Azure](sql-database-auditing.md) è stato migliorato per includere un nuovo campo nel log di controllo denominato `data_sensitivity_information` . Questo campo registra le classificazioni di riservatezza (etichette) dei dati restituiti da una query. Ad esempio:

![Log di controllo](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Autorizzazioni

Questi ruoli predefiniti possono leggere la classificazione dei dati di un database SQL di Azure:

- Proprietario
- Lettore
- Collaboratore
- Gestione della sicurezza SQL
- Amministratore accessi utente

Questi ruoli predefiniti possono modificare la classificazione dei dati di un database SQL di Azure:

- Proprietario
- Collaboratore
- Gestione della sicurezza SQL

Altre informazioni sulle autorizzazioni basate sui ruoli in [RBAC per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gestire le classificazioni

È possibile usare T-SQL, un'API REST o PowerShell per gestire le classificazioni.

### <a name="use-t-sql"></a>Usare T-SQL

È possibile utilizzare T-SQL per aggiungere o rimuovere classificazioni di colonne e per recuperare tutte le classificazioni per l'intero database.

> [!NOTE]
> Quando si usa T-SQL per gestire le etichette, non esiste alcuna convalida che le etichette aggiunte a una colonna esistano nei criteri di protezione delle informazioni dell'organizzazione (il set di etichette visualizzato nelle raccomandazioni del portale). Quindi, è possibile convalidare questa operazione.

Per informazioni sull'uso di T-SQL per le classificazioni, vedere i riferimenti seguenti:

- Per aggiungere o aggiornare la classificazione di una o più colonne: [Aggiungi classificazione di riservatezza](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Per rimuovere la classificazione da una o più colonne: [Elimina classificazione di riservatezza](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Per visualizzare tutte le classificazioni nel database: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Usare i cmdlet di PowerShell
È possibile usare PowerShell per gestire le classificazioni e le raccomandazioni per il database SQL di Azure e le istanze gestite.

#### <a name="powershell-cmdlets-for-sql-database"></a>Cmdlet di PowerShell per database SQL

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Cmdlet di PowerShell per le istanze gestite

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Usare l'API REST

È possibile usare l'API REST per gestire le classificazioni e le raccomandazioni a livello di codice. L'API REST pubblicata supporta le operazioni seguenti:

- [Crea o aggiorna](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): crea o aggiorna l'etichetta di riservatezza della colonna specificata.
- [Delete](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Elimina l'etichetta di riservatezza della colonna specificata.
- [Disabilita Raccomandazione: Disabilita](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)le raccomandazioni di riservatezza per la colonna specificata.
- [Enable recommendation: Abilita](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)le raccomandazioni di riservatezza nella colonna specificata. (Le indicazioni sono abilitate per impostazione predefinita in tutte le colonne).
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Ottiene l'etichetta di riservatezza della colonna specificata.
- [List Current by database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): ottiene le etichette di riservatezza correnti del database specificato.
- [Elenco consigliato per database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): ottiene le etichette di riservatezza consigliate del database specificato.

## <a name="next-steps"></a><a id="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sicurezza dei dati avanzata](sql-database-advanced-data-security.md).
- Valutare l'opportunità di configurare [il servizio di controllo del database SQL di Azure](sql-database-auditing.md) per il monitoraggio e il controllo dell'accesso ai dati sensibili classificati.
- Per una presentazione che includa individuazione e classificazione dei dati, vedere Individuazione [, classificazione, assegnazione di etichette & protezione dei dati SQL | Dati esposti](https://www.youtube.com/watch?v=itVi9bkJUNc).
