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
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907019"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Sicurezza dei dati avanzata per SQL Server in macchine virtuali di Azure (anteprima)
La protezione avanzata dei dati per SQL Server in macchine virtuali di Azure è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. Questa funzionalità di anteprima include funzionalità per l'identificazione e la mitigazione di potenziali vulnerabilità del database e per il rilevamento di attività anomale che potrebbero indicare minacce per il database. 

Questa offerta di sicurezza per le macchine virtuali di Azure SQL Server è basata sulla stessa tecnologia fondamentale usata nel [pacchetto di sicurezza dei dati avanzato del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Overview

Advanced Data Security offre un set di funzionalità avanzate per la sicurezza di SQL, costituite da valutazione della vulnerabilità e da Advanced Threat Protection.

* [Valutazione della vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include i passaggi per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit di SQL Server. Monitora costantemente il database per le attività sospette e fornisce avvisi di sicurezza orientati alle azioni sui modelli di accesso al database anomali. Questi avvisi forniscono i dettagli delle attività sospette e le azioni consigliate per analizzare e mitigare la minaccia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Introduzione alla sicurezza dei dati avanzata per SQL in macchine virtuali di Azure

La procedura seguente illustra come iniziare a usare Advanced Data Security per SQL in macchine virtuali di Azure in anteprima pubblica.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configurare la sicurezza dei dati avanzata per SQL in macchine virtuali di Azure

Abilitare la sicurezza dei dati avanzata per SQL Server in macchine virtuali a livello di sottoscrizione/area di lavoro:
 
1. Dalla barra laterale del Centro sicurezza, aprire la pagina **impostazioni & prezzi** .

1. Selezionare la sottoscrizione o l'area di lavoro per cui si vuole abilitare la sicurezza dei dati avanzata per SQL in macchine virtuali di Azure.

1. Attivare o disabilitare l'opzione per **SQL Server nella macchina virtuale (anteprima)** . 

    (Fare clic sullo screenshot per espanderlo)

    [![raccomandazioni e avvisi del Centro sicurezza come visualizzato nell'interfaccia di amministrazione di Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    La protezione avanzata dei dati per SQL Server verrà abilitata in tutti i server SQL connessi all'area di lavoro selezionata o nell'area di lavoro predefinita della sottoscrizione selezionata.

    >[!NOTE]
    > La soluzione sarà completamente attiva dopo il primo riavvio del SQL Server. 

Per creare una nuova area di lavoro, seguire le istruzioni riportate in [creare un'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Per connettere l'host del SQL Server a un'area di lavoro, seguire le istruzioni riportate in [connettere computer Windows a monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-atp-alerts"></a>Configurare la notifica di posta elettronica per gli avvisi ATP 

È possibile impostare un elenco di destinatari per ricevere una notifica tramite posta elettronica quando vengono generati gli avvisi del Centro sicurezza. Il messaggio di posta elettronica contiene un collegamento diretto all'avviso nel centro sicurezza di Azure con tutti i dettagli pertinenti. 

1. Passare al **Centro sicurezza** > **impostazioni di prezzi &** e fare clic sulla sottoscrizione pertinente

    ![Impostazioni sottoscrizione](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Scegliere **notifiche tramite posta elettronica**dal menu impostazioni. 
1. Nella casella di testo **indirizzo di posta elettronica** immettere gli indirizzi di posta elettronica per ricevere le notifiche. È possibile immettere più di un indirizzo di posta elettronica separando gli indirizzi di posta elettronica con una virgola (,).  Ad esempio admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![Impostazioni di posta elettronica](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nelle impostazioni di **notifica tramite posta elettronica** impostare le opzioni seguenti:
  
    * **Inviare notifiche di posta elettronica per gli avvisi con livello di gravità elevato**: anziché inviare messaggi di posta elettronica per tutti gli avvisi, inviare solo per gli avvisi con gravità elevata
    * **Inviare notifiche tramite posta elettronica anche ai proprietari della sottoscrizione**: inviare notifiche anche ai proprietari delle sottoscrizioni.

1. Nella parte superiore della schermata **notifiche posta elettronica** fare clic su **Salva**.

  > [!NOTE]
  > Assicurarsi di fare clic su **Salva** prima di chiudere la finestra. in alternativa, le nuove impostazioni di **notifica tramite posta elettronica** non verranno salvate.

## <a name="explore-vulnerability-assessment-reports"></a>Esplorare i report di valutazione della vulnerabilità

Il dashboard della valutazione della vulnerabilità fornisce una panoramica dei risultati della valutazione in tutti i database. È possibile visualizzare la distribuzione dei database in base alla versione SQL Server, insieme a un riepilogo del mancato rispetto del passaggio dei database e di un riepilogo generale dei controlli non riusciti in base alla distribuzione dei rischi.

È possibile visualizzare i risultati della valutazione della vulnerabilità direttamente dal centro sicurezza.

1. Dall'intestazione laterale del Centro sicurezza, in igiene sicurezza risorse selezionare **dati & archiviazione**.

1. Selezionare le vulnerabilità di raccomandazione nei **database SQL nelle VM da correggere (anteprima)** . Per altre informazioni, vedere [raccomandazioni del Centro sicurezza](security-center-recommendations.md). 

    [![* * le vulnerabilità nei database SQL nelle macchine virtuali devono essere risolte (anteprima) * * Raccomandazione](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Viene visualizzata la visualizzazione dettagliata di questa raccomandazione.

    [![visualizzazione dettagliata per la * * vulnerabilità nei database SQL nelle macchine virtuali deve essere risolta (anteprima) * * Raccomandazione](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Per eseguire il drill-down per altri dettagli:

    * Per una panoramica delle risorse analizzate (database) e l'elenco dei controlli di sicurezza testati, fare clic sul server di interesse.
    [![vulnerabilità raggruppate in SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Per una panoramica delle vulnerabilità raggruppate in base a un database SQL specifico, fare clic sul database di interesse.
    [![vulnerabilità raggruppate in SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    In ogni visualizzazione, i controlli di sicurezza sono ordinati in base alla **gravità**. Fare clic su un controllo di sicurezza specifico per visualizzare un riquadro dei dettagli con una **Descrizione**, **come risolverlo e altre** informazioni correlate, ad esempio **Impact** o **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avvisi di Advanced Threat Protection per SQL Server in macchine virtuali di Azure
Gli avvisi vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o exploit di SQL Server. Questi eventi possono attivare gli avvisi seguenti:

### <a name="anomalous-access-pattern-alerts-preview"></a>Avvisi del criterio di accesso anomalo (anteprima)

* **Accesso da una posizione insolita:** Questo avviso viene generato quando viene apportata una modifica al modello di accesso a SQL Server, in cui un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. Possibili cause:
    * Un utente malintenzionato o un utilizzo dannoso precedente ha eseguito l'accesso al SQL Server.
    * Un utente legittimo ha eseguito l'accesso al SQL Server da una nuova posizione.
* **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. Possibili cause:
    * Un utente malintenzionato sta tentando di violare SQL usando gli strumenti di attacco più comuni.
    * Test di penetrazione legittimi in azione.
* **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). Possibili cause:
    * Un utente malintenzionato o un utilizzo dannoso precedente ha eseguito l'accesso al SQL Server. 
    * Un utente legittimo ha eseguito l'accesso al SQL Server da con una nuova entità.
* **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. Possibili cause:
    * Un utente malintenzionato sta tentando di violare il SQL usando forza bruta.
    * Test di penetrazione legittimi in azione.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenziali attacchi intrusivi nel codice SQL (supportati nella SQL Server 2019)

* **Vulnerabilità a SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Possibili cause:
    * Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
    * Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
* **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Comando unsafe (supportato in SQL Server 2019)

* **Azione potenzialmente non sicura**: questo avviso viene generato quando viene eseguito un comando con privilegi elevati e potenzialmente non sicuri. Possibili cause:
    * Il comando che è consigliabile disabilitare per una migliore postura di sicurezza è abilitato.
    * Un utente malintenzionato che tenta di sfruttare i privilegi di accesso SQL o di escalation.   


## <a name="explore-and-investigate-security-alerts"></a>Esplorare ed esaminare gli avvisi di sicurezza

Gli avvisi di sicurezza dei dati sono disponibili nella pagina degli avvisi del Centro sicurezza, nella scheda sicurezza della risorsa o tramite il collegamento diretto nei messaggi di posta elettronica di avviso.

1. Per visualizzare gli avvisi:

    * Nel centro sicurezza fare clic su **avvisi di sicurezza** dalla barra laterale e selezionare un avviso.
    * Nell'ambito della risorsa: aprire la pagina delle risorse pertinente e, nella barra laterale, fare clic su **sicurezza**. 

1. Gli avvisi sono progettati per essere indipendenti, con procedure dettagliate per la correzione e informazioni di analisi in ognuna di esse. È possibile approfondire ulteriormente l'uso di altre funzionalità del Centro sicurezza di Azure e di Azure Sentinel per una visualizzazione più ampia:

    * Abilitare la funzionalità di controllo SQL Server per ulteriori indagini. Se si è un utente di Sentinel di Azure, è possibile caricare i log di controllo SQL dagli eventi del registro di sicurezza di Windows in Sentinel e usufruire di un'esperienza di analisi avanzata.
    * Per migliorare il comportamento di sicurezza, usare le raccomandazioni del Centro sicurezza per il computer host indicato in ogni avviso. Questa operazione ridurrà i rischi di attacchi futuri. 


## <a name="next-steps"></a>Passaggi successivi

Per materiale correlato, vedere l'articolo seguente:

- [Come correggere le raccomandazioni](security-center-remediate-recommendations.md)