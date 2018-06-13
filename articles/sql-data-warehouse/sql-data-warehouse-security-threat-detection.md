---
title: Rilevamento delle minacce - Azure SQL Data Warehouse | Microsoft Docs
description: Configurare la funzionalità di rilevamento delle minacce ed esplorare eventi sospetti in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 8dc1ef0432536c6bfd4fe069406cd057ca069ea2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523898"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Funzionalità di rilevamento delle minacce in Azure SQL Data Warehouse
Configurare la funzionalità di rilevamento delle minacce ed esplorare eventi sospetti in Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>Introduzione alla funzionalità di rilevamento delle minacce
La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. 

Il rilevamento delle minacce offre un nuovo livello di protezione, che consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. Gli utenti possono esaminare gli eventi sospetti usando il [servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) per determinare se sono il risultato di un tentativo di accesso, violazione o exploit dei dati nel data warehouse.
Il rilevamento delle minacce rende più semplice affrontare le minacce potenziali al data warehouse, senza dover essere esperti della sicurezza o gestire sistemi di controllo di sicurezza avanzati.

Ad esempio, la funzionalità di rilevamento delle minacce individua determinate attività anomale nel database che indicano potenziali tentativi di attacco SQL injection. L'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori di attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, per violare o modificare i dati nel database.

## <a name="set-up-threat-detection-for-your-database"></a>Configurare il rilevamento delle minacce per il database
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione dell’SQL Data Warehouse che si vuole monitorare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce**.
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. Nel pannello di configurazione **Controllo e rilevamento minacce** selezionare **ON** per attivare il controllo, che mostrerà le impostazioni di rilevamento delle minacce.
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Impostare il rilevamento delle minacce su **SÌ** .
5. Configurare l'elenco di indirizzi di posta elettronica che riceveranno avvisi di sicurezza in caso di rilevamento di attività anomale di data warehouse.
6. Fare clic su **Salva** nel pannello di configurazione **Controllo e rilevamento minacce** per salvare i criteri di controllo e rilevamento delle minacce nuovi o aggiornati.
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Esaminare le attività anomale di data warehouse quando viene rilevato un evento sospetto
1. Si riceverà una notifica tramite posta elettronica al rilevamento di attività di database anomale. <br/>
   Il messaggio di posta elettronica fornirà informazioni sull'evento di sicurezza sospetto, inclusi la natura delle attività anomale, il nome del database, il nome del server e l'ora dell'evento. Verranno anche fornite informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia al database.<br/>
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. Nel messaggio di posta elettronica fare clic sul collegamento relativo al **log di controllo SQL di Azure** che avvierà il portale di Azure  visualizzando i record di controllo pertinenti intorno all'ora dell'evento sospetto.
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Fare clic sui record di controllo per visualizzare altri dettagli sulle attività di database sospette, come l'istruzione SQL, il motivo dell'errore e l'indirizzo IP del client.
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. Nel pannello Auditing Records (Controllo record) fare clic su  **Apri in Excel** per aprire un modello Excel preconfigurato per importare ed eseguire un'analisi più approfondita del log di controllo sull'orario in cui si è verificato l'evento sospetto.<br/>
   **Nota:** in Excel 2010 o versione successiva, Power Query e l'impostazione **Combinazione rapida** sono obbligatori
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Per configurare l'impostazione **Combinazione rapida**, nella scheda della barra multifunzione **POWER QUERY** selezionare **Opzioni** per visualizzare la finestra di dialogo Opzioni. Selezionare la sezione Privacy e scegliere la seconda opzione - "Ignora i livelli di privacy per un potenziale miglioramento delle prestazioni":
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Per caricare i log di controllo SQL, assicurarsi che i parametri nella scheda Impostazioni siano configurati correttamente e quindi selezionare "Dati" sulla barra multifunzione e fare clic sul pulsante "Aggiorna tutto".
   
    ![Riquadro di spostamento](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. I risultati vengono visualizzati nel foglio dei **log di controllo SQL** che consente di eseguire un'analisi più approfondita delle attività anomale rilevate e di ridurre l'impatto dell'evento di sicurezza nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sicurezza, vedere [Proteggere un data warehouse](sql-data-warehouse-overview-manage-security.md).
