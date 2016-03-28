<properties
   pageTitle="Novità di Catalogo dati di Azure | Microsoft Azure"
   description="Panoramica delle nuove funzionalità nell'anteprima di Catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/14/2016"
   ms.author="maroche"/>

# Novità di Catalogo dati di Azure

Gli aggiornamenti su **Catalogo dati di Azure** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includeranno nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. Questa pagina illustra le nuove funzionalità destinate all'utente aggiunte al servizio **Catalogo dati di Azure**.

## Novità della versione della settimana del 11 marzo 2016

Nella settimana del 11 marzo 2016 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Un endpoint API REST consolidato per l'accesso a livello di codice alle funzionalità di ricerca e alle funzionalità di gestione degli asset di catalogo del servizio **Catalogo dati di Azure**. L'endpoint API di ricerca e l'endpoint API di catalogo esistenti verranno dichiarati obsoleti e interrotti il 21 marzo 2016. Non sono state apportate modifiche alla semantica dell'API. Verrà modificato solo l'URI degli endpoint. Per altre informazioni, vedere il riferimento dell'[API REST del Catalogo dati di Azure](https://msdn.microsoft.com/library/azure/mt267595.aspx). Per esempi di API, vedere [Esempi per sviluppatori del Catalogo dati di Azure](data-catalog-samples.md).

## Novità della versione della settimana del 19 febbraio 2016

Nella settimana del 19 febbraio 2016 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Una nuova esperienza di selezione dell'origine dati riprogettata nello strumento di registrazione delle origini dati del **Catalogo dati di Azure**. Lo strumento di registrazione delle origini dati è stato aggiornato per rendere più semplice per gli utenti individuare e selezionare dalle origini dati supportate dal **Catalogo dati di Azure**.
- Supporto di dieci lingue aggiuntive nel portale **Catalogo dati di Azure** e nello strumento di registrazione delle origini dati. Oltre all'inglese, l'esperienza del **Catalogo dati di Azure** è ora disponibile in tedesco, spagnolo, francese, italiano, giapponese, coreano, portoghese (Brasile), russo, cinese semplificato e cinese tradizionale. L'esperienza utente del **Catalogo dati di Azure** sarà localizzata in base alle preferenze della lingua configurate in Windows o nel browser Web dell'utente.
- Supporto della replica geografica dei dati del **Catalogo dati di Azure** per il ripristino di emergenza e la continuità aziendale. Tutti i contenuti del **Catalogo dati di Azure**, compresi i metadati delle origini dati e le annotazioni di crowdsourcing vengono ora replicati tra due aree di Azure senza alcun costo aggiuntivo per i clienti. Le aree di Azure sono pre-abbinate, ad almeno 500 miglia di distanza, e seguono il mapping come descritto in [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).


## Novità della versione della settimana del 5 febbraio 2016

Nella settimana del 5 febbraio 2016 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la modifica della sottoscrizione di Azure usata dal **Catalogo dati di Azure**. Gli amministratori del **Catalogo dati di Azure** possono usare la pagina Impostazioni del portale **Catalogo dati di Azure** per selezionare un'altra sottoscrizione di Azure ai fini della fatturazione.

## Novità della versione della settimana del 29 gennaio 2016

Nella settimana del 29 gennaio 2016 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati aggiuntive. Gli utenti possono usare "Crea voce manuale" nel portale **Catalogo dati di Azure** oppure usare l'API REST del **Catalogo dati di Azure** per registrare le origini dati seguenti:
   + OData: funzione, set di entità e contenitore di entità
   + HTTP: file, endpoint, report e sito
   + File system: file
   + SharePoint: elenco
   + FTP: file e directory
   + Salesforce.com: oggetto
   + DB2: tabella, vista e database
   + PostgreSQL: tabella, vista e database
- Supporto per "Apri in SQL Server Data Tools" per le origini dati SQL Server, inclusi il database SQL di Azure e Azure SQL Data Warehouse.  

> [AZURE.NOTE] "Apri in SQL Server Data Tools" richiede l'installazione di Visual Studio 2013 con Update 4 e degli strumenti di SQL Server. Per installare la versione più recente di SQL Server Data Tools, vedere [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx).

## Novità della versione della settimana del 22 gennaio 2016

Nella settimana del 22 gennaio 2016 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione e l'individuazione di viste e pacchetti SAP HANA. Gli utenti possono registrare le origini dati SAP HANA con lo strumento di registrazione delle origini dati del **Catalogo dati di Azure** e possono annotare e individuare le origini di dati SAP HANA registrate usando il portale **Catalogo dati di Azure**.

## Novità della versione della settimana dell'8 gennaio 2016

Nella settimana dell'8 gennaio 2016 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Possibilità di aggiungere e rimuovere gli asset di dati nel portale **Catalogo dati di Azure**. Gli utenti possono scegliere di bloccare gli asset di dati per semplificarne la riscoperta e il riuso.
- Nuova home page riprogettata nel portale **Catalogo dati di Azure**. La nuova home page include approfondimenti sulle attività del momento degli utenti, tra cui gli asset pubblicati di recente, gli asset bloccati e le ricerche salvate, oltre ad approfondimenti sulle attività nel catalogo in generale.
- Supporto per le impostazioni utente persistenti nel portale **Catalogo dati di Azure**. Le impostazioni di esperienza utente, tra cui la visualizzazione griglia o riquadro, il numero di risultati per pagina, l'attivazione o la disattivazione di evidenziazione dei risultati, sono persistenti tra le sessioni utente.
- Il **Catalogo dati di Azure** è ora disponibile in due nuove aree di Azure. I clienti possono eseguire il provisioning del **Catalogo dati di Azure** nelle aree Europa settentrionale e Asia sud-orientale, oltre a Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale e Australia orientale. Per altre informazioni, vedere la pagina [Aree di Azure](https://azure.microsoft.com/regions/).

## Novità della versione della settimana del 18 dicembre 2015

Nella settimana del 18 dicembre 2015 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per profili dati per le origini dati di SQL Data Warehouse di Azure. Durante la registrazione di tabelle e viste di SQL Data Warehouse di Azure, gli utenti possono scegliere di includere metriche dei profili dati con i metadati estratti dall'origine dati.
- Supporto per la registrazione e l'individuazione degli oggetti e dei database MySQL. Gli utenti possono registrare le origini dati di MySQL tramite lo strumento di registrazione delle origini dati **Catalogo dati di Azure** e possono annotare e individuare le origini di dati di MySQL registrate usando il portale **Catalogo dati di Azure**.

## Novità della versione della settimana del 4 dicembre 2015

Nella settimana del 4 dicembre 2015 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l'autenticazione SPNEGO e Windows per le origini dati di Teradata. Durante la registrazione di tabelle e viste Teradata, gli utenti possono scegliere di connettersi a Teradata mediante autenticazione SPNEGO e Windows, nonché LDAP e TD2.
- Supporto per le origini dati di Archivio Azure Data Lake. Gli utenti possono ora registrarsi e individuare le origini dati di Archivio Azure Data Lake tramite Catalogo dati di Azure.
- Supporto per specificare manualmente le impostazioni di proxy di rete nello strumento di registrazione delle origini dati di Catalogo dati di Azure. Gli utenti possono selezionare "Modificare le impostazioni di proxy" nella pagina di benvenuto dello strumento e possono specificare l'indirizzo proxy e la porta che verranno usati dallo strumento.


## Novità della versione della settimana del 20 novembre 2015

Nella settimana del 20 novembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- La possibilità di visualizzare e copiare le stringhe di connessione dall'interno del portale **Catalogo dati di Azure** per le origini dati di SQL Server (inclusi i Database SQL di Azure) e Oracle. Gli utenti possono fare clic sul collegamento "Visualizza stringhe di connessione" nelle informazioni di connessione per una tabella di SQL Server o Oracle, vista o database, per visualizzare le stringhe di connessione utilizzate per la connessione all'origine dati. Le stringhe di connessione ADO.NET, ODBC, OLEDB e JDBC sono disponibili per le origini dati di SQL Server. Le stringhe di connessione ODBC e OLEDB vengono fornite per le origini dati di Oracle.
- Supporto per includere i profili dati durante la registrazione delle tabelle Teradata e visualizzazioni.
- Supporto per "Apri in Power BI Desktop" per le origini di SQL Server (inclusi i database SQL di Azure e SQL Data Warehouse di Azure), SQL Server Analysis Services. Archiviazione di Azure e HDFS.  

> [AZURE.NOTE] "Apri in Power BI Desktop" richiede che sia installata una versione corrente dell'applicazione Desktop di Power BI. Se si verificano problemi o errori durante l'uso di questa funzionalità, assicurarsi di aver scaricato la versione più recente di Power BI Desktop da [PowerBI.com](https://powerbi.com).

## Novità della versione della settimana del 13 novembre 2015

Nella settimana del 13 novembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l'autenticazione LDAP per le origini dati di Teradata. Durante la registrazione delle tabelle e visualizzazioni Teradata, gli utenti possono scegliere di connettersi a Teradata mediante autenticazione LDAP e TD2.
- Supporto per "Apri in Excel" per le origini dati di Teradata.
- Supporto per i termini di ricerca salvati nel portale **Catalogo dati di Azure**. Durante la ricerca nel portale, gli utenti possono selezionare termini di ricerca utilizzati di recente per accelerare l'esperienza di individuazione.

## Novità della versione della settimana del 6 novembre 2015

Nella settimana del 6 novembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l’anteprima per le origini dati Teradata. Durante la registrazione delle tabelle e viste Teradata, gli utenti possono scegliere di includere record snapshot con i metadati estratti dall'origine dati.
- Supporto per "Apri in Excel" per le origini dati di SQL Data Warehouse di Azure.
- Supporto per la definizione e la modifica di schemi a livello di colonna per gli asset di dati registrati manualmente. Dopo aver creato manualmente un asset di dati utilizzando il portale **Catalogo dati di Azure**, gli utenti possono aggiungere definizioni di colonna nelle proprietà dell'asset di dati.
- Supporto per query “has” durante la ricerca **Catalogo dati di Azure**, per attivare l'individuazione degli asset di dati registrati che dispongono di metadati specifici. La sintassi delle query di **Catalogo dati di Azure** ora include:

| Sintassi delle query | Scopo |
|-------------------------|---------|
| has:previews | Trova gli asset di dati che includono un'anteprima |
| has:documentation | Trova gli asset di dati per i quali è stata fornita la documentazione |
| has:tableDataProfiles | Trova gli asset di dati con informazioni sul profilo dei dati a livello di tabella |
| has:columnsDataProfiles | Trova gli asset di dati con informazioni sul profilo dei dati a livello di colonna |


## Novità della versione della settimana del 30 ottobre 2015

Nella settimana del 30 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la crittografia locale delle anteprime e dei profili dati per le origini dati registrate. **Catalogo dati di Azure** esegue in modo trasparente la crittografia dei record di anteprima e delle origini dei profili dati registrati con il servizio, senza che gli amministratori del catalogo debbano eseguire alcuna operazione di gestione.

## Novità della versione della settimana del 23 ottobre 2015

Nella settimana del 23 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per origini dati Teradata. Gli utenti possono ora registrarsi e individuare tabelle e viste Teradata.

> [AZURE.NOTE] Per la versione corrente è supportata solo l'autenticazione Teradata TD2. Nelle versioni future saranno supportati meccanismi di autenticazione aggiuntivi.

## Novità della versione della settimana del 16 ottobre 2015

Nella settimana del 16 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per le origini dati Hive locali. Gli utenti possono ora registrarsi e individuare tabelle Hive per Apache Hive di Hadoop in origini dati locali.
- Supporto per ricerche salvate nel portale **Catalogo dati di Azure**. Gli utenti possono salvare termini di ricerca e selezioni di filtri per ripetere facilmente ricerche precedenti e definire viste di contenuti del catalogo particolarmente utili. Possono anche contrassegnare una ricerca salvata come ricerca predefinita. In questo modo, quando un utente fa clic sull'icona di ricerca a forma di "lente di ingrandimento" dalla home page del portale **Catalogo dati di Azure** o dalla pagina introduttiva, verrà visualizzata direttamente la ricerca salvata contrassegnata come predefinita.


## Novità della versione della settimana del 9 ottobre 2015

Nella settimana del 9 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la documentazione in formato RTF per asset e contenitori di dati registrati nel portale di Catalogo dati di Azure. Gli utenti ora possono fornire la documentazione per asset di dati, ad esempio tabelle, viste e report, e per i contenitori, ad esempio database e modelli, per gli scenari in cui tag e descrizioni non sono sufficienti.

## Novità della versione della settimana del 2 ottobre 2015

Nella settimana del 2 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati conosciuti. Gli utenti possono immettere manualmente informazioni sulle origini dati tramite il portale **Catalogo dati di Azure** per tutti i tipi di origini dati supportati da **Catalogo dati di Azure**.
- Supporto per l'autorizzazione di gruppi di sicurezza di Azure Active Directory. Gli amministratori del catalogo possono concedere l'accesso al catalogo a gruppi di sicurezza e account utente, rendendo più semplice la gestione dell'accesso a **Catalogo dati di Azure**.
- Supporto per l'apertura di origini dati Hive in Excel dal portale **Catalogo dati di Azure**.

> [AZURE.NOTE] Per usare la funzionalità "Apri in Excel" per le origini dati Hive, è necessario installare il driver ODBC per Hive.

## Novità della versione della settimana del 25 settembre 2015

Nella settimana del 25 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per includere i profili dati durante la registrazione delle origini dati Hive.
- Supporto per il rilevamento a livello di codice dell'API del catalogo, per rendere più semplice l'integrazione delle applicazioni con **Catalogo dati di Azure**.

## Novità della versione della settimana del 18 settembre 2015

Nella settimana del 18 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Una nuova funzionalità "introduttiva" per l'individuazione di origini dati nel portale di **Catalogo dati di Azure**. Quando gli utenti accedono alla pagina di "individuazione" del portale di **Catalogo dati di Azure** senza immettere un termine di ricerca, viene visualizzata una panoramica del contenuto del catalogo, inclusi i tag usati più di frequente, nonché gli esperti, i tipi di origini dati e i tipi di oggetti.
- Supporto per la registrazione e l'individuazione degli oggetti e dei database di Azure SQL Data Warehouse. Per altre informazioni su Azure SQL Data Warehouse, vedere [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Supporto per la registrazione e l'individuazione di modelli di SQL Server Analysis Services e di server SQL Server Reporting Services come contenitori. Quando si registrano oggetti SSAS e SSRS, **Catalogo dati di Azure** crea una voce per il modello SSAS e il server SSRS, nonché per i report e altri oggetti. I contenitori possono essere individuati e annotati mediante il portale **Catalogo dati di Azure**. Gli utenti possono inoltre eseguire ricerche e applicare filtri nel contenuto di un modello o di un server oltre a eseguire ricerche e applicare filtri nel contenuto del catalogo.

> [AZURE.NOTE] Gli oggetti SSAS e SSRS che sono stati registrati prima della versione del 18 settembre devono essere registrati di nuovo usando lo strumento di registrazione dell'origine dati prima di aggiungere la voce del modello o del server nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana dell'11 settembre 2015

Nella settimana dell'11 settembre 2015 sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione e l'individuazione degli oggetti di SQL Server Analysis Services tramite HTTP/HTTPS. Gli utenti possono ora connettersi ai server SSAS usando un URL (ad esempio https://servername/olap/msmdpump.dll) anziché un nome di server e possono usare l'autenticazione di base e le connessioni anonime oltre all'autenticazione di Windows. Per altre informazioni sulle connessioni HTTP/HTTPS a SSAS, vedere [Configurare l'accesso HTTP ad Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
- Supporto per le origini dati Hive in HDInsight. Gli utenti possono ora registrarsi e individuare le tabelle Hive per Apache Hive di Hadoop sulle origini dati HDInsight. Per altre informazioni su Hive in HDInsight, vedere il [centro di documentazione HDInsight](../hdinsight-use-hive/).
- Supporto per la registrazione e l'individuazione dei database Oracle e dei cluster HDFS come contenitori. Quando si registrano tabelle e viste Oracle o HDFS, **Catalogo dati di Azure** crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di **Catalogo dati di Azure**. Gli utenti possono inoltre cercare e filtrare il contenuto di un database o cluster, oltre a cercare e filtrare il contenuto del catalogo.


> [AZURE.NOTE] Le tabelle, le visualizzazioni e i file HDFS di Oracle che sono stati registrati prima della versione dell'11 settembre devono essere registrate di nuovo usando lo strumento di registrazione di origine dati prima di aggiungere la voce del database o cluster nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana del 4 settembre 2015

Nella settimana del 4 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati sconosciute. Gli utenti possono immettere manualmente informazioni sull'origine dati tramite il portale di **Catalogo dati di Azure** in modo che sia possibile annotare e individuare anche le origini dati non esplicitamente supportate dallo strumento di registrazione dell'origine dati.
- Supporto per la registrazione e l'individuazione dei database di SQL Server come contenitori. Quando si registrano le tabelle di SQL Server e le viste, **Catalogo dati di Azure** crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di **Catalogo dati di Azure**. Gli utenti possono inoltre cercare e filtrare il contenuto di un database oltre a cercare e filtrare il contenuto del catalogo.

> [AZURE.NOTE] Le tabelle di SQL Server e le viste che sono state registrate prima della versione del 4 settembre devono essere registrate usando lo strumento di registrazione di origine dati prima di aggiungere la voce del database nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana del 28 agosto 2015

Nella settimana del 28 agosto 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l'analisi dei dati di origini dati di SQL Server e Oracle. Quando si registrano le viste e le tabelle di SQL Server e Oracle, gli utenti possono scegliere di includere informazioni sul profilo dati per gli oggetti da registrare. Il profilo dati include le statistiche a livello di oggetto e di colonna.
- Supporto per le origini dati HDFS Hadoop. Gli utenti possono ora registrarsi e individuare directory e file HDFS.

## Novità della versione della settimana del 21 agosto 2015

Nella settimana del 21 agosto 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per fornire informazioni sulla richiesta di accesso per le origini dati registrate. Per qualsiasi asset di dati registrato, gli utenti possono ora fornire istruzioni per richiedere l'accesso, inclusi URL o collegamenti di posta elettronica, per una facile integrazione con i processi e gli strumenti esistenti.
- Descrizioni comandi per i tag e gli esperti, per semplificare l'individuazione dei metadati forniti dagli utenti per gli asset di dati registrati.
- Nuovo menu e pulsante "User" nella barra di spostamento superiore. Questo menu consente di vedere l'account usato per accedere a **Catalogo dati di Azure** ed eventualmente disconnettersi. Questo menu visualizza anche il nome del catalogo, utile per gli sviluppatori che usano l'API REST di **Catalogo dati di Azure**.
- Edizione Standard: quando si aggiungono proprietari agli asset di dati, il **Catalogo dati di Azure** ora supporta sia gli account utente che i gruppi di sicurezza come proprietari. Per aggiungere un gruppo di sicurezza come proprietario di asset di dati selezionati, è possibile immettere il nome visualizzato o l'indirizzo di posta elettronica UPN del gruppo, se presente.
- Supporto per le origini dati dell'archivio BLOB di Azure. Gli utenti possono ora registrarsi e individuare i BLOB e le directory di archiviazione di Azure.

<!---HONumber=AcomDC_0316_2016-->