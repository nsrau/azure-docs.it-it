---
title: Advanced Threat Protection - Database SQL di Azure | Microsoft Docs
description: Advanced Threat Protection rileva attività di database anomale che indicano potenziali minacce alla sicurezza in Database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797065"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection per il database SQL di Azure

Advanced Threat Protection per [Database SQL di Azure](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Advanced Threat Protection fa parte del [protezione dati avanzata](sql-database-advanced-data-security.md) (ADS) offerta, che è un pacchetto unificato per la funzionalità di sicurezza avanzate SQL. Advanced Threat Protection possono essere accessibili e gestite tramite il portale di annunci pubblicitari SQL centrale.

> [!NOTE]
> Questo argomento è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection

 Advanced Threat Protection offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle minacce potenziali appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection si integra gli avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che includono i dettagli delle attività sospette e consigliano azioni analizzare e ridurre la minaccia. Advanced Threat Protection rende più semplice affrontare le minacce potenziali al database senza dover essere esperti della sicurezza o gestire sistemi di monitoraggio di sicurezza avanzate.

Per un'esperienza di analisi completa, è consigliabile abilitare l'[azione di controllo del database SQL](sql-database-auditing.md), che scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure.  

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection

Advanced Threat Protection per il Database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare gli avvisi seguenti:

- **Vulnerabilità agli attacchi SQL injection**: questo avviso viene attivato quando un'applicazione genera un'istruzione SQL non corretta nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Ci sono due possibili motivi per la generazione di un'istruzione non corretta:

  - Un difetto nel codice dell'applicazione che crea l'istruzione SQL non corretta
  - Codice dell'applicazione o stored procedure che non correggono l'input utente quando viene creata l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection
- **Potenziale attacco SQL injection**: questo avviso viene attivato quando si verifica un exploit attivo che sfrutta una vulnerabilità identificata dell'applicazione agli attacchi SQL injection. Ciò significa che l'utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione o stored procedure vulnerabili.
- **Accesso da una posizione insolita**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un data center di Azure insolito**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server da un data center di Azure insolito, rilevato nel server di recente. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione in Azure, Power BI, editor di query SQL di Azure). In altri casi, l'avviso rileva un'azione dannosa proveniente da una risorsa o un servizio di Azure (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'entità di sicurezza non familiare**: questo avviso viene attivato quando il modello di accesso a SQL Server cambia, quando un utente ha effettuato l'accesso a SQL Server usando un'entità di sicurezza insolita (utente SQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).
- **Accesso da un'applicazione potenzialmente dannosa**: questo avviso viene attivato quando un'applicazione potenzialmente dannosa viene usata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.
- **Attacco di forza bruta a credenziali SQL**: questo avviso viene attivato quando si verifica un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività di database anomale quando viene rilevato un evento sospetto

Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server, il nome dell'applicazione e l'ora dell'evento. Il messaggio di posta elettronica fornirà anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.

![Report Anomalie dell'attività](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Fare clic sul collegamento **Visualizza gli avvisi di SQL recenti** nel messaggio di posta elettronica per avviare il portale di Azure e visualizzare la pagina degli avvisi del Centro sicurezza di Azure, con una panoramica delle minacce attive rilevate nel database di SQL.

   ![Minacce di attività](./media/sql-database-threat-detection/active_threats.png)

2. Fare clic su uno specifico avviso per visualizzare altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

   Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza più comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database. Negli avvisi di attacchi SQL injection, i dettagli includono l'istruzione SQL vulnerabile che ha subito un exploit.

   ![Avviso specifico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Esplorare gli avvisi di Advanced Threat Protection per il database nel portale di Azure

Advanced Threat Protection integra i suoi avvisi con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). I riquadri animati SQL Advanced Threat Protection nel database e nei pannelli di annunci di SQL nel portale di Azure di tenere traccia dello stato delle minacce attive.

Fare clic su **avviso di Advanced Threat Protection** per avviare il Centro sicurezza di Azure gli avvisi di pagina e ottenere una panoramica delle minacce SQL attive rilevate nel database o data warehouse.

   ![Avviso di protezione dalle minacce avanzata](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [Advanced Threat Protection nel database singoli e pool](sql-database-threat-detection.md).
- Altre informazioni sulle [Advanced Threat Protection in istanza gestita](sql-database-managed-instance-threat-detection.md).
- Altre informazioni sulle [protezione dati avanzata](sql-database-advanced-data-security.md).
- Altre informazioni sul [controllo del database SQL di Azure](sql-database-auditing.md)
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Per altre informazioni sui prezzi, visitare la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
