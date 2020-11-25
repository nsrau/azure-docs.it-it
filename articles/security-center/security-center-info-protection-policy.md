---
title: Criteri di SQL Information Protection nel centro sicurezza di Azure
description: Informazioni su come personalizzare i criteri di protezione delle informazioni in Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013786"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Criteri di SQL Information Protection nel centro sicurezza di Azure
 
Il [meccanismo di individuazione e classificazione dei dati](../azure-sql/database/data-discovery-and-classification-overview.md) di SQL Information Protection offre funzionalità avanzate per l'individuazione, la classificazione, l'assegnazione di etichette e la creazione di report sui dati sensibili nei database. È incorporato nel [database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md), in [Azure SQL istanza gestita](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)e in [Azure sinapsi Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Il meccanismo di classificazione è basato sui due elementi seguenti:

- **Labels** : gli attributi di classificazione principali, usati per definire il *livello di sensibilità dei dati* archiviati nella colonna. 
- **Tipi di informazioni** : fornisce granularità aggiuntiva al *tipo di dati* archiviati nella colonna.

Le opzioni dei criteri di Information Protection all'interno del Centro sicurezza forniscono un set predefinito di etichette e tipi di informazioni che fungeranno da impostazioni predefinite per il motore di classificazione. È possibile personalizzare il criterio in base alle esigenze dell'organizzazione, come descritto di seguito.

> [!IMPORTANT]
> Per personalizzare i criteri di Information Protection per il tenant di Azure, sono necessari privilegi amministrativi per il gruppo di gestione radice del tenant. Per altre informazioni, vedere [ottenere visibilità a livello di tenant per il Centro sicurezza di Azure](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Pagina che mostra i criteri di SQL Information Protection":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Ricerca per categorie accedere ai criteri di SQL Information Protection?

Sono disponibili tre modi per accedere ai criteri di Information Protection:

- **(Scelta consigliata)** Dalla pagina prezzi e impostazioni del Centro sicurezza
- Dalla raccomandazione di sicurezza "i dati sensibili nei database SQL devono essere classificati"
- Dalla pagina di individuazione dati del database SQL di Azure

Ognuno di questi elementi viene visualizzato nella scheda pertinente riportata di seguito.



### <a name="from-security-centers-settings"></a>[**Dalle impostazioni del Centro sicurezza**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Accedere al criterio dalla pagina dei prezzi e delle impostazioni del Centro sicurezza <a name="sqlip-tenant"></a>

Dalla pagina **prezzi e impostazioni** del Centro sicurezza selezionare **SQL Information Protection**.

> [!NOTE]
> Questa opzione viene visualizzata solo per gli utenti con autorizzazioni a livello di tenant. 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Accesso ai criteri di Information Protection SQL dalla pagina dei prezzi e delle impostazioni del Centro sicurezza di Azure":::



### <a name="from-security-centers-recommendation"></a>[**Dal Consiglio del Centro sicurezza**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Accedere ai criteri dalla raccomandazione del Centro sicurezza <a name="sqlip-db"></a>

Usare la raccomandazione del Centro sicurezza "i dati sensibili nei database SQL devono essere classificati" per visualizzare la pagina individuazione dati e classificazione per il database. Verranno inoltre visualizzate le colonne individuate che contengono informazioni che è consigliabile classificare.

1. Dalla pagina **raccomandazioni** del Centro sicurezza, cercare i **dati sensibili delle raccomandazioni nei database SQL da classificare**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Trovare la raccomandazione che fornisce l'accesso ai criteri di SQL Information Protection":::

1. Nella pagina Dettagli raccomandazione selezionare un database dalle schede **integre** o non **integre** .

1. Verrà visualizzata la pagina **individuazione dati & classificazione** . Selezionare **Configura**.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Apertura dei criteri di SQL Information Protection dalla raccomandazione pertinente del Centro sicurezza di Azure":::



### <a name="from-azure-sql"></a>[**Da SQL di Azure**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Accedere ai criteri da Azure SQL <a name="sqlip-azuresql"></a>

1. Dal portale di Azure aprire SQL Azure.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Apertura di Azure SQL dalla portale di Azure":::

1. Selezionare un database.

1. Dall'area **sicurezza** del menu aprire la pagina **individuazione dati & classificazione** (1) e selezionare **Configura** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Apertura dei criteri di SQL Information Protection da Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Personalizzare i tipi di informazioni

Per gestire e personalizzare i tipi di informazioni:

1. Selezionare **Gestisci tipi di informazioni**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Gestire i tipi di informazioni per i criteri di Information Protection":::

1. Per aggiungere un nuovo tipo, selezionare **Crea tipo di informazioni**. È possibile configurare un nome, una descrizione e le stringhe dei criteri di ricerca per il tipo di informazioni. Le stringhe dei criteri di ricerca possono contenere facoltativamente parole chiave con caratteri jolly, indicati dal carattere '%', che il motore di rilevamento automatico usa per identificare i dati sensibili nei database in base ai metadati delle colonne.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Configurare un nuovo tipo di informazioni per i criteri di Information Protection":::

1. È anche possibile modificare i tipi incorporati aggiungendo stringhe di criteri di ricerca aggiuntive, disabilitando alcune delle stringhe esistenti o modificando la descrizione. 

    > [!TIP]
    > Non è possibile eliminare i tipi incorporati o modificarne i nomi. 

1. I **tipi di informazioni** sono elencati in ordine di classificazione dell'individuazione decrescente, il che significa che i tipi più in alto nell'elenco tenteranno di trovare una corrispondenza prima dei tipi seguenti. Per modificare la classificazione di due tipi, trascinare i tipi nel punto desiderato della tabella oppure usare i pulsanti **Sposta su** e **Sposta giù** per modificare l'ordine. 

1. Al termine, fare clic su **OK** .

1. Dopo aver completato la gestione dei tipi di informazioni, associare i tipi alle etichette pertinenti facendo clic su **Configura** per una determinata etichetta e aggiungendo o eliminando i tipi di informazioni da associare a essa.

1. Per applicare le modifiche, selezionare **Salva** nella pagina principale **etichette** .
 

## <a name="exporting-and-importing-a-policy"></a>Esportazione e importazione di un criterio

È possibile scaricare un file JSON con le etichette e i tipi di informazioni definiti, modificare il file nell'editor di propria scelta e quindi importare il file aggiornato. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Esportazione e importazione dei criteri di Information Protection":::

> [!NOTE]
> È necessario disporre delle autorizzazioni a livello di tenant per importare un file di criteri. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Gestire SQL Information Protection usando Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Recupera il criterio tenant SQL Information Protection effettivo.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): imposta i criteri di SQL Information Protection del tenant effettivi.
 

## <a name="next-steps"></a>Passaggi successivi
 
In questo articolo si è appreso come definire i criteri di protezione delle informazioni nel centro sicurezza di Azure. Per altre informazioni sull'utilizzo di SQL Information Protection per classificare e proteggere i dati sensibili nel database SQL, vedere [Individuazione dati e classificazione nel database SQL di Azure](../azure-sql/database/data-discovery-and-classification-overview.md).

Per ulteriori informazioni sui criteri di sicurezza e sulla protezione dei dati nel centro sicurezza, vedere gli articoli seguenti:
 
- [Impostazione dei criteri di sicurezza nel centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure
- [Sicurezza dei dati del Centro sicurezza di Azure](security-center-data-security.md): informazioni su come il Centro sicurezza gestisce e protegge i dati
