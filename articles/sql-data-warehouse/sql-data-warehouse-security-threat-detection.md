---
title: Introduzione al Rilevamento delle minacce in SQL Data Warehouse
description: "Attività iniziali per il rilevamento delle minacce"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="get-started-with-threat-detection"></a>Introduzione al rilevamento delle minacce
> [!div class="op_single_selector"]
> * [Controllo](sql-data-warehouse-auditing-overview.md)
> * [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Overview
La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Questa funzionalità è in anteprima ed è supportata per SQL Data Warehouse.

Il rilevamento delle minacce offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti possono esaminare gli eventi sospetti usando il [servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) per determinare se sono il risultato di un tentativo di accesso, violazione o exploit dei dati nel data warehouse.
Il rilevamento delle minacce rende più semplice affrontare le minacce potenziali al data warehouse, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.

Ad esempio, la funzionalità di rilevamento delle minacce individua determinate attività anomale nel database che indicano potenziali tentativi di attacco SQL injection. L'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori di attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, per violare o modificare i dati nel database.

## <a name="set-up-threat-detection-for-your-database"></a>Configurare il rilevamento delle minacce per il database
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione dell’SQL Data Warehouse che si vuole monitorare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce**.
   
    ![Riquadro di spostamento][1]
3. Nel pannello di configurazione **Controllo e rilevamento minacce** selezionare **ON** per attivare il controllo, che mostrerà le impostazioni di rilevamento delle minacce.
   
    ![Riquadro di spostamento][2]
4. Impostare il rilevamento delle minacce su **SÌ** .
5. Configurare l'elenco di indirizzi di posta elettronica che riceveranno avvisi di sicurezza in caso di rilevamento di attività anomale di data warehouse.
6. Fare clic su **Salva** nel pannello di configurazione **Controllo e rilevamento minacce** per salvare i criteri di controllo e rilevamento delle minacce nuovi o aggiornati.
   
    ![Riquadro di spostamento][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività anomale di data warehouse quando viene rilevato un evento sospetto
1. Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. <br/>
   Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Verranno anche fornite informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.<br/>
   
    ![Riquadro di spostamento][4]
2. Nel messaggio di posta elettronica fare clic sul collegamento relativo al **log di controllo SQL di Azure** che avvierà il portale di Azure classico visualizzando i record di controllo pertinenti intorno all'ora dell'evento sospetto.
   
    ![Riquadro di spostamento][5]
3. Fare clic sui record di controllo per visualizzare altri dettagli sulle attività di database sospette, come l'istruzione SQL, il motivo dell'errore e l'indirizzo IP del client.
   
    ![Riquadro di spostamento][6]
4. Nel pannello Auditing Records (Controllo record) fare clic su  **Apri in Excel** per aprire un modello Excel preconfigurato per importare ed eseguire un'analisi più approfondita del log di controllo sull'orario in cui si è verificato l'evento sospetto.<br/>
   **Nota**: in Excel 2010 o versione successiva sono richiesti Power Query e l'impostazione **Combinazione rapida**.
   
    ![Riquadro di spostamento][7]
5. Per configurare l'impostazione **Combinazione rapida**, nella scheda della barra multifunzione **POWER QUERY** selezionare **Opzioni** per visualizzare la finestra di dialogo Opzioni. Selezionare la sezione Privacy e scegliere la seconda opzione - "Ignora i livelli di privacy per un potenziale miglioramento delle prestazioni":
   
    ![Riquadro di spostamento][8]
6. Per caricare i log di controllo SQL, assicurarsi che i parametri nella scheda Impostazioni siano configurati correttamente e quindi selezionare "Dati" sulla barra multifunzione e fare clic sul pulsante "Aggiorna tutto".
   
    ![Riquadro di spostamento][9]
7. I risultati vengono visualizzati nel foglio dei **log di controllo SQL** che consente di eseguire un'analisi più approfondita delle attività anomale rilevate e di ridurre l'impatto dell'evento di sicurezza nell'applicazione.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png

