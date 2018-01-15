---
title: Rilevamento minacce - Database SQL di Azure | Documentazione Microsoft
description: "La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: shaik
editor: v-romcal
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 06/19/2017
ms.author: ronmat
ms.openlocfilehash: 889f65a796aee20d7902964b8c47af46dd9149cb
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="sql-database-threat-detection"></a>Rilevamento delle minacce nel database SQL

Rilevamento minacce di SQL rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

## <a name="overview"></a>Panoramica

Rilevamento minacce di SQL offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale.  Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di accesso ai database anomali. Gli avvisi di Rilevamento minacce di SQL forniscono i dettagli delle attività sospette e consigliano azioni per analizzare e ridurre la minaccia. Gli utenti possono esaminare gli eventi sospetti tramite il [servizio di controllo del database SQL](sql-database-auditing.md) per determinare se sono il risultato di un tentativo di accesso, una violazione o un exploit dei dati del database. Il rilevamento delle minacce rende più semplice affrontare le minacce potenziali al database, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.

Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database.

Rilevamento minacce di SQL integra gli avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/en-us/services/security-center/). Ogni database SQL protetto verrà fatturato allo stesso prezzo del livello Standard del Centro sicurezza di Azure, al prezzo di $ 15 per nodo al mese, dove ogni server di database SQL protetto viene conteggiato come un nodo.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurare il rilevamento delle minacce per il database tramite il portale di Azure
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare alla pagina di configurazione del database SQL che si vuole monitorare. Nella pagina Impostazioni selezionare **Controllo e rilevamento minacce**. 
    ![Riquadro di spostamento][1]
3. Nella pagina di configurazione **Controllo e rilevamento minacce** impostare il controllo su **SÌ**, il che consentirà di visualizzare le impostazioni di rilevamento minacce.
  
    ![Riquadro di spostamento][2]
4. Impostare il rilevamento delle minacce su **SÌ** .
5. Configurare l'elenco di indirizzi di posta elettronica che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
6. Fare clic su **Salva** nella pagina di configurazione **Controllo e rilevamento minacce** per salvare il controllo nuovo o aggiornato e le impostazioni di rilevamento delle minacce.
       
    ![Riquadro di spostamento][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configurare il rilevamento delle minacce tramite PowerShell

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività di database anomale quando viene rilevato un evento sospetto
1. Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. <br/>
   Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server, il nome dell'applicazione e l'ora dell'evento. Il messaggio di posta elettronica fornirà anche informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.<br/>
     
    ![Riquadro di spostamento][4]
2. L'avviso di posta elettronica include un collegamento diretto al log di controllo SQL. Facendo clic su questo collegamento viene avviato il portale di Azure e si aprono i record di controllo SQL riferiti alla stessa ora dell'evento sospetto. Fare clic su un record di controllo per visualizzare altri dettagli sulle attività sospette del database, per trovare più facilmente le istruzioni SQL eseguite (chi ha eseguito l'accesso, cosa ha fatto e quando) e determinare se l'evento era legittimo o dannoso (ad esempio se sono state sfruttate vulnerabilità dell'applicazione all'SQL injection, un utente ha compromesso dati sensibili e così via).<br/>
   ![Riquadro di spostamento][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Esplorare gli avvisi di rilevamento delle minacce per il database tramite il portale di Azure

Rilevamento minacce del database SQL integra i suoi avvisi con il [Centro sicurezza di Azure](https://azure.microsoft.com/en-us/services/security-center/). Un riquadro sulla sicurezza live di SQL all'interno della pagina del database nel portale di Azure tiene traccia dello stato delle minacce attive. 

   ![Riquadro di spostamento][6]
   
1. Facendo clic sul riquadro della sicurezza di SQL si avvia la pagina degli avvisi del Centro sicurezza di Azure e viene fornita una panoramica delle minacce SQL attive rilevate nel database. 

  ![Riquadro di spostamento][7]

2. Facendo clic su uno specifico avviso vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

  ![Riquadro di spostamento][8]


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Rilevamento minacce, visitare il [blog di Azure](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Altre informazioni sul [controllo del database SQL di Azure](sql-database-auditing.md)
* Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Per altre informazioni sui prezzi, vedere la [pagina Prezzi di Database SQL](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* Per un esempio di script di PowerShell, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


