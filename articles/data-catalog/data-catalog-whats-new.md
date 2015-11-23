<properties
   pageTitle="Novità di Catalogo dati di Azure"
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
   ms.date="11/06/2015"
   ms.author="maroche"/>

# Novità di Catalogo dati di Azure

Gli aggiornamenti su **Catalogo dati di Azure** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includeranno nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. Questa pagina illustra le nuove funzionalità destinate all'utente aggiunte al servizio **Catalogo dati di Azure**.

## Novità della versione della settimana del 6 novembre 2015

Nella settimana del 6 novembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l’anteprima per le origini dati Teradata. Durante la registrazione delle tabelle e viste Teradata, gli utenti possono scegliere di includere record snapshot con i metadati estratti dall'origine dati.
- Supporto per "Apri in Excel" per le origini dati di SQL Data Warehouse di Azure.
- Supporto per la definizione e la modifica di schemi a livello di colonna per gli asset di dati registrati manualmente. Dopo aver creato manualmente un asset di dati utilizzando il portale **Catalogo dati di Azure**, gli utenti possono aggiungere definizioni di colonna nelle proprietà dell'asset di dati.
- Supporto per query “has” durante la ricerca **Catalogo dati di Azure**, per attivare l'individuazione degli asset di dati registrati che dispongono di metadati specifici. La sintassi delle query di**Catalogo dati di Azure** ora include:

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

> [AZURE.NOTE]Per la versione corrente è supportata solo l'autenticazione Teradata TD2. Nelle versioni future saranno supportati meccanismi di autenticazione aggiuntivi.

## Novità della versione della settimana del 16 ottobre 2015

Nella settimana del 16 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per le origini dati Hive locali. Gli utenti possono ora registrarsi e individuare tabelle Hive per Apache Hive di Hadoop in origini dati locali.
- Supporto per ricerche salvate nel portale di **Catalogo dati di Azure**. Gli utenti possono salvare termini di ricerca e selezioni di filtri per ripetere facilmente ricerche precedenti e definire viste di contenuti del catalogo particolarmente utili. Possono anche contrassegnare una ricerca salvata come ricerca predefinita. In questo modo, quando un utente fa clic sull'icona di ricerca a forma di "lente di ingrandimento" dalla home page del portale di **Catalogo dati di Azure** o dalla pagina introduttiva, verrà visualizzata direttamente la ricerca salvata contrassegnata come predefinita.


## Novità della versione della settimana del 9 ottobre 2015

Nella settimana del 9 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la documentazione in formato RTF per asset e contenitori di dati registrati nel portale di Catalogo dati di Azure. Gli utenti ora possono fornire la documentazione per asset di dati, ad esempio tabelle, viste e report, e per i contenitori, ad esempio database e modelli, per gli scenari in cui tag e descrizioni non sono sufficienti.

## Novità della versione della settimana del 2 ottobre 2015

Nella settimana del 2 ottobre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati conosciuti. Gli utenti possono immettere manualmente informazioni sulle origini dati tramite il portale di **Catalogo dati di Azure** per tutti i tipi di origini dati supportati da **Catalogo dati di Azure**.
- Supporto per l'autorizzazione di gruppi di sicurezza di Azure Active Directory. Gli amministratori del catalogo possono concedere l'accesso al catalogo a gruppi di sicurezza e account utente, rendendo più semplice la gestione dell'accesso a **Catalogo dati di Azure**.
- Supporto per l'apertura di origini dati Hive in Excel dal portale di **Catalogo dati di Azure**.

> [AZURE.NOTE]Per usare la funzionalità "Apri in Excel" per le origini dati Hive, è necessario installare il driver ODBC per Hive.

## Novità della versione della settimana del 25 settembre 2015

Nella settimana del 25 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per includere i profili dati durante la registrazione delle origini dati Hive.
- Supporto per il rilevamento a livello di codice dell'API del catalogo, per rendere più semplice l'integrazione delle applicazioni con **Catalogo dati di Azure**.

## Novità della versione della settimana del 18 settembre 2015

Nella settimana del 18 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Una nuova funzionalità "introduttiva" per l'individuazione di origini dati nel portale di **Catalogo dati di Azure**. Quando gli utenti accedono alla pagina di "individuazione" del portale di **Catalogo dati di Azure** senza immettere un termine di ricerca, viene visualizzata una panoramica del contenuto del catalogo, inclusi i tag usati più di frequente, nonché gli esperti, i tipi di origini dati e i tipi di oggetti.
- Supporto per la registrazione e l'individuazione degli oggetti e dei database di Azure SQL Data Warehouse. Per altre informazioni su SQL Data Warehouse di Azure, vedere [SQL Data Warehouse](http://azure.microsoft.com/services/sql-data-warehouse/).
- Supporto per la registrazione e l'individuazione di modelli di SQL Server Analysis Services e di server SQL Server Reporting Services come contenitori. Quando si registrano oggetti SSAS e SSRS, **Catalogo dati di Azure** crea una voce per il modello SSAS e il server SSRS, nonché per i report e altri oggetti. I contenitori possono essere individuati e annotati mediante il portale di **Catalogo dati di Azure**. Gli utenti possono inoltre eseguire ricerche e applicare filtri nel contenuto di un modello o di un server oltre a eseguire ricerche e applicare filtri nel contenuto del catalogo.

