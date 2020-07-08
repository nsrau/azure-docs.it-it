---
title: Sicurezza dei dati avanzata
description: Informazioni sulle funzionalità per l'individuazione e la classificazione dei dati sensibili, la gestione delle vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database nel database SQL di Azure, in Azure SQL Istanza gestita o in una sinapsi di Azure.
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
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983995"
---
# <a name="advanced-data-security"></a>Sicurezza dei dati avanzata
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Advanced Data Security (ADS) è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. Sono disponibili annunci per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics. Include funzionalità per l'individuazione e la classificazione di dati sensibili, il rilevamento e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per il database. Offre una posizione unica per l'abilitazione e la gestione di queste funzionalità.

## <a name="overview"></a>Panoramica

ADS fornisce un set di funzionalità avanzate per la sicurezza di SQL, tra cui individuazione dati & classificazione, valutazione della vulnerabilità SQL e Advanced Threat Protection.
- L' [individuazione dei dati & classificazione](data-discovery-and-classification-overview.md) offre funzionalità integrate nel database SQL di Azure, in azure SQL istanza gestita e nelle sinapsi di Azure per l'individuazione, la classificazione, l'assegnazione di etichette e la creazione di report sui dati sensibili nei database. Consente di visualizzare lo stato di classificazione del database e di tenere traccia dell'accesso ai dati sensibili all'interno del database e all'esterno di questo ambito.
- La [valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio facile da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include passaggi di utilità pratica per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
- [Advanced Threat Protection](threat-detection-overview.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Monitora costantemente il database per individuare le attività sospette e fornisce avvisi di sicurezza immediata su potenziali vulnerabilità, attacchi intrusivi in SQL di Azure e modelli di accesso al database anomali. Gli avvisi della funzionalità di Advanced Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

Abilitare la sicurezza dei dati avanzata una volta per abilitare tutte le funzionalità incluse. Con un solo clic è possibile abilitare gli annunci per tutti i database nel [Server](logical-servers.md) in Azure o in SQL istanza gestita. Per abilitare o gestire le impostazioni degli annunci è necessario appartenere al ruolo [Gestione sicurezza SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) o a uno dei ruoli di amministratore del database o del server.

Il prezzo ADS è allineato al livello standard del Centro sicurezza di Azure, in cui ogni server protetto o istanza gestita viene conteggiato come un nodo. Le nuove risorse protette danno diritto a una versione di valutazione gratuita del livello Standard del Centro sicurezza. Per altre informazioni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Introduzione agli annunci

I passaggi seguenti consentono di iniziare a usare Advanced Data Security.

## <a name="1-enable-ads"></a>1. abilitare gli annunci

Per abilitare gli annunci, passare alla **sicurezza dei dati avanzata** sotto l'intestazione **sicurezza** per il server o l'istanza gestita.

> [!NOTE]
> Un account di archiviazione viene creato e configurato automaticamente per archiviare i risultati dell'analisi della **valutazione della vulnerabilità** . Se gli annunci sono già stati abilitati per un altro server nello stesso gruppo di risorse e nella stessa area, viene usato l'account di archiviazione esistente.

![Abilitare ADS](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> Il costo degli annunci è allineato ai prezzi del livello standard del Centro sicurezza di Azure per nodo, dove un nodo è l'intero server o istanza gestita. Si paga una sola volta per proteggere tutti i database nel server o nell'istanza gestita con ADS. È possibile provare Advanced Data Security con una versione di valutazione gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. iniziare a classificare i dati, tenere traccia delle vulnerabilità e analizzare gli avvisi per le minacce

Fare clic sulla scheda **Individuazione dati e classificazione** per visualizzare le colonne sensibili consigliate e per classificare i dati con etichette di riservatezza persistenti. Fare clic sulla scheda **Valutazione della vulnerabilità** per visualizzare e gestire le analisi e i report sulle vulnerabilità e tenere traccia del livello di sicurezza. Se sono stati ricevuti gli avvisi di sicurezza, fare clic sulla scheda **Advanced Threat Protection** per visualizzare i dettagli degli avvisi e per visualizzare un report consolidato su tutti gli avvisi nella sottoscrizione di Azure tramite la pagina avvisi di sicurezza del Centro sicurezza di Azure.

## <a name="3-manage-ads-settings"></a>3. gestire le impostazioni degli annunci

Per visualizzare e gestire le impostazioni ADS, passare a **Advanced Data Security** sotto l'intestazione di **sicurezza** per il server o l'istanza gestita. In questa pagina è possibile abilitare o disabilitare gli annunci e modificare la valutazione della vulnerabilità e le impostazioni di Advanced Threat Protection per l'intero server o istanza gestita.

![Impostazioni del server](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. gestire le impostazioni ADS per un database

Per eseguire l'override delle impostazioni di Sicurezza dei dati avanzata per uno specifico database, selezionare la casella di controllo **Abilita Sicurezza dei dati avanzata al livello del database**. Utilizzare questa opzione solo se si dispone di un particolare requisito per la ricezione di avvisi di protezione avanzata da minacce o di valutazione della vulnerabilità per il singolo database, al posto di o in aggiunta agli avvisi e ai risultati ricevuti per tutti i database nel server o nell'istanza gestita.

Una volta selezionata la casella di controllo, è possibile configurare le impostazioni rilevanti per questo database.

![Impostazioni di database e Advanced Threat Protection](./media/advanced-data-security/database_threat_detection_settings.png)

Le impostazioni avanzate di sicurezza dei dati per il server o l'istanza gestita possono essere raggiunte anche dal riquadro database ADS. Fare clic su **Impostazioni** nel riquadro principale di Advanced Data Security e fare clic su **View Advanced Data Security server settings** (Visualizza le impostazioni del server di Advanced Data Security).

![Impostazioni database](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [individuazione dei dati & classificazione](data-discovery-and-classification-overview.md)
- Scopri di più sulla [valutazione della vulnerabilità](sql-vulnerability-assessment.md)
- Altre informazioni su [Advanced Threat Protection](threat-detection-configure.md)
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
