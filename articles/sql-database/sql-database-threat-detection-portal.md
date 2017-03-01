---
title: 'Portale di Azure: Configurare il rilevamento delle minacce per il database SQL | Documentazione Microsoft'
description: Configurare e gestire il rilevamento di una minaccia nel portale di Azure
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 0222847321b82c9572e361cc5e578b779d859792
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-azure-sql-database-threat-detection-in-the-azure-portal"></a>Configurare e gestire il rilevamento di una minaccia del database SQL di Azure nel portale di Azure

Questo argomento illustra come configurare il rilevamento minacce ed esplorare le attività anomale del database. Per una panoramica del rilevamento minacce del database SQL di Azure, vedere [Threat detection overview](sql-database-threat-detection.md) (Panoramica del rilevamento minacce).

## <a name="set-up-threat-detection-for-your-database"></a>Configurare il rilevamento delle minacce per il database
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione del database SQL che si vuole monitorare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce**.
   
    ![Riquadro di spostamento][1]
3. Nel pannello di configurazione **Controllo e rilevamento minacce** impostare il controllo su **SÌ**, il che consentirà di visualizzare le impostazioni di rilevamento minacce.
   
    ![Riquadro di spostamento][2]
4. Impostare il rilevamento delle minacce su **SÌ**.
5. Configurare l'elenco di indirizzi di posta elettronica che riceveranno avvisi di sicurezza in caso di rilevamento di attività di database anomale.
6. Fare clic su **Salva** nel pannello di configurazione **Controllo e rilevamento minacce** per salvare il controllo nuovo o aggiornato e i criteri di rilevamento delle minacce.
   
    ![Riquadro di spostamento][3]

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività di database anomale quando viene rilevato un evento sospetto
1. Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. <br/>
   Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Verranno anche fornite informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.<br/>
   
    ![Riquadro di spostamento][4]
2. Nel messaggio di posta elettronica fare clic sul collegamento relativo al **log di controllo SQL di Azure** che avvierà il portale di Azure  visualizzando i record di controllo pertinenti intorno all'ora dell'evento sospetto.
   
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

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica del rilevamento minacce del database SQL, vedere [Threat detection](sql-database-threat-detection.md) (Rilevamento minacce).
* Per una panoramica del controllo del database SQL, vedere l'articolo sul [controllo del database](sql-database-auditing.md).


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png

