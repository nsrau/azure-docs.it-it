---
title: Supporto per lo spostamento delle risorse SQL di Azure tra le aree con Azure Resource Mover.
description: Esaminare il supporto per lo spostamento delle risorse SQL di Azure tra le aree con Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652870"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Supporto per lo trasferimento di risorse SQL di Azure tra aree di Azure

Questo articolo riepiloga il supporto e i prerequisiti per lo spostamento delle risorse SQL di Azure tra aree di Azure con il motore di risorse di Azure.

## <a name="requirements"></a>Requisiti

I requisiti sono riepilogati nella tabella seguente.

**Funzionalità** | **Supportato/Non supportato** | **Dettagli**
--- | --- | ---
**Hyperscale nel database SQL di Azure** | Non supportate | Non è possibile spostare i database nel livello di servizio dell'iperscalabilità SQL di Azure con il motore di risorse.
**Ridondanza della zona** | Funzionalità supportata |  Opzioni di spostamento supportate:<br/><br/> -Tra aree che supportano la ridondanza della zona.<br/><br/> -Tra aree che non supportano la ridondanza della zona.<br/><br/> -Tra un'area che supporta la ridondanza della zona in un'area che non supporta la ridondanza della zona.<br/><br/> -Tra un'area che non supporta la ridondanza della zona in un'area che supporta la ridondanza della zona. 
**Sincronizzazione dati** | Database hub/Sync: non supportato<br/><br/> Membro di sincronizzazione: supportato. | Se un membro di sincronizzazione viene spostato, è necessario configurare la sincronizzazione dei dati per il nuovo database di destinazione.
**Replica geografica esistente** | Funzionalità supportata | Le repliche geografiche esistenti vengono mappate nuovamente al nuovo database primario nell'area di destinazione.<br/><br/> Il seeding deve essere inizializzato dopo lo spostamento. [Altre informazioni](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transparent Data Encryption (Transparent Data Encryption) con Bring Your Own Key (BYOK)** | Funzionalità supportata | [Altre](../key-vault/general/move-region.md) informazioni sullo trasferimento di insiemi di credenziali delle chiavi tra le aree.
**Transparent Data Encryption con chiave gestita dal servizio** | Supportata. |  [Altre](../key-vault/general/move-region.md) informazioni sullo trasferimento di insiemi di credenziali delle chiavi tra le aree.
**Regole per la maschera dati dinamica** | Supportata. | Le regole vengono copiate automaticamente nell'area di destinazione come parte dello spostamento. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Sicurezza dei dati avanzata** | Non supportata. | Soluzione alternativa: configurare a livello di SQL Server nell'area di destinazione. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Regole del firewall** | Non supportata. | Soluzione alternativa: configurare le regole del firewall per SQL Server nell'area di destinazione. Le regole del firewall a livello di database vengono copiate dal server di origine al server di destinazione. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Criteri di controllo** | Non supportata. | I criteri vengono reimpostati su default dopo lo spostamento. [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) su come eseguire la reimpostazione.
**Conservazione dei backup** | Supportata. | I criteri di conservazione dei backup per il database di origine vengono trasferiti nel database di destinazione. [Informazioni](/azure/sql-database/sql-database-long-term-backup-retention-configure) su come modificare le impostazioni dopo lo spostamento.
**Ottimizzazione automatica** | Non supportata. | Soluzione alternativa: impostare le impostazioni di ottimizzazione automatica dopo lo spostamento. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Avvisi del database** | Non supportata. | Soluzione temporanea: impostare gli avvisi dopo lo spostamento. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**stretch database SQL Server di Azure** | Non Supportato | Non è possibile spostare SQL Server stretch database con il motore di risorse.
**Azure Synapse Analytics** | Non Supportato | Non è possibile spostare l'analisi sinapsi (in precedenza Azure SQL Data Warehouse) con il motore di risorse.
## <a name="next-steps"></a>Passaggi successivi

Provare [le risorse SQL di Azure](tutorial-move-region-sql.md) in un'altra area con il motore di risorse.