---
title: 'Analisi di flusso: Ruotare le credenziali di accesso per input e output | Microsoft Docs'
description: Informazioni su come aggiornare le credenziali di input e output di Analisi di flusso.
keywords: credenziali di accesso
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/11/2018
ms.author: sngun
ms.openlocfilehash: c1aded8fefc7b56acd2e9ff36bb2c9641665db76
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Ruotare le credenziali di accesso per input e output in un processo di Analisi di flusso

Ogni volta che si rigenerano le credenziali per l'input o l'output di un processo di Analisi di flusso, è necessario aggiornare il processo con nuove credenziali. Prima di eseguire questa operazione è necessario arrestare il processo. Non è possibile sostituire le credenziali mentre il processo è in esecuzione. Per ridurre l'intervallo tra l'arresto e riavvio del processo, Analisi di flusso consente di riprendere l'esecuzione di un processo dall'ultimo output. Questo argomento illustra come ruotare le credenziali di accesso e riavviare il processo con nuove credenziali.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Rigenerare le credenziali e aggiornare il processo con le nuove credenziali 

Questa sezione illustra la procedura da seguire per rigenerare le credenziali per Archiviazione BLOB, Hub eventi, il database SQL e Archiviazione tabelle. 

### <a name="blob-storagetable-storage"></a>Archiviazione BLOB/Archiviazione tabelle
1. Accedere al portale di Azure e passare all'account di archiviazione usato come input/output per il processo di Analisi di flusso.    
2. Dalla sezione Impostazioni aprire **Chiavi di accesso**. Delle due chiavi predefinite (key1, key2), selezionare quella che non viene usata dal processo e rigenerarla:  
   ![Rigenerare le chiavi per l'account di archiviazione](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Copiare la chiave appena generata.    
4. Dal portale di Azure passare al processo di Analisi di flusso, selezionare **Arresta** e attendere l'arresto del processo.    
5. Individuare l'input o l'output di Archiviazione BLOB o tabelle per cui aggiornare le credenziali.    
6. Individuare il campo **Chiave dell'account di archiviazione**, incollarvi la chiave appena generata e quindi fare clic su **Salva**.    
7. Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. È possibile visualizzarlo dalla scheda delle notifiche. Sono presenti due notifiche che corrispondono rispettivamente al salvataggio dell'aggiornamento e al test della connessione:  
   ![Notifiche dopo la modifica della chiave](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Passare alla sezione [Avviare il processo dall'ultimo arresto] (#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Hub eventi

1. Accedere al portale di Azure e passare all'hub eventi usato come input/output per il processo di Analisi di flusso.    
2. Dalla sezione Impostazioni aprire **Criteri di accesso condiviso** e selezionare i criteri di accesso richiesti. Delle due chiavi configurate, **Chiave primaria** e **Chiave secondaria**, selezionare quella non usata dal processo e rigenerarla:  
   ![Rigenerare le chiavi per Hub eventi](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Copiare la chiave appena generata.    
4. Dal portale di Azure passare al processo di Analisi di flusso, selezionare **Arresta** e attendere l'arresto del processo.    
5. Individuare l'input o l'output di Hub eventi per cui aggiornare le credenziali.    
6. Individuare il campo **Chiave criteri hub eventi**, incollarvi la chiave appena generata e quindi fare clic su **Salva**.    
7. Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.    
8. Passare alla sezione [Avviare il processo dall'ultimo arresto](#start-your-job-from-the-last-stopped-time).

### <a name="sql-database"></a>Database SQL

Se è necessario connettersi al database SQL per aggiornare le credenziali di accesso di un utente esistente, è possibile eseguire questa operazione tramite il portale di Azure o con uno strumento sul lato client, ad esempio SQL Server Management Studio. Questa sezione illustra la procedura da seguire per aggiornare le credenziali tramite il portale di Azure.

1. Accedere al portale di Azure e passare al database SQL usato come input/output per il processo di Analisi di flusso.    
2. Da **Esplora dati** connettersi al database e selezionare il tipo di autorizzazione come **Autenticazione di SQL Server**, quindi digitare i dati nei campi **Account di accesso** e **Password** e infine fare clic su **OK**.  
   ![Rigenerare le credenziali per il database SQL](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Nella scheda della query modificare la password per uno degli utenti eseguendo la query seguente, facendo attenzione a sostituire `<user_name>` con il nome utente e `<new_password>` con la nuova password:  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Prendere nota della nuova password.    
5. Dal portale di Azure passare al processo di Analisi di flusso, selezionare **Arresta** e attendere l'arresto del processo.    
6. Individuare l'output del database SQL per cui ruotare le credenziali. Aggiornare la password e salvare le modifiche.    
7. Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.    
8. Passare alla sezione [Avviare il processo dall'ultimo arresto](#start-your-job-from-the-last-stopped-time).

### <a name="power-bi"></a>Power BI
1. Accedere al portale di Azure, passare al processo di Analisi di flusso, selezionare **Arresta** e attendere l'arresto del processo.    
2. Individuare l'output di Power BI per cui rinnovare le credenziali, fare clic su **Rinnova autorizzazione** (dovrebbe essere visualizzato un messaggio di conferma) e quindi fare clic su **Salva** per salvare le modifiche.    
3. Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.    
4. Passare alla sezione [Avviare il processo dall'ultimo arresto](#start-your-job-from-the-last-stopped-time).

## <a name="start-your-job-from-the-last-stopped-time"></a>Avviare il processo dall'ultimo arresto

1. Passare al riquadro **Panoramica** del processo e fare clic su **Avvia** per avviare il processo.    
2. Selezionare **Ultimo arresto** e fare clic su **Avvia**. Si noti che l'opzione "Ultimo arresto" viene visualizzata solo se il processo è stato eseguito in precedenza ed è stato generato dell'output. Il processo viene riavviato in base al momento in cui è stato restituito l'ultimo valore di output.
   ![Avviare il processo](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
