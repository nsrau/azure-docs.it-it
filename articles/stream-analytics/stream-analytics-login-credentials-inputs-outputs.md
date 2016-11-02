<properties 
    pageTitle="Analisi di flusso: Ruotare le credenziali di accesso per input e output | Microsoft Azure" 
    description="Informazioni su come aggiornare le credenziali di input e output di Analisi dei flussi."
    keywords="credenziali di accesso"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Ruotare le credenziali di accesso per input e output nei processi di analisi di flusso

##<a name="abstract"></a>Sunto
Analisi dei flussi di Azure, al momento, non consente di sostituire le credenziali su input/output durante l’esecuzione del processo.

Nonostante Analisi di flusso di Azure supporti la ripresa di un processo dall’ultimo output, abbiamo voluto condividere l’intero processo per ridurre l’intervallo fra l’arresto e l’avvio del processo e la rotazione delle credenziali di accesso.

##<a name="part-1---prepare-the-new-set-of-credentials:"></a>Parte 1 - Preparare il nuovo set di credenziali:
Questa parte è applicabile ai seguenti input/output:

* Archiviazione BLOB
* Hub eventi
* Database SQL
* Archiviazione tabelle

Per altri input/output, andare alla Parte 2.

###<a name="blob-storage/table-storage"></a>Archiviazione BLOB/Archiviazione tabelle
1.  Andare all'estensione di Archiviazione nel portale di gestione di Azure:   
![graphic1][graphic1]
2.  Individuare l’archiviazione usata dal processo e accedervi:   
![graphic2][graphic2]
3.  Fare clic sul comando Gestisci chiavi di accesso:   
![graphic3][graphic3]
4.  Tra la chiave di accesso primaria e la chiave di accesso secondaria, **selezionare quella non usata dal processo**.
5.  Premere l’opzione di rigenerazione:   
![graphic4][graphic4]
6.  Copiare la chiave appena generata:   
![graphic5][graphic5]
7.  Continuare con la Parte 2.

###<a name="event-hubs"></a>Hub eventi
1.  Andare all'estensione del bus di servizio sul portale di gestione di Azure:   
![graphic6][graphic6]
2.  Individuare lo spazio dei nomi del bus di servizio usato dal processo e accedervi:   
![graphic7][graphic7]
3.  Se il processo usa criteri di accesso condivisi sullo spazio dei nomi del bus di servizio, saltare al passaggio 6  
4.  Andare alla scheda Hub eventi:   
![graphic8][graphic8]
5.  Individuare l’hub eventi usato dal processo e accedervi:   
![graphic9][graphic9]
6.  Andare alla scheda Configura:   
![graphic10][graphic10]
7.  Nell’elenco a discesa Nome criteri, individuare i criteri di accesso condivisi usati dal processo:   
![graphic11][graphic11]
8.  Tra la chiave primaria e la chiave secondaria, **selezionare quella non usata dal processo**.  
9.  Premere l’opzione di rigenerazione:   
![graphic12][graphic12]
10. Copiare la chiave appena generata:   
![graphic13][graphic13]
11. Continuare con la Parte 2.  

###<a name="sql-database"></a>Database SQL

>[AZURE.NOTE] Nota: sarà necessario connettersi al servizio Database SQL. Verrà mostrato come eseguire l'operazione usando lo strumento di gestione sul portale di gestione di Azure. Tuttavia, è anche possibile scegliere uno strumento sul lato client come SQL Server Management Studio.

1.  Andare all'estensione del database SQL sul portale di gestione di Azure:   
![graphic14][graphic14]
2.  Individuare il database SQL usato dal processo e **fare clic sul collegamento al server** sulla stessa riga:  
![graphic15][graphic15]
3.  Fare clic sul comando Gestisci:   
![graphic16][graphic16]
4.  Digitare master del database:   
![graphic17][graphic17]
5.  Immettere il nome utente, la password e fare clic su Accedi:   
![graphic18][graphic18]
6.  Fare clic su Nuova query:   
![graphic19][graphic19]
7.  Immettere la query seguente sostituendo <login_name> con il nome utente e sostituendo <enterStrongPasswordHere> con la nuova password:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Fare clic su Esegui:   
![graphic20][graphic20]
9.  Tornare al passaggio 2 e, questa volta, fare clic sul database:   
![graphic21][graphic21]
10. Fare clic sul comando Gestisci:   
![graphic22][graphic22]
11. Digitare il nome utente, la password e fare clic su Accedi:   
![graphic23][graphic23]
12. Fare clic su Nuova query:   
![graphic24][graphic24]
13. Immettere la query seguente sostituendo <user_name> con il nome con cui identificare l'accesso nel contesto di questo database (è possibile indicare lo stesso valore assegnato per <login_name>, ad esempio) e sostituendo <login_name> con il nuovo nome utente:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Fare clic su Esegui:   
![graphic25][graphic25]
15. A questo punto è necessario assegnare al nuovo utente gli stessi ruoli e privilegi che aveva l'utente originale.
16. Continuare con la Parte 2.

