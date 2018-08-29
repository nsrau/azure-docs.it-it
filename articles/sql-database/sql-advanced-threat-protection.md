---
title: Advanced Threat Protection - Database SQL di Azure | Microsoft Docs
description: Informazioni sulle funzionalità per l'individuazione e la classificazione di dati sensibili, per la gestione delle vulnerabilità dei database e per il rilevamento di attività anomale che potrebbero indicare una minaccia al database SQL di Azure.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: 8881f66a73a2368696ee16cc0f9bb0b16d2f6d7e
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "42146208"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection per il database SQL di Azure

SQL Advanced Threat Protection è un pacchetto che raccoglie le funzionalità di sicurezza SQL avanzate. Include funzionalità per l'individuazione e la classificazione di dati sensibili, il rilevamento e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per il database. Offre una posizione unica per l'abilitazione e la gestione di queste funzionalità. 

## <a name="overview"></a>Panoramica

SQL Advanced Threat Protection (ATP) offre una serie di funzionalità di sicurezza SQL avanzate, tra cui Individuazione dati e classificazione, Valutazione della vulnerabilità e Rilevamento minacce. 

- [Individuazione dati e classificazione](sql-database-data-discovery-and-classification.md) (attualmente in anteprima) offre funzionalità avanzate integrate nel database SQL di Azure per l'individuazione, la classificazione, l'aggiunta di etichette e la protezione dei dati sensibili presenti nel database. Consente di visualizzare lo stato di classificazione del database e di tenere traccia dell'accesso ai dati sensibili all'interno del database e all'esterno di questo ambito.
- [Valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio semplice da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Consente di visualizzare lo stato di sicurezza e prevede passaggi utili per risolvere i problemi di sicurezza e migliorare la protezione del database.
- [Rilevamento minacce](sql-database-threat-detection.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza immediati su potenziali vulnerabilità, attacchi SQL injection e in caso di modelli di accesso ai database anomali. Gli avvisi della funzionalità di rilevamento minacce includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

Abilitare SQL Advanced Threat Protection una volta per abilitare tutte le funzionalità incluse. Con un solo clic è possibile abilitare Advanced Threat Protection nell'intero server di database, applicandolo a tutti i database nel server. 

I prezzi di ATP sono allineati con il livello Standard del Centro sicurezza di Azure alla tariffa di $ 15/nodo/mese, dove ogni server di database SQL viene conteggiato come un nodo. Nei primi 60 giorni dopo l'abilitazione si usa una versione di valutazione senza alcun addebito. Per altre informazioni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Introduzione ad Advanced Threat Protection 
I passaggi seguenti consentono di iniziare a usare Advanced Threat Protection. 

## <a name="1-enable-atp"></a>1. Abilitare Advanced Threat Protection

Abilitare Advanced Threat Protection passando ad **Advanced Threat Protection** nell'intestazione **Sicurezza** nel riquadro del database SQL di Azure. Per abilitare Advanced Threat Protection per tutti i database nel server, fare clic su **Abilita Advanced Threat Protection nel server**.

![Abilitare Advanced Threat Protection](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Il costo di Advanced Threat Protection è $ 15/nodo/mese, in cui un nodo è l'intero server logico SQL. Si paga solo una volta per proteggere tutti i database sul server con Advanced Threat Protection. Nei primi 60 giorni si usa una versione di valutazione senza alcun addebito.

## <a name="2-configure-vulnerability-assessment"></a>2. Configurare Valutazione della vulnerabilità

Per iniziare a usare Valutazione della vulnerabilità, è necessario configurare un account di archiviazione in cui vengono salvati i risultati dell'analisi. A tale scopo fare clic sulla scheda Valutazione della vulnerabilità.

![Configurare Valutazione della vulnerabilità](./media/sql-advanced-protection/configure_va.png) 

Selezionare o creare un account di archiviazione per salvare i risultati dell'analisi. È possibile anche attivare frequenti analisi periodiche per configurare Valutazione della vulnerabilità in modo da eseguire analisi automatiche una volta a settimana. Verrà inviato un riepilogo dei risultati di analisi agli indirizzi di posta elettronica forniti.

![Impostazioni di Valutazione della vulnerabilità](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Avviare la classificazione dei dati, il rilevamento delle vulnerabilità e l'analisi degli avvisi di minaccia

Fare clic sulla scheda **Individuazione dati e classificazione** per visualizzare le colonne sensibili consigliate e per classificare i dati con etichette di riservatezza persistenti. Fare clic sulla scheda **Valutazione della vulnerabilità** per visualizzare e gestire le analisi e i report sulle vulnerabilità e tenere traccia del livello di sicurezza. Se sono stati ricevuti avvisi di sicurezza, fare clic sulla scheda **Rilevamento minacce** per visualizzarne i dettagli e per esaminare un report consolidato di tutti gli avvisi nella sottoscrizione di Azure tramite la pagina degli avvisi di sicurezza nel Centro sicurezza di Azure.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Gestire le impostazioni di Advanced Threat Protection nel server SQL

Per visualizzare e gestire le impostazioni di Advanced Threat Protection, passare da **Advanced Threat Protection** nell'intestazione **Sicurezza** nel riquadro del server SQL. In questa pagina è possibile abilitare o disabilitare Advanced Threat Protection e modificare le impostazioni di rilevamento minacce per l'intero server SQL.

![Impostazioni del server](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Gestire le impostazioni di Advanced Threat Protection per un database SQL

Per eseguire l'override delle impostazioni di rilevamento minacce di Advanced Threat Protection per uno specifico database, selezionare la casella di controllo **Abilita Advanced Threat Protection al livello del database**. Usare questa opzione solo se è impostato un requisito specifico per ricevere avvisi di rilevamento minacce separati per il singolo database, in sostituzione o in aggiunta agli avvisi ricevuti per tutti i database nel server. 

Dopo avere selezionato la casella di controllo, fare clic su **Impostazioni di Rilevamento minacce per questo database** e configurare le impostazioni pertinenti per questo database.

![Impostazioni di rilevamento delle minacce e del database](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Le impostazioni di Advanced Threat Protection per il server sono disponibili anche nel riquadro del database di Advanced Threat Protection. Fare clic su **Impostazioni** nel riquadro principale di Advanced Threat Protection e fare clic su **Visualizza le impostazioni del server di Advanced Threat Protection**. 

![Impostazioni database](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Passaggi successivi 

- Altre informazioni su [Individuazione dati e classificazione](sql-database-data-discovery-and-classification.md) 
- Altre informazioni su [Valutazione della vulnerabilità](sql-vulnerability-assessment.md) 
- Altre informazioni sul [rilevamento delle minacce](sql-database-threat-detection.md)
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
