---
title: Sicurezza avanzata dei dati per IaaS nel Centro sicurezza di Azure Documenti Microsoft
description: " Informazioni su come abilitare la sicurezza avanzata dei dati per IaaS nel Centro sicurezza di Azure.Learn how to enable advanced data security for IaaS in Azure Security Center. "
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
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282731"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Sicurezza avanzata dei dati per i server SQL nelle macchine virtuali di Azure (anteprima)Advanced data security for SQL servers on Azure Virtual Machines (Preview)
La sicurezza avanzata dei dati per SQL Server in Macchine virtuali di Azure è un pacchetto unificato per funzionalità di sicurezza SQL avanzate. Questa funzionalità di anteprima include funzionalità per identificare e ridurre potenziali vulnerabilità del database e rilevare attività anomale che potrebbero indicare minacce al database. 

Questa offerta di sicurezza per i server SQL delle macchine virtuali di Azure si basa sulla stessa tecnologia fondamentale usata nel pacchetto di sicurezza avanzata dei dati del [database SQL di Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="overview"></a>Panoramica

La sicurezza avanzata dei dati fornisce una serie di funzionalità di sicurezza SQL avanzate, costituite da Valutazione delle vulnerabilità e Advanced Threat Protection.

* [La valutazione delle vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio facile da configurare in grado di individuare, tenere traccia e aiutare a risolvere potenziali vulnerabilità del database. Fornisce visibilità sullo stato di sicurezza e include i passaggi per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare il server SQL. Monitora continuamente il database per individuare attività sospette e fornisce avvisi di sicurezza orientati alle azioni sui modelli di accesso al database anomali. Questi avvisi forniscono i dettagli delle attività sospette e le azioni consigliate per analizzare e mitigare la minaccia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Introduzione alla sicurezza avanzata dei dati per SQL nelle macchine virtuali di AzureGet started with Advanced data security for SQL on Azure VMs

I passaggi seguenti consentono di iniziare a usare Sicurezza avanzata dei dati per SQL in Azure VMs Public Preview.The following steps get you started with Advanced Data Security for SQL on Azure VMs Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurare la sicurezza avanzata dei dati per SQL nelle macchine virtuali di AzureSet up advanced data security for SQL on Azure VMs

Abilitare la sicurezza avanzata dei dati per SQL Server in macchine virtuali a livello di sottoscrizione/area di lavoro:Enable Advanced Data Security for SQL Servers on Virtual Machines at the subscription/workspace level:
 
1. Dalla barra laterale del Centro sicurezza, apri la pagina **Impostazioni & dei prezzi.**

1. Selezionare la sottoscrizione o l'area di lavoro per cui si vuole abilitare Sicurezza avanzata dei dati per SQL nelle macchine virtuali di Azure.Select the subscription or workspace for which you want to enable Advanced Data Security for SQL on Azure VMs.

1. Attivare o disattivare l'opzione per i **server SQL nella macchina virtuale (anteprima).** 

    (Fare clic sullo screenshot per espanderlo)

    [![Consigli e avvisi del Centro sicurezza visualizzati nell'interfaccia di amministrazione di Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    La sicurezza avanzata dei dati per SQL Server verrà abilitata in tutti i server SQL connessi all'area di lavoro selezionata o all'area di lavoro predefinita della sottoscrizione selezionata.

    >[!NOTE]
    > La soluzione sarà completamente attiva dopo il primo riavvio di SQL Server. 

Per creare una nuova area di lavoro, seguire le istruzioni in [Creare un'area](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)di lavoro di Log Analytics.

Per connettere l'host di SQL Server a un'area di lavoro, seguire le istruzioni in [Connettere computer Windows ad Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Configurare la notifica tramite posta elettronica per gli avvisi di sicurezza 

È possibile impostare un elenco di destinatari per ricevere una notifica tramite posta elettronica quando vengono generati avvisi del Centro sicurezza. Il messaggio di posta elettronica contiene un collegamento diretto all'avviso nel Centro sicurezza di Azure con tutti i dettagli pertinenti. 

1. Vai a**Impostazioni di & dei prezzi** del **Centro** > sicurezza e fai clic sull'abbonamento pertinente

    ![Impostazioni sottoscrizione](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Dal menu Impostazioni, fare clic su **Notifiche e-mail**. 
1. Nella casella di testo **Indirizzo di posta elettronica** immettere gli indirizzi di posta elettronica per ricevere le notifiche. È possibile immettere più indirizzi di posta elettronica separando gli indirizzi di posta elettronica con una virgola (,).  Ad admin1@mycompany.comesempioadmin2@mycompany.com, ,admin3@mycompany.com

    ![Impostazioni posta elettronica](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nelle impostazioni di **notifica e-mail,** impostare le seguenti opzioni:
  
    * **Invia notifica tramite posta elettronica per avvisi con gravità elevata:** anziché inviare messaggi di posta elettronica per tutti gli avvisi, invia solo per gli avvisi con gravità elevata.
    * **Invia anche notifiche e-mail ai proprietari delle sottoscrizioni:** invia notifiche anche ai proprietari delle sottoscrizioni.

1. Nella parte superiore della schermata **Notifiche e-mail,** fare clic su **Salva**.

  > [!NOTE]
  > Assicurarsi di fare clic su **Salva** prima di chiudere la finestra, altrimenti le nuove impostazioni di **notifica e-mail** non verranno salvate.

## <a name="explore-vulnerability-assessment-reports"></a>Esplora i report di valutazione delle vulnerabilità

Il dashboard di valutazione delle vulnerabilità fornisce una panoramica dei risultati della valutazione in tutti i database. È possibile visualizzare la distribuzione dei database in base alla versione di SQL ServerSQL Server , insieme a un riepilogo dei database con errori e di failover e a un riepilogo generale dei controlli non riusciti in base alla distribuzione dei rischi.

È possibile visualizzare i risultati della valutazione della vulnerabilità direttamente dal Centro sicurezza.

1. Dalla barra laterale del Centro sicurezza, in HYGIENE RESOURCE SECURITY, selezionare **Data & Storage**.

1. Selezionare il suggerimento **Vulnerabilità nei database SQL nelle macchine virtuali da correggere (anteprima)**. Per ulteriori informazioni, consultate Suggerimenti per il [Centro sicurezza](security-center-recommendations.md). 

    [![Le vulnerabilità nei database SQL nelle macchine virtuali devono essere corretti (anteprima)":](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Viene visualizzata la visualizzazione dettagliata per questa raccomandazione.

    [![È necessario correggere in modo dettagliato il numero di suggerimenti per la visualizzazione delle vulnerabilità nei database SQL nelle macchine virtuali.](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Per eseguire il drill-down per ulteriori dettagli:

    * Per una panoramica delle risorse analizzate (database) e dell'elenco dei controlli di sicurezza testati, fare clic sul server di interesse.
    [![Vulnerabilità raggruppate per SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Per una panoramica delle vulnerabilità raggruppate in base a un database SQL specifico, fare clic sul database di interesse.
    [![Vulnerabilità raggruppate per SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    In ogni visualizzazione, i controlli di sicurezza vengono ordinati in base a **Gravità**. Fare clic su un controllo di sicurezza specifico per visualizzare un riquadro dei dettagli con una **descrizione**, la modalità **di correzione** e altre informazioni correlate, ad esempio **Impact** o **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avvisi di protezione avanzata dalle minacce per i server SQL nelle macchine virtuali di AzureAdvanced threat protection for SQL server on Azure VMs alerts
Gli avvisi vengono generati da tentativi insoliti e potenzialmente dannosi di accedere o sfruttare SQL Server.Alerts are generated by unusual and potentially harmful attempts to access or exploit SQL Servers. Questi eventi possono attivare gli avvisi seguenti:

### <a name="anomalous-access-pattern-alerts-preview"></a>Avvisi del modello di accesso anomalo (anteprima)Anomalous access pattern alerts (Preview)

* **Accesso da luoghi insoliti:** Questo avviso viene attivato quando viene apportata una modifica al modello di accesso a SQL Server, in cui un utente ha eseguito l'accesso al server SQL da una posizione geografica insolita. Possibili cause:
    * Un utente malintenzionato o ex malintenzionato ha eseguito l'accesso a SQL Server.
    * Un utente legittimo ha eseguito l'accesso a SQL Server da una nuova posizione.
* **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. Possibili cause:
    * Un utente malintenzionato che tenta di violare il codice SQL utilizzando strumenti di attacco comuni.
    * Un test di penetrazione legittimo in azione.
* **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). Possibili cause:
    * Un utente malintenzionato o ex malintenzionato ha eseguito l'accesso a SQL Server. 
    * Un utente legittimo ha eseguito l'accesso a SQL Server da con una nuova entità.
* **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. Possibili cause:
    * Un utente malintenzionato che tenta di violare il codice SQL utilizzando la forza bruta.
    * Un test di penetrazione legittimo in azione.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenziali attacchi SQL injection (supportati in SQL Server 2019)Potential SQL injection attacks (Supported in SQL Server 2019)

* **Vulnerabilità all'inserimento SQL:** questo avviso viene attivato quando un'applicazione genera un'istruzione SQL errata nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Possibili cause:
    * Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
    * Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
* **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Comando Unsafe (supportato in SQL Server 2019)

* **Azione potenzialmente unsafe**: questo avviso viene attivato quando viene eseguito un comando con privilegi elevati e potenzialmente non sicuri. Possibili cause:
    * Comando che ha consigliato di essere disabilitato per una migliore posizione di sicurezza è abilitato.
    * Un utente malintenzionato che tenta di sfruttare i privilegi di accesso a SQL o di escalation.   


## <a name="explore-and-investigate-security-alerts"></a>Esplorare e analizzare gli avvisi di sicurezza

Gli avvisi di sicurezza dei dati sono disponibili nella pagina degli avvisi del Centro sicurezza, nella scheda di sicurezza della risorsa o tramite il collegamento diretto nei messaggi di posta elettronica degli avvisi.

1. Per visualizzare gli avvisi:

    * Nel Centro sicurezza: fai clic su **Avvisi di sicurezza** dalla barra laterale e seleziona un avviso.
    * Nell'ambito della risorsa: aprire la pagina delle risorse pertinente e nella barra laterale fare clic su **Sicurezza**. 

1. Gli avvisi sono progettati per essere autonomi, con procedure di correzione dettagliate e informazioni di indagine in ognuno di essi. È possibile analizzare ulteriormente usando altre funzionalità del Centro sicurezza di Azure e Di Azure Sentinel per una visualizzazione più ampia:You can investigate further by using other Azure Security Center and Azure Sentinel capabilities for a broader view:

    * Abilitare la funzionalità di controllo di SQL Server per ulteriori indagini. Se sei un utente di Azure Sentinel, puoi caricare i log di controllo SQL dagli eventi del registro di sicurezza di Windows in Sentinel e godere di un'esperienza di analisi avanzata.
    * Per migliorare la sicurezza, utilizzare i consigli del Centro sicurezza per la macchina host indicata in ogni avviso. Ciò ridurrà i rischi di attacchi futuri. 


## <a name="next-steps"></a>Passaggi successivi

Per il materiale correlato, vedere il seguente articolo:

- [Come correggere i suggerimenti](security-center-remediate-recommendations.md)