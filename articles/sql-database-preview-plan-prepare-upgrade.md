<properties 
	pageTitle="Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL" 
	description="Questo argomento descrive le operazioni di preparazione e le limitazioni relative all'aggiornamento del database SQL di Azure alla versione 12." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL


<!--
GeneMi, 05 marzo 2015 giovedì 20:06
Rimuovere 'Azure' dal titolo.
-->


Questo argomento descrive la pianificazione e i preparativi da effettuare per eseguire l'aggiornamento dei database SQL di Azure dalla versione 11 alla versione 12.


È disponibile un nuovo [portale di Azure](http://portal.azure.com/) per supportare l'aggiornamento alla versione 12.


> [AZURE.NOTE]
> Database di test, copie di database o nuovi database sono candidati ideali per l'aggiornamento all'anteprima. Per database di produzione vitali per la propria attività è opportuno attendere il termine del periodo di anteprima.<br/><br/>
> Per la famiglia della versione 12, è possibile determinare se nella propria area geografica è disponibile lo stato di anteprima o GA esaminando la tabella delle aree in [Novità della versione 12 del database SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable).


La seguente tabella elenca e descrive altri argomenti della Guida relativi alla versione 12.


| Titolo e collegamento | Descrizione del contenuto |
| :--- | :--- |
| [Novità della versione 12 del database SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Fornisce informazioni dettagliate sulle funzionalità della versione 12 che avvicinano il database SQL di Azure a Microsoft SQL Server 2014. |
| [Procedura dettagliata: effettuare l'iscrizione all'ultimo aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Descrive la procedura da eseguire per aggiornare i database SQL di Azure all'anteprima più recente. |
| [Creazione di un database nell'aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Descrive come creare un nuovo database SQL di Azure che includa le nuove funzionalità dell'anteprima più recente. Illustra le varie opzioni oltre alla semplice creazione di un database vuoto. |
| [Aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Descrive come aggiornare i database SQL di Azure all'anteprima più recente. Indica che è necessario innanzitutto eseguire un backup del database, poiché l'aggiornamento è permanente e non può essere annullato. |


## A. Pianificazione


Le seguenti sottosezioni forniscono le informazioni da acquisire e indicano le decisioni da prendere prima di procedere con l'aggiornamento del database SQL di Azure alla versione 12.

### A.1 Chiarimento sulla versione


Questo documento riguarda l'aggiornamento del database SQL di Microsoft Azure dalla versione 11 alla versione 12. A livello più formale, i numeri di versione si avvicinano ai due seguenti valori, come indicato dall'istruzione Transact-SQL **SELECT @@version;** :


- 11.0.9228.18 *(11)*
- 12.0.2000.8 *(o appena superiore, 12)*


### A.2 Pianificazione del livello di servizio


A partire dalla versione 12, il database SQL di Azure supporterà solo i livelli di servizio denominati Basic, Standard e Premium. I livelli di servizio Web e Business non sono supportati nella versione 12. Quindi, se si intende aggiornare un database SQL di Azure che attualmente è al livello di servizio Web o Business, è necessario decidere quale sarà il suo nuovo livello di servizio.


Per informazioni dettagliate sui livelli di servizio Basic, Standard e Premium, vedere:


- [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Database SQL di Azure Prezzi](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 Rivedere la configurazione della replica geografica


Se il database SQL di Azure è configurato per la replica geografica, è necessario documentarne la configurazione corrente e interrompere la replica geografica prima di avviare i preparativi per l'aggiornamento. Al termine dell'aggiornamento occorre riconfigurare il database per la replica geografica.


La strategia consiste nel lasciare inalterata l'origine ed eseguire il test su una copia del database.


### A.4 Confronto tra aggiornamento sul posto e copia del database in un nuovo server


Sono disponibili due metodi per aggiornare il database SQL di Azure dalla versione 11 alla versione 12:


- Aggiornamento sul posto: al termine di questo processo il database versione 11 non esisterà più. La versione del server di database SQL sarà 12.
- Copiare il database nel server 12: questo processo lascia inalterato il database versione 11 e ne crea una copia aggiornata in un server di database SQL di Azure 12. <br/> Si noti che le informazioni DNS usate dal codice client per accedere al database non consentiranno più di accedere alla versione 11 e dovrebbero essere modificate per accedere al database versione 11.


## B. Preparativi


Terminata la pianificazione, è possibile eseguire i passaggi descritti nelle seguenti sottosezioni per preparare la fase finale di aggiornamento.


Negli argomenti della Guida indicati all'inizio di questo argomento sono riportate descrizioni dettagliate della fase finale di aggiornamento.


### B.1 Azioni relative al livello di servizio


I livelli di prezzo Web e Business non sono supportati nella versione 12.


Se il database SQL di Azure versione 11 è un database Web o Business, durante il processo di aggiornamento verrà proposto di passare a un piano supportato. Viene suggerito un piano adeguato in base alla cronologia del carico di lavoro del database. È comunque possibile scegliere il piano supportato che si preferisce.


Per ridurre i passaggi da eseguire durante l'aggiornamento, è possibile disattivare il piano Web e Business per il database versione 11 prima di avviare l'aggiornamento. Questa operazione può essere eseguita nel portale di Azure.


In caso di incertezza sul livello di servizio a cui passare, il livello S0 del piano Standard rappresenta una scelta iniziale adeguata.


### B.2 Sospendere la replica geografica durante l'aggiornamento


Non è possibile eseguire l'aggiornamento alla versione 12 se nel database è attiva la replica geografica. È innanzitutto necessario riconfigurare il database per interrompere l'uso della replica geografica.


Al termine dell'aggiornamento sarà possibile configurare il database in modo che usi di nuovo la replica geografica.


## <a id="limitations"></a>C. Limitazioni durante e dopo l'aggiornamento alla versione 12


Questa sezione descrive le limitazioni associate all'aggiornamento alla versione 12 del database SQL di Azure.


### C.1 Portale di anteprima per la versione 12


Solo il primo dei due seguenti portali di gestione di Azure supporta i database della versione 12:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Questa versione più recente del portale è in stato di anteprima e non è ancora disponibile a livello generale.<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Questa versione precedente del portale non verrà aggiornata per supportare la versione 12.


Si consiglia di connettersi al database SQL di Azure con Visual Studio 2013 (VS2013). VS2013 può essere usato per le seguenti attività:


- Per eseguire un'istruzione T-SQL.
- Per progettare uno schema.
- Per sviluppare un database, sia online che offline.


È invece possibile connettersi con [Visual Studio Community 2013](https://www.visualstudio.com/it-it/news/vs2013-community-vs.aspx/), una versione gratuita e completa di VS2013.


Nella pagina del database del portale di gestione di Azure precedente, per avviare VS2013 nel computer e connettersi al Database SQL di Azure è possibile fare clic su **Apri in Visual Studio**.


In alternativa, per connettersi al Database SQL di Azure è possibile usare SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/). Per altri dettagli, vedere il seguente post di blog:<br/>[Aggiornamenti degli strumenti client per il database SQL di Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### C.2 Limitazione *during* l'aggiornamento alla versione 12

La seguente tabella descrive le limitazioni attive durante il processo di aggiornamento.


| Limitazione | Descrizione |
| :--- | :--- |
| Durata dell'aggiornamento | Per un database molto grande, di dimensioni pari o anche superiori a 50 GB, il processo di aggiornamento può richiedere fino a 24 ore. |
| Ritardo di aggiornamento della voce DNS | Terminato l'aggiornamento, occorreranno alcuni minuti perché il sistema aggiorni la voce DNS per il database 12 per la connettività dall'applicazione client. |
| Creazione di database non supportata | Durante l'esecuzione dell'aggiornamento, sul server di database SQL di Azure 12 non sono disponibili le seguenti azioni per la creazione di un database: <p></p> * Creazione di un nuovo database <br/> * Copia di un database nel server <br/> * Ripristino di un database eliminato <br/> * Ripristino temporizzato di un database <br/><br/> Tuttavia, il ripristino temporizzato durante l'aggiornamento è una funzionalità che potrebbe essere supportata prima della fine del periodo di anteprima 12. |
| Replica geografica non supportata | La replica geografica non è supportata in un server 12 sottoposto a un aggiornamento dalla versione 11. |
| Regole di avviso | Le regole di avviso non possono essere aggiunte a un database 12. |


### C.3 Limitazione *after* l'aggiornamento alla versione 12

La seguente tabella descrive le limitazioni attive dopo il processo di aggiornamento.


| Limitazione | Descrizione |
| :--- | :--- |
| DNS nel database versione 11 | Al termine di un aggiornamento eseguito mediante la tecnica della copia del database versione 11 in un nuovo server versione 12, le informazioni DNS usate dal codice client per la connessione fanno riferimento al database versione 12. <p> </p> Il codice client dovrebbe usare informazioni DNS modificate per fare riferimento al database versione 11. |
| Non è possibile ripristinare la versione 11 | Dopo un aggiornamento sul posto, il risultato non può essere annullato. |
| Livello Web o Business | Dopo l'avvio dell'aggiornamento, il server per il nuovo database versione 12 non è più in grado di riconoscere o accettare il livello di servizio Web o Business. |
| Sconto del 50% non riportato nelle schede di livello dei prezzi nel portale Azure | Durante il periodo di anteprima è disponibile uno sconto* di anteprima del 50% sui database registrati nell'ultimo aggiornamento di anteprima del database SQL di Azure (versione 12). Anche se non è visualizzato nel portale di anteprima nel pannello relativo ai piani tariffari per i servizi, lo sconto è in vigore.<br/><br/> Lo sconto del 50% rimane attivo in tutte le aree geografiche fino al **31 marzo 2015**, data in cui scadrà per tutte le aree. Lo sconto è valido anche nelle aree per cui è stato annunciato lo stato di disponibilità generale (GA).<br/><br/> * L'uso delle funzionalità dell'aggiornamento del database SQL di Azure più recente (versione 12) è soggetto ai termini di anteprima nel contratto di licenza (ad esempio, il contratto Enterprise Agreement, il Contratto di Microsoft Azure o il Contratto di Sottoscrizione Microsoft Online), nonché alle [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/) applicabili.  Per la durata dell'anteprima, Microsoft (o il rivenditore locale, a seconda delle situazioni) addebiterà all'utente per tutti i database registrati in questa versione di anteprima la metà della tariffa prevista per la disponibilità generale (GA) in modo da applicare uno sconto del 50% per l'anteprima. Microsoft invierà un avviso via e-mail 30 giorni prima della scadenza del periodo di anteprima e la tariffa di anteprima scontata. |


### C.4 Esportazione e importazione *after* l'aggiornamento alla versione 12


È possibile esportare o importare un database versione 12 usando il [portale Web Azure](http://portal.azure.com/). In alternativa, è possibile esportare o importare usando uno dei seguenti strumenti:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Tuttavia, per usare gli strumenti, è innanzitutto necessario installare gli aggiornamenti più recenti per assicurarsi che supportino le nuove funzionalità della versione 12:


- [Pacchetto di aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Aggiornamento di febbraio 2015 di SQL Server Data Tools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Aggiornamento di febbraio 2015 di Data-Tier Application Framework (DacFx) per il database SLQ di Azure versione 12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] I precedenti collegamenti sono stati aggiornati il giorno 2 marzo 2015 o in data successiva. Si consiglia di usare gli aggiornamenti più recenti di questi strumenti.


#### C.4.1 L'importazione presenta un problema temporaneo con i file bacpac


 (Mercoledì 18 febbraio 2015)


Esiste un problema noto con l'esportazione di file bacpac da un server di database SQL di Azure aggiornato alla versione 12. I file bacpac esportati conterranno un oggetto denominato script_deployment_databases. I file bacpac che contengono questo oggetto non possono essere importati usando gli strumenti SQL Server Management Studio (SSMS), SqlPackage.exe o API Data-Tier Application Framework (DacFx).


Tuttavia, il portale web Azure consente di importare un file bacpac di questo tipo nel database SQL di Azure. Microsoft prevede di rilasciare una fix permanente per questo problema, incluso un aggiornamento per gli strumenti interessati, il giorno venerdì 27 febbraio 2015 o durante i giorni successivi. Nel frattempo, contattare il supporto Microsoft se è necessaria ulteriore assistenza per ripristinare un file già esportato con estensione bacpac.


Oltre al problema di esportazione, è possibile che un numero limitato di server e clienti che hanno recentemente eseguito l'aggiornamento alla versione 12 riscontri un errore diverso durante il tentativo di importare un file bacpac. Questo errore correlato alle autorizzazioni è temporaneo e in genere viene risolto in un server interessato entro un giorno. Si prevede che anche questo problema verrà definitivamente risolto durante la settimana di lunedì 23 febbraio 2015. Nel frattempo, ripetendo l'operazione l'importazione dovrebbe avere esito positivo. Se è necessaria ulteriore assistenza per l'importazione di un file bacpac in un database SQL di Azure, contattare il supporto Microsoft.


Se necessario, è possibile seguire questa procedura per contattare il supporto Microsoft:


1. Accedere al portale Azure.
2. Fare clic con il pulsante destro del mouse sul nome dell'account, nell'angolo superiore destro.
3. Scegliere la voce relativa al supporto tecnico dal menu contestuale.
 - La voce di menu sarà probabilmente **Contatta il supporto Microsoft** o **Guida e supporto tecnico**.


> [AZURE.NOTE] (Lunedì 2 marzo 2015)
> 
> Il problema descritto nella presente sezione C.4.1 è stato risolto. Gli utenti possono proseguire senza particolari conoscenze o senza dover implementare azioni specifiche.
> 
> L'importazione/esportazione automatica funziona normalmente indipendentemente dalla data di creazione del file bacpac.
> 
> Gli utenti che elaborano i file bacpac utilizzando DacFx devono scaricare gli strumenti aggiornati. I collegamenti per i download sono disponibili nella sezione C.4.


### C.5 Ripristinare un database versione 11 eliminato nella versione 12

Il seguente scenario illustra che un database SQL di Azure versione 11 eliminato può essere ripristinato in un server di database SQL di Azure versione 12.

1. Si supponga di disporre di un server di database SQL di Azure versione 11. <br/> Nel server è presente un database al livello di servizio Web e Business obsoleto.
2. Il database viene eliminato.
3. Il server viene aggiornato alla versione 12.
4. In seguito il database viene ripristinato nel server. <br/> Il database viene quindi aggiornato alla versione 12, al livello S0 del livello di servizio Standard.
5. Per il database è possibile attivare qualsiasi livello di servizio supportato, se il livello S0 non è in linea con le proprie esigenze.



## D. Risoluzione in caso di errore

Se l'aggiornamento non riesce per qualche motivo, il database versione 11 rimane attivo e disponibile come di consueto.


## Collegamenti correlati

- [Funzionalità di anteprima](http://azure.microsoft.com/services/preview/) di Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
<!--HONumber=47-->