##<a name="part-2:-stopping-the-stream-analytics-job"></a>Parte 2: arresto del processo di Analisi dei flussi
1.  Andare all'estensione di Analisi di flusso sul portale di gestione di Azure:   
![graphic26][graphic26]
2.  Individuare il processo e accedervi:   
![graphic27][graphic27]
3.  Andare nella scheda Input o nella scheda Output a seconda se si stanno ruotando le credenziali su un Input o su un Output.  
![graphic28][graphic28]
4.  Fare clic sul comando Arresta e confermare l’arresto del processo:   
![graphic29][graphic29] Attendere l'arresto del processo.
5.  Individuare l’input/output su cui ruotare le credenziali e accedervi:   
![graphic30][graphic30]
6.  Andare alla Parte 3.

##<a name="part-3:-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: modifica delle credenziali sul processo di Analisi dei flussi

###<a name="blob-storage/table-storage"></a>Archiviazione BLOB/Archiviazione tabelle
1.  Individuare il campo Chiave dell'account di archiviazione e incollarvi la chiave appena generata:   
![graphic31][graphic31]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:   
![graphic32][graphic32]
3.  Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.
4.  Andare alla Parte 4.

###<a name="event-hubs"></a>Hub eventi
1.  Individuare il campo Chiave di criterio dell’hub eventi e incollarvi la chiave appena generata:   
![graphic33][graphic33]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:   
![graphic34][graphic34]
3.  Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.
4.  Andare alla Parte 4.

###<a name="power-bi"></a>Power BI
1.  Fare clic su Rinnova autorizzazione:  
* ![graphic35][graphic35]
* Si otterrà la conferma seguente:  
* ![graphic36][graphic36]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:   
![graphic37][graphic37]
3.  Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.
4.  Andare alla Parte 4.

###<a name="sql-database"></a>Database SQL
1.  Individuare i campi Nome utente e Password e incollarvi il set di credenziali appena create:   
![graphic38][graphic38]
2.  Fare clic sul comando Salva e confermare il salvataggio delle modifiche:   
![graphic39][graphic39]
3.  Al salvataggio delle modifiche, verrà automaticamente avviato un test di connessione. Assicurarsi che abbia esito positivo.  
4.  Andare alla Parte 4.

##<a name="part-4:-starting-your-job-from-last-stopped-time"></a>Parte 4: avvio del processo dall’ora dell’ultimo arresto
1.  Uscire da Input/Output:   
![graphic40][graphic40]
2.  Fare clic sul comando di avvio:   
![graphic41][graphic41]
3.  Selezionare l’ora dell’ultimo arresto e fare clic su OK:   
 ![graphic42][graphic42]
4.  Andare alla Parte 5.  

##<a name="part-5:-removing-the-old-set-of-credentials"></a>Parte 5: rimozione del set di credenziali precedente
Questa parte è applicabile ai seguenti input/output:
* Archiviazione BLOB
* Hub eventi
* Database SQL
* Archiviazione tabelle

###<a name="blob-storage/table-storage"></a>Archiviazione BLOB/Archiviazione tabelle
Ripetere la Parte 1 per la chiave di accesso usata in precedenza dal processo per rinnovare la chiave di accesso ora inutilizzata.

###<a name="event-hubs"></a>Hub eventi
Ripetere la Parte 1 per la chiave usata in precedenza dal processo per rinnovare la chiave ora inutilizzata.

###<a name="sql-database"></a>Database SQL
1.  Tornare alla finestra delle query dalla Parte 1 Passaggio 7 e immettere la query seguente, sostituendo <previous_login_name> con il nome utente usato in precedenza dal processo:  
`DROP LOGIN <previous_login_name>`  
2.  Fare clic su Esegui:   
    ![graphic43][graphic43]  

Si dovrebbe ottenere la conferma seguente: 

    Command(s) completed successfully.

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 



<!--HONumber=Oct16_HO2-->


