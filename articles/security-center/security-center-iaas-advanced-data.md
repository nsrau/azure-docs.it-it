---
title: Avanzate di sicurezza dei dati per IaaS nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare la protezione avanzata dei dati per la tecnologia IaaS nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e7420adfe1608df39ef72124817f1d6dadf07db8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400158"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Protezione avanzata dei dati per le istanze di SQL Server in IaaS
Protezione avanzata dei dati per istanze di SQL Server in macchine virtuali di Azure è un pacchetto unificato per la funzionalità di sicurezza avanzate SQL. Include attualmente funzionalità per esporre e mitigazione delle potenziali vulnerabilità del database e rilevare attività anomale che indicano una minaccia al database. 

Questa offerta per i server SQL di Azure le macchine virtuali è basata sulla stessa tecnologia fondamentale utilizzata nel [pacchetto di protezione avanzata dei dati Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Panoramica

Protezione avanzata dei dati fornisce un set di funzionalità di sicurezza SQL avanzate, costituito la valutazione della vulnerabilità e Advanced Threat Protection.

* [Valutazione della vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include i passaggi per risolvere i problemi di sicurezza e migliorare fortifications il database.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei computer SQL server. Viene continuamente monitora il tuo database per le attività sospette e fornisce avvisi di sicurezza orientate alle azioni in modelli di accesso ai database anomali. Questi avvisi forniscono i dettagli delle attività sospette e le azioni consigliate per indagini e mitigare la minaccia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Iniziare con sicurezza avanzata di dati per SQL in macchine virtuali di Azure

I passaggi seguenti consentono di iniziare con sicurezza avanzata di dati per SQL in macchine virtuali di Azure.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurare sicurezza avanzata di dati per SQL in macchine virtuali di Azure

**Prima di iniziare**: È necessario un'area di lavoro di Log Analitica per archiviare i log di sicurezza che si sta analizzati. Se non è uno, quindi è possibile crearne uno con facilità, come illustrato in [creare un'area di lavoro di Log Analitica nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Connettere la macchina virtuale che ospita SQL server per l'area di lavoro di Log Analitica. Per istruzioni, vedere [i computer Windows di connettersi a monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Da Azure Marketplace, passare al [soluzione di protezione dati avanzata SQL](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(È possibile trovarlo con l'opzione di ricerca marketplace, come vedere nell'immagine seguente.) Il **protezione dati avanzata SQL** verrà visualizzata la pagina.

    ![Protezione avanzata dei dati per IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Fare clic su **Create**(Crea). Vengono visualizzate le aree di lavoro.

    ![Creare la protezione avanzata dei dati](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Selezionare l'area di lavoro da usare e fare clic su **Create**.

   ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Riavviare il [SQL server della macchina virtuale](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Esplorare e analizzare gli avvisi di sicurezza

È possibile visualizzare e gestire gli avvisi di sicurezza corrente.

1. Fare clic su **Centro sicurezza** > **avvisi di sicurezza**e fare clic su un avviso.

    ![Trovare l'avviso](./media/security-center-advanced-iaas-data/find-alert.png)

1. Dal **risorsa attacco** colonna, fare clic su una risorsa che ha subito un attacco.

1. Per visualizzare i dettagli degli avvisi e azioni per analizzare la minaccia corrente e la risoluzione di eventuali minacce future, scorrere verso il basso il **informazioni generali** pagina e nel **i passaggi correttivi** sezione, fare clic sulla  **PASSAGGI di analisi** collegamento.

    ![Procedura di correzione](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Per visualizzare i log che sono associati all'attivazione dell'avviso, passare a **registrare le aree di lavoro di analitica** e procedere come segue:

     > [!NOTE]
     > Se **registrare le aree di lavoro di analitica** non vengono visualizzati nel menu a sinistra, fare clic su **tutti i servizi**e cercare **Log le aree di lavoro di analitica**.

    1. Verificare che le colonne vengono visualizzati i **piano tariffario** e **WorkspaceID** colonne. (**Registrare le aree di lavoro di analitica** > **Upravit sloupce**, aggiungere **tariffario** e **WorkspaceID**.)

     ![Upravit Sloupce](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Fare clic sull'area di lavoro con i log di avviso.

    1. Sotto il **generali** menu, fare clic su **log**

    1. Fare clic accanto a occhi **SQLAdvancedThreatProtection** tabella. I log sono elencati.

     ![Visualizzare i log](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Impostare notifiche Email per avvisi di ATP 

È possibile impostare un elenco di destinatari per ricevere una notifica di posta elettronica quando vengono generati avvisi di Centro sicurezza di AZURE. Messaggio di posta elettronica contiene un collegamento diretto dell'avviso nel Centro sicurezza di Azure con tutti i relativi dettagli. 

1. Passare a **Centro sicurezza** > **criteri di sicurezza** quindi nella riga della selezione sottoscrizione da usare **modificare le impostazioni >** .

    ![Impostazioni di sottoscrizione](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Dal **le impostazioni** dal menu **notifiche tramite posta elettronica**. 
1. Nel **indirizzo di posta elettronica** testo casella, immettere gli indirizzi di posta elettronica per ricevere le notifiche. È possibile immettere più di un indirizzo di posta elettronica, separare gli indirizzi di posta elettronica con una virgola (,).  Ad esempio admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Impostazioni di posta elettronica](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nel **notifica tramite posta elettronica** impostazioni, impostare le opzioni seguenti:
  
    * **Inviare notifiche email per avvisi con gravità alta**: Anziché inviare messaggi di posta elettronica per tutti gli avvisi, invia solo per avvisi con gravità alta.
    * **Inviare notifiche di posta elettronica anche ai proprietari di sottoscrizioni**:  Inviare notifiche ai proprietari di sottoscrizioni troppo.

1. Nella parte superiore della **notifiche tramite posta elettronica** schermata, fare clic su **salvare**.

  > [!NOTE]
  > Assicurarsi di fare clic su **salvare** prima di chiudere la finestra o il nuovo **notifica di posta elettronica** impostazioni non verranno salvate.

## <a name="explore-vulnerability-assessment-reports"></a>Esplorare i report di valutazione della vulnerabilità

Il dashboard di valutazione della vulnerabilità viene fornita una panoramica dei risultati della valutazione tra tutti i database. È possibile visualizzare la distribuzione dei database in base alla versione di SQL Server, insieme a un riepilogo del failover e il passaggio di database e un riepilogo generale dei controlli in base alla distribuzione di rischio non superati.

È possibile visualizzare i risultati della valutazione delle vulnerabilità e i report direttamente dal Log Analitica.

1. Passare all'area di lavoro di Log Analitica con la soluzione di protezione dati avanzata.
1. Passare a **Solutions** e selezionare il **valutazione della vulnerabilità SQL** soluzione.
1. Nel **Summary** riquadro, fare clic su **Visualizza il riepilogo** e selezionare il **Report di valutazione della vulnerabilità SQL**.

    ![Report di valutazione SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Consente di caricare il dashboard di report. Assicurarsi che sia impostato l'intervallo di tempo per almeno il **ultimi 7 giorni** poiché analisi di valutazione delle vulnerabilità vengono eseguite sui database su una pianificazione fissa di una volta per ogni 7 giorni.

    ![Impostare negli ultimi 7 giorni](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Per eseguire il drill-per altri dettagli, fare clic su uno degli elementi del dashboard. Ad esempio:

   1. Fare clic su un segno di spunta una vulnerabilità il **Failed controlla riepilogo** sezione per visualizzare una tabella di Log Analitica con i risultati per questo controllo su tutti i database. Quelli che hanno i risultati vengono elencati per primi.

   1. Quindi, fare clic per visualizzare i dettagli per ciascuna vulnerabilità, tra cui la descrizione delle vulnerabilità e impatto, rischio associato e stato i risultati effettivi in questo database. È anche possibile visualizzare la Query effettiva che è stata eseguita per eseguire questo controllo e sulle azioni di correzione per risolvere questa vulnerabilità.

    ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Selezionare l'area di lavoro](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. È possibile eseguire qualsiasi query di Log Analitica sui dati dei risultati di valutazione della vulnerabilità, per suddividere e ripartire i dati in base alle esigenze.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Advanced Threat Protection per SQL Server in macchine virtuali di Azure gli avvisi
Gli avvisi vengono generati dai tentativi insoliti e potenzialmente dannosi di accesso o exploit dei server SQL. Questi eventi possono attivare gli avvisi seguenti:

### <a name="anomalous-access-pattern-alerts"></a>Avvisi di criterio di accesso anomalo

* **Accesso da posizione insolita:** questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. Possibili cause:
     * Un utilizzano dannoso ex o autore dell'attacco ha accesso di SQL Server.
     * Un utente autorizzato ha accesso di SQL Server da una nuova posizione.
* **Accesso da un'applicazione potenzialmente dannosa**: il suo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. Possibili cause:
     * Un utente malintenzionato sta tentando di violare la stringa SQL tramite strumenti comuni di attacco.
     * Un legittimo i test di penetrazione in azione.
* **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). Possibili cause:
     * Un utilizzano dannoso ex o autore dell'attacco ha accesso di SQL Server. 
     * Un utente autorizzato ha avuto accesso al Server di SQL da con una nuova entità.
* **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. Possibili cause:
     * Un utente malintenzionato sta tentando di violare la stringa SQL tramite attacchi di forza bruta.
     * Un legittimo i test di penetrazione in azione.

### <a name="potential-sql-injection-attacks-coming"></a>Potenziale attacco SQL Injection (in entrata)

* **Vulnerabilità agli attacchi SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Possibili cause:
     * Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
     * Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
* **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.
