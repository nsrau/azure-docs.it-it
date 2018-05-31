---
title: Rilevamento minacce - Database SQL di Azure | Documentazione Microsoft
description: La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364149"
---
# <a name="azure-sql-database-threat-detection"></a>Rilevamento delle minacce per il database SQL di Azure

Il rilevamento delle minacce per il database SQL di Azure rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Rilevamento delle minacce fa parte dell'offerta [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (Protezione avanzata dalle minacce SQL) (ATP), che è un pacchetto unificato per le funzionalità avanzate di sicurezza SQL. È possibile accedere e gestire Rilevamento delle minacce tramite il portale centrale ATP SQL.

## <a name="what-is-threat-detection"></a>Introduzione alla funzionalità di rilevamento delle minacce

Rilevamento minacce di SQL offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Il Rilevamento minacce SLQ integra gli avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che comprende i dettagli delle attività sospette e raccomandano azioni per individuare e ridurre la minaccia. Il rilevamento delle minacce di SQL rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati. 

Per un'esperienza di analisi completa, è consigliabile abilitare l'[azione di controllo del database SQL](sql-database-auditing.md), che scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurare il rilevamento delle minacce per il database tramite il portale di Azure
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del server del database SQL di Azure che si vuole proteggere. Nelle impostazioni di sicurezza, selezionare **Protezione avanzata dalle minacce**.
3. Nella pagina di configurazione **Protezione avanzata dalle minacce**:

   - Abilita Advanced Threat Protection nel server.
   - In **Impostazioni di rilevamento delle minacce**nella casella di testo **Invia avvisi a**, viene fornito l'elenco dei messaggi di posta elettronica per ricevere avvisi di sicurezza al rilevamento delle attività anomale del database.
  
   ![Impostare il rilevamento delle minacce](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurare il rilevamento delle minacce tramite PowerShell

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività di database anomale quando viene rilevato un evento sospetto

Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server, il nome dell'applicazione e l'ora dell'evento. Il messaggio di posta elettronica fornirà anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.

![Report Anomalie dell'attività](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Fare clic sul collegamento **Visualizza gli avvisi di SQL recenti** nel messaggio di posta elettronica per avviare il portale di Azure e visualizzare la pagina degli avvisi del Centro sicurezza di Azure, con una panoramica delle minacce attive rilevate nel database di SQL.

   ![Minacce dell'attività](./media/sql-database-threat-detection/active_threats.png)

2. Fare clic su uno specifico avviso per visualizzare altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

   Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza più comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database. Negli avvisi di attacchi SQL injection, i dettagli includono l'istruzione SQL vulnerabile che ha subito un exploit.

   ![Avviso specifico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Esplorare gli avvisi di rilevamento delle minacce per il database tramite il portale di Azure

Rilevamento minacce del database SQL integra i suoi avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). Un riquadro sul rilevamento minacce live di SQL all'interno dei pannelli del database e di SQL ATP nel portale di Azure tiene traccia dello stato delle minacce attive.

Fare clic su **Avviso rilevamento minacce**per avviare la pagina degli avvisi del Centro sicurezza di Azure e ottenere una panoramica delle minacce SQL attive rilevate nel database.

   ![Avviso di rilevamento minacce](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Avviso di rilevamento minacce2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Avvisi di rRilevamento delle minacce per il database SQL di Azure 
Il servizio di rilevamento delle minacce per il database SQL di Azure rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare i tipi di avvisi seguenti:
- **Vulnerabilità agli attacchi SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. Ciò potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Ci sono due possibili motivi per la generazione di un'istruzione non corretta:
   - Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
   - Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
- **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.
- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un data center di Azure insolito**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da un data center di Azure insolito, rilevato nel server di recente. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione in Azure, Power BI, editor di query SQL di Azure). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (Protezione avanzata dalle minacce SQL). 
* Altre informazioni sul [controllo del database SQL di Azure](sql-database-auditing.md)
* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, visitare la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).  
