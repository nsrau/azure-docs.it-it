---
title: Sicurezza dei dati avanzata per IaaS nel centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare la sicurezza dei dati avanzata per IaaS nel centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 287da68617a9527bc398df577cf8d10773fa8557
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202182"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Sicurezza dei dati avanzata per SQL Server in macchine virtuali di Azure (anteprima pubblica)
La protezione avanzata dei dati per SQL Server in macchine virtuali di Azure è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. Attualmente (in anteprima pubblica) include funzionalità per l'emersione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database. 

Questa offerta di sicurezza per le macchine virtuali di Azure SQL Server è basata sulla stessa tecnologia fondamentale usata nel [pacchetto di sicurezza dei dati avanzato del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Panoramica

Advanced Data Security offre un set di funzionalità avanzate per la sicurezza di SQL, costituite da valutazione della vulnerabilità e da Advanced Threat Protection.

* [Valutazione della vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include i passaggi per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit di SQL Server. Monitora costantemente il database per le attività sospette e fornisce avvisi di sicurezza orientati alle azioni sui modelli di accesso al database anomali. Questi avvisi forniscono i dettagli delle attività sospette e le azioni consigliate per analizzare e mitigare la minaccia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Introduzione alla sicurezza dei dati avanzata per SQL in macchine virtuali di Azure

La procedura seguente illustra come iniziare a usare Advanced Data Security per SQL in macchine virtuali di Azure in anteprima pubblica.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurare la sicurezza dei dati avanzata per SQL in macchine virtuali di Azure

**Prima di iniziare**: Per archiviare i log di sicurezza analizzati, è necessaria un'area di lavoro Log Analytics. Se non si dispone di un, è possibile crearne uno facilmente, come illustrato in [creare un'area di lavoro log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Connettere la macchina virtuale che ospita il server SQL all'area di lavoro Log Analytics. Per istruzioni, vedere [connettere computer Windows a monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Da Azure Marketplace passare alla [soluzione SQL Advanced Data Security](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(È possibile trovarlo usando l'opzione di ricerca nel Marketplace, come illustrato nella figura seguente). Verrà visualizzata la pagina **SQL Advanced Data Security** .

    ![Sicurezza avanzata dei dati per IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Fare clic su **Create**(Crea). Vengono visualizzate le area di lavoro.

    ![Creazione avanzata della sicurezza dei dati](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selezionare l'area di lavoro da usare e fare clic su **Crea**.

   ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Riavviare il [server SQL della macchina virtuale](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Esplorare ed esaminare gli avvisi di sicurezza

È possibile visualizzare e gestire gli avvisi di sicurezza correnti.

1. Fare clic su**avvisi di sicurezza**del **centro** > sicurezza e fare clic su un avviso.

    ![Trova avviso](./media/security-center-advanced-iaas-data/find-alert.png)

1. Dalla colonna **risorsa attaccata** fare clic su una risorsa che è stata attaccata.

1. Per visualizzare i dettagli e le azioni degli avvisi per esaminare la minaccia corrente e risolvere le minacce future, scorrere la pagina **informazioni generali** e nella sezione **passaggi correttivi** fare clic sul collegamento **passaggi di analisi** .

    ![Procedura di correzione](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Per visualizzare i log associati all'attivazione dell'avviso, passare ad **aree di lavoro di log Analytics** e seguire questa procedura:

     > [!NOTE]
     > Se le **aree di lavoro di log Analytics** non vengono visualizzate nel menu a sinistra, fare clic su **tutti i servizi**e cercare le **aree di lavoro di log Analytics**.

    1. Assicurarsi che le colonne visualizzino il piano tariffario e le colonne **WorkspaceID** . (Le**aree di lavoro** > di log Analytics modificano le**colonne**, aggiungono il piano **tariffario** e **WorkspaceID**.)

     ![Modifica colonne](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Fare clic sull'area di lavoro che contiene i log degli avvisi.

    1. Scegliere **registri** dal menu **generale**

    1. Fare clic sull'occhio accanto alla tabella **SQLAdvancedThreatProtection** . Sono elencati i log.

     ![Visualizza log](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Configurare la notifica di posta elettronica per gli avvisi ATP 

È possibile impostare un elenco di destinatari per ricevere una notifica tramite posta elettronica quando vengono generati gli avvisi ASC. Il messaggio di posta elettronica contiene un collegamento diretto all'avviso nel centro sicurezza di Azure con tutti i dettagli pertinenti. 

1. Passare a prezzi del **centro** > sicurezza **& impostazioni** e fare clic sulla sottoscrizione pertinente

    ![Impostazioni sottoscrizione](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Scegliere **notifiche tramite posta elettronica**dal menu **Impostazioni** . 
1. Nella casella di testo **indirizzo di posta elettronica** immettere gli indirizzi di posta elettronica per ricevere le notifiche. È possibile immettere più di un indirizzo di posta elettronica separando gli indirizzi di posta elettronica con una virgola (,).  Ad esempio admin1@mycompany.comadmin2@mycompany.com,,admin3@mycompany.com

      ![Impostazioni posta elettronica](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nelle impostazioni di **notifica tramite posta elettronica** impostare le opzioni seguenti:
  
    * **Invia notifica tramite posta elettronica per gli avvisi con livello di gravità elevato**: Anziché inviare messaggi di posta elettronica per tutti gli avvisi, inviare solo per gli avvisi con gravità elevata.
    * **Inviare notifiche tramite posta elettronica anche ai proprietari della sottoscrizione**:  Inviare anche le notifiche ai proprietari delle sottoscrizioni.

1. Nella parte superiore della schermata **notifiche posta elettronica** fare clic su **Salva**.

  > [!NOTE]
  > Assicurarsi di fare clic su **Salva** prima di chiudere la finestra. in alternativa, le nuove impostazioni di **notifica tramite posta elettronica** non verranno salvate.

## <a name="explore-vulnerability-assessment-reports"></a>Esplorare i report di valutazione della vulnerabilità

Il dashboard della valutazione della vulnerabilità fornisce una panoramica dei risultati della valutazione in tutti i database. È possibile visualizzare la distribuzione dei database in base alla versione SQL Server, insieme a un riepilogo del mancato rispetto del passaggio dei database e di un riepilogo generale dei controlli non riusciti in base alla distribuzione dei rischi.

È possibile visualizzare i risultati e i report di valutazione della vulnerabilità direttamente da Log Analytics.

1. Passare all'area di lavoro Log Analytics con la soluzione di sicurezza dei dati avanzata.
1. Passare a **soluzioni** e selezionare la soluzione di **valutazione della vulnerabilità di SQL** .
1. Nel riquadro **Riepilogo** fare clic su **Visualizza riepilogo** e selezionare il **report di valutazione della vulnerabilità SQL**.

    ![Report Valutazione SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Il dashboard del report viene caricato. Verificare che l'intervallo di tempo sia impostato su almeno gli **ultimi 7 giorni** dall'esecuzione delle analisi di valutazione della vulnerabilità sui database in base a una pianificazione fissa di una volta ogni 7 giorni.

    ![Imposta ultimi 7 giorni](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Per eseguire il drill-down per altri dettagli, fare clic su uno degli elementi del dashboard. Esempio:

   1. Fare clic su un controllo della vulnerabilità nella sezione **Riepilogo controlli non riusciti** per visualizzare una tabella log Analytics con i risultati per questo controllo in tutti i database. Quelli con risultati vengono elencati per primi.

   1. Fare quindi clic per visualizzare i dettagli di ogni vulnerabilità, incluse la descrizione della vulnerabilità e l'effetto, lo stato, il rischio associato e i risultati effettivi sul database. È anche possibile visualizzare la query effettiva che è stata eseguita per eseguire questo controllo e le informazioni di monitoraggio e aggiornamento per la risoluzione di questa vulnerabilità.

    ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. È possibile eseguire qualsiasi query Log Analytics sui dati dei risultati della valutazione della vulnerabilità, per sezionare e tagliare i dati in base alle esigenze.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avvisi di Advanced Threat Protection per SQL Server in macchine virtuali di Azure
Gli avvisi vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o exploit di SQL Server. Questi eventi possono attivare gli avvisi seguenti:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Avvisi del modello di accesso anomalo (supportati nell'anteprima pubblica)

* **Accesso da una posizione insolita:** questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. Possibili cause:
     * Un utente malintenzionato o un utilizzo dannoso precedente ha eseguito l'accesso al SQL Server.
     * Un utente legittimo ha eseguito l'accesso al SQL Server da una nuova posizione.
* **Accesso da un'applicazione potenzialmente dannosa**: il suo avviso viene attivato quando un'applicazione potenzialmente dannosa viene utilizzata per accedere al database. Possibili cause:
     * Un utente malintenzionato sta tentando di violare SQL usando gli strumenti di attacco più comuni.
     * Test di penetrazione legittimi in azione.
* **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). Possibili cause:
     * Un utente malintenzionato o un utilizzo dannoso precedente ha eseguito l'accesso al SQL Server. 
     * Un utente legittimo ha eseguito l'accesso al SQL Server da con una nuova entità.
* **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. Possibili cause:
     * Un utente malintenzionato sta tentando di violare il SQL usando forza bruta.
     * Test di penetrazione legittimi in azione.

### <a name="potential-sql-injection-attacks-coming"></a>Potenziali attacchi intrusivi nel codice SQL (in arrivo)

* **Vulnerabilità agli attacchi SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Possibili cause:
     * Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
     * Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
* **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.