> [AZURE.NOTE]Gli oggetti SSAS e SSRS che sono stati registrati prima della versione del 18 settembre devono essere registrati di nuovo usando lo strumento di registrazione dell'origine dati prima di aggiungere la voce del modello o del server nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana dell'11 settembre 2015

Nella settimana del 11 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione e l'individuazione degli oggetti di SQL Server Analysis Services tramite HTTP/HTTPS. Gli utenti possono ora connettersi ai server SSAS usando un URL (ad esempio https://servername/olap/msmdpump.dll) anziché un nome di server e possono usare l'autenticazione Basic e le connessioni anonime oltre all'autenticazione di Windows. Per altre informazioni sulle connessioni HTTP/HTTPS a SSAS, vedere [Configurare l'accesso HTTP ad Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
- Supporto per le origini dati Hive in HDInsight. Gli utenti possono ora registrarsi e individuare le tabelle Hive per Apache Hive di Hadoop sulle origini dati HDInsight. Per altre informazioni su Hive in HDInsight, vedere il [centro di documentazione di HDInsight](../hdinsight-use-hive/).
- Supporto per la registrazione e l'individuazione dei database Oracle e dei cluster HDFS come contenitori. Quando si registrano tabelle e viste Oracle o HDFS, **Catalogo dati di Azure** crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di **Catalogo dati di Azure**. Gli utenti possono inoltre cercare e filtrare il contenuto di un database o cluster, oltre a cercare e filtrare il contenuto del catalogo.


> [AZURE.NOTE]Le tabelle, le visualizzazioni e i file HDFS di Oracle che sono stati registrati prima della versione dell'11 settembre devono essere registrate di nuovo usando lo strumento di registrazione di origine dati prima di aggiungere la voce del database o cluster nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana del 4 settembre 2015

Nella settimana del 4 settembre 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati sconosciute. Gli utenti possono immettere manualmente informazioni sull'origine dati tramite il portale di **Catalogo dati di Azure** in modo che sia possibile annotare e individuare anche le origini dati non esplicitamente supportate dallo strumento di registrazione dell'origine dati.
- Supporto per la registrazione e l'individuazione dei database di SQL Server come contenitori. Quando si registrano tabelle e viste di SQL Server, **Catalogo dati di Azure** crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di **Catalogo dati di Azure**. Gli utenti possono inoltre cercare e filtrare il contenuto di un database oltre a cercare e filtrare il contenuto del catalogo.

> [AZURE.NOTE]Le tabelle di SQL Server e le viste che sono state registrate prima della versione del 4 settembre devono essere registrate usando lo strumento di registrazione di origine dati prima di aggiungere la voce del database nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di **Catalogo dati di Azure**.

## Novità della versione della settimana del 28 agosto 2015

Nella settimana del 28 agosto 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l'analisi dei dati di origini dati di SQL Server e Oracle. Quando si registrano le viste e le tabelle di SQL Server e Oracle, gli utenti possono scegliere di includere informazioni sul profilo dati per gli oggetti da registrare. Il profilo dati include le statistiche a livello di oggetto e di colonna.
- Supporto per le origini dati HDFS Hadoop. Gli utenti possono ora registrarsi e individuare directory e file HDFS.

## Novità della versione della settimana del 21 agosto 2015

Nella settimana del 21 agosto 2015 sono state aggiunte a **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per fornire informazioni sulla richiesta di accesso per le origini dati registrate. Per qualsiasi asset di dati registrato, gli utenti possono ora fornire istruzioni per richiedere l'accesso, inclusi URL o collegamenti di posta elettronica, per una facile integrazione con i processi e gli strumenti esistenti.
- Descrizioni comandi per i tag e gli esperti, per semplificare l'individuazione dei metadati forniti dagli utenti per gli asset di dati registrati.
- Nuovo menu e pulsante "User" nella barra di spostamento superiore. Questo menu consente di vedere l'account usato per accedere a **Catalogo dati di Azure** ed eventualmente disconnettersi. Questo menu visualizza anche il nome del catalogo, utile per gli sviluppatori che usano l'API REST di **Catalogo dati di Azure**.
- Solo per l'edizione Standard: quando si aggiungono proprietari ad asset di dati, **Catalogo dati di Azure** supporta ora come proprietari sia account utente sia gruppi di sicurezza. Per aggiungere un gruppo di sicurezza come proprietario di asset di dati selezionati, è possibile immettere il nome visualizzato o l'indirizzo di posta elettronica UPN del gruppo, se presente.
- Supporto per le origini dati dell'archivio BLOB di Azure. Gli utenti possono ora registrarsi e individuare i BLOB e le directory di archiviazione di Azure.

<!---HONumber=Nov15_HO3-->