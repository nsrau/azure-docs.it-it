---
title: Advanced Threat Protection
description: Advanced Threat Protection detects anomalous database activities indicating potential security threats in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124769"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection per il database SQL di Azure

Advanced Threat Protection for [Azure SQL Database](sql-database-technical-overview.md) and Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Advanced Threat Protection fa parte dell'offerta [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), un pacchetto unificato per le funzionalità di sicurezza SQL avanzate. È possibile accedere e gestire Advanced Threat Protection tramite il portale centrale Sicurezza dei dati avanzata di SQL.

> [!NOTE]
> Questo argomento si applica al server SQL di Azure e sia al database SQL che alla protezione di Azure creati nel server SQL di Azure.This topic applies to Azure SQL server, and both SQL Database and Azure Synapse that are created on the Azure SQL server. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che allo synapsi di Azure.For simplicity, SQL Database is used when referring to both SQL Database and Azure Synapse.

## <a name="what-is-advanced-threat-protection"></a>Che cos'è Advanced Threat Protection

 Advanced Threat Protection offre un nuovo livello di sicurezza, che consente ai clienti di rilevare e rispondere alle potenziali minacce man mano che si verificano fornendo avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection integra avvisi con il Centro sicurezza di [Azure,](https://azure.microsoft.com/services/security-center/)che includono dettagli sulle attività sospette e consigliano azioni su come analizzare e ridurre la minaccia. Advanced Threat Protection semplifica l'affrontare potenziali minacce al database senza la necessità di essere esperti di sicurezza o di gestire sistemi di monitoraggio della sicurezza avanzati.

Per un'esperienza di analisi completa, è consigliabile abilitare l'[azione di controllo del database SQL](sql-database-auditing.md), che scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure.  

## <a name="advanced-threat-protection-alerts"></a>Avvisi di Advanced Threat Protection

Advanced Threat Protection per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database e può generare gli avvisi seguenti:Advanced Threat Protection for Azure SQL Database detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases and it can trigger the following alerts:

- **Vulnerabilità all'inserimento SQL:** questo avviso viene attivato quando un'applicazione genera un'istruzione SQL errata nel database. L'avviso potrebbe indicare una possibile vulnerabilità ad attacchi SQL injection. Ci sono due possibili motivi per la generazione di un'istruzione non corretta:

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

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Esplorare gli avvisi di Advanced Threat Protection per il database nel portale di AzureExplore Advanced Threat Protection alerts for your database in the Azure portal

Advanced Threat Protection integra gli avvisi con il Centro sicurezza di [Azure.](https://azure.microsoft.com/services/security-center/) I riquadri di Live SQL Advanced Threat Protection all'interno del database e i pannelli ADS SQL nel portale di Azure tengono traccia dello stato delle minacce attive.

Fare clic su **Avviso di Advanced Threat Protection** per avviare la pagina Avvisi del Centro sicurezza di Azure e ottenere una panoramica delle minacce SQL attive rilevate nel database.

   ![Avviso di Protezione avanzata dalle minacce](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Avviso di protezione avanzata dalle minacce2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [Advanced Threat Protection in database singoli e in pool](sql-database-threat-detection.md).
- Ulteriori informazioni su [Advanced Threat Protection nell'istanza gestita](sql-database-managed-instance-threat-detection.md).
- Ulteriori informazioni sulla [sicurezza avanzata dei dati](sql-database-advanced-data-security.md).
- Altre informazioni sul controllo del database SQL di [AzureLearn more](sql-database-auditing.md) about Azure SQL Database auditing
- Altre informazioni sul Centro sicurezza di [AzureLearn](https://docs.microsoft.com/azure/security-center/security-center-intro) more about Azure security center
- Per altre informazioni sui prezzi, vedere la [pagina dei prezzi del database SQLFor](https://azure.microsoft.com/pricing/details/sql-database/) more information on pricing, see the SQL Database pricing page  
