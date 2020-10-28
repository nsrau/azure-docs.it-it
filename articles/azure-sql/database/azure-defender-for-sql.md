---
title: Azure Defender per SQL
description: Informazioni sulle funzionalità per la gestione delle vulnerabilità del database e sul rilevamento di attività anomale che potrebbero indicare una minaccia per il database nel database SQL di Azure, in Istanza gestita di Azure SQL o in una sinapsi di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d147303df43c4f86843df518c71316e6a97b6671
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678084"
---
# <a name="azure-defender-for-sql"></a>Azure Defender per SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender per SQL è un pacchetto unificato che include le funzionalità di sicurezza avanzate SQL. Azure Defender è disponibile per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics. Include funzionalità per l'individuazione e la classificazione di dati sensibili, il rilevamento e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per il database. Offre una posizione unica per l'abilitazione e la gestione di queste funzionalità.

## <a name="overview"></a>Panoramica

Azure Defender offre un set di funzionalità avanzate per la sicurezza di SQL, tra cui la valutazione della vulnerabilità SQL e la protezione avanzata dalle minacce.
- La [valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio facile da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include passaggi di utilità pratica per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
- [Advanced Threat Protection](threat-detection-overview.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Monitora costantemente il database per individuare le attività sospette e fornisce avvisi di sicurezza immediata su potenziali vulnerabilità, attacchi intrusivi in SQL di Azure e modelli di accesso al database anomali. Gli avvisi della funzionalità di Advanced Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

Abilitare Azure Defender per SQL una volta per abilitare tutte le funzionalità incluse. Con un solo clic è possibile abilitare Azure Defender per tutti i database nel [Server](logical-servers.md) in Azure o in SQL istanza gestita. Per abilitare o gestire le impostazioni di Azure Defender è necessario appartenere al ruolo [Gestione sicurezza SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) o a uno dei ruoli di amministratore del database o del server.

Per altre informazioni sui prezzi di Azure Defender per SQL, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Introduzione ad Azure Defender

Per iniziare a usare Azure Defender, seguire questa procedura.

## <a name="enable-azure-defender"></a>Abilitare Azure Defender

È possibile accedere ad Azure Defender tramite il [portale di Azure](https://portal.azure.com). Abilitare Azure Defender passando al **Centro sicurezza** sotto l'intestazione di **sicurezza** per il server o l'istanza gestita.

> [!NOTE]
> Un account di archiviazione viene creato e configurato automaticamente per archiviare i risultati dell'analisi della **valutazione della vulnerabilità** . Se Azure Defender è già stato abilitato per un altro server nello stesso gruppo di risorse e nella stessa area, viene usato l'account di archiviazione esistente.
>
> Il costo di Azure Defender è allineato con i prezzi del livello standard del Centro sicurezza di Azure per nodo, dove un nodo è l'intero server o istanza gestita. Si paga una sola volta per proteggere tutti i database nel server o nell'istanza gestita con Azure Defender. È possibile provare Azure Defender inizialmente con una versione di valutazione gratuita.

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Inizia a tenere traccia delle vulnerabilità ed esamina gli avvisi relativi alle minacce

Fare clic sulla scheda **Valutazione della vulnerabilità** per visualizzare e gestire le analisi e i report sulle vulnerabilità e tenere traccia del livello di sicurezza. Se sono stati ricevuti gli avvisi di sicurezza, fare clic sulla scheda **Advanced Threat Protection** per visualizzare i dettagli degli avvisi e per visualizzare un report consolidato su tutti gli avvisi nella sottoscrizione di Azure tramite la pagina avvisi di sicurezza del Centro sicurezza di Azure.

## <a name="manage-azure-defender-settings"></a>Gestire le impostazioni di Azure Defender

Per visualizzare e gestire le impostazioni di Azure Defender, passare al **Centro sicurezza** sotto l'intestazione di **sicurezza** per il server o l'istanza gestita. In questa pagina è possibile abilitare o disabilitare Azure Defender e modificare la valutazione della vulnerabilità e le impostazioni di Advanced Threat Protection per l'intero server o istanza gestita.

## <a name="manage-azure-defender-settings-for-a-database"></a>Gestire le impostazioni di Azure Defender per un database

Per eseguire l'override delle impostazioni di Azure Defender per un determinato database, selezionare la casella **di controllo Abilita Azure Defender per SQL a livello di database** . Utilizzare questa opzione solo se si dispone di un particolare requisito per la ricezione di avvisi di protezione avanzata da minacce o di valutazione della vulnerabilità per il singolo database, al posto di o in aggiunta agli avvisi e ai risultati ricevuti per tutti i database nel server o nell'istanza gestita.

Una volta selezionata la casella di controllo, è possibile configurare le impostazioni rilevanti per questo database.

Azure Defender per le impostazioni SQL per il server o l'istanza gestita può essere raggiunto anche dal riquadro del database di Azure Defender. Fare clic su **Impostazioni** nel riquadro principale di Azure Defender, quindi fare clic su **Visualizza Azure Defender per le impostazioni di SQL Server** .

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Valutazione della vulnerabilità](sql-vulnerability-assessment.md)
- Altre informazioni su [Advanced Threat Protection](threat-detection-configure.md)
- Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)