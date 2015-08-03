<properties
	pageTitle="Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL"
	description="In questo argomento vengono descritte le operazioni di preparazione e le limitazioni relative all'aggiornamento del database SQL di Azure alla versione 12."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL


In questo argomento vengono descritti la pianificazione e i preparativi da effettuare per eseguire l'aggiornamento dei database SQL di Azure dalla versione V11 alla versione V12 ([in versione di anteprima in alcune aree geografiche](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)).


È disponibile un nuovo [portale di anteprima di Azure](http://portal.azure.com/) per supportare l'aggiornamento alla versione 12.


Nella seguente tabella vengono elencati altri argomenti della Guida relativi alla versione 12.


| Titolo e collegamento | Descrizione del contenuto |
| :--- | :--- |
| [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md) | Fornisce informazioni dettagliate sulle funzionalità della versione 12 che avvicinano il database SQL di Azure a Microsoft SQL Server. |
| [Procedura dettagliata: effettuare l'iscrizione all'ultimo aggiornamento alla versione 12 del database SQL](sql-database-v12-sign-up.md) | Viene descritta la procedura da eseguire per aggiornare i database SQL di Azure alla versione 12. |
| [Creare un database nella versione 12 di aggiornamento del database SQL](sql-database-create.md) | Viene descritto come creare un nuovo database SQL di Azure versione 12. Illustra le varie opzioni oltre alla semplice creazione di un database vuoto. |


## Pianificazione


Le seguenti sottosezioni forniscono le informazioni da acquisire e indicano le decisioni da prendere prima di procedere con l'aggiornamento del database SQL di Azure alla versione 12.

### Chiarimento sulla versione


Questo documento riguarda l'aggiornamento del database SQL di Microsoft Azure dalla versione 11 alla versione 12. A livello più formale, i numeri di versione si avvicinano ai due seguenti valori, come indicato dall'istruzione Transact-SQL **SELECT @@version;**:


- 12.0.2000.8 *(o appena superiore, V12)*
- 11.0.9228.18 *(V11)*
 - V11 è indicato anche come SAWA v2.

### Pianificazione del livello di servizio


A partire dalla versione 12, il database SQL di Azure supporterà solo i livelli di servizio denominati Basic, Standard e Premium. I livelli di servizio Web e Business non sono supportati nella versione 12. Quindi, se si intende aggiornare un database SQL di Azure che attualmente è al livello di servizio Web o Business, è necessario decidere quale sarà il suo nuovo livello di servizio.


Per informazioni dettagliate sui livelli di servizio Basic, Standard e Premium, vedere:


- [Aggiornare le edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-new-service-tiers.md)
- [Database SQL di Azure - Prezzi](http://azure.microsoft.com/pricing/details/sql-database/)


### Rivedere la configurazione della replica geografica


Se il database SQL di Azure è configurato per la replica geografica, è necessario documentarne la configurazione corrente e interrompere la replica geografica prima di avviare i preparativi per l'aggiornamento. Al termine dell'aggiornamento occorre riconfigurare il database per la replica geografica.


La strategia consiste nel lasciare inalterata l'origine ed eseguire il test su una copia del database.


## Preparativi


Terminata la pianificazione, è possibile eseguire i passaggi descritti nelle seguenti sottosezioni per preparare la fase finale di aggiornamento.


Negli argomenti della Guida indicati all'inizio di questo argomento sono riportate descrizioni dettagliate della fase finale di aggiornamento.


### Azioni relative al livello di servizio


I livelli di prezzo Web e Business non sono supportati nella versione 12.


Se il database SQL di Azure versione 11 è un database Web o Business, durante il processo di aggiornamento verrà proposto di passare a un piano supportato. Viene suggerito un piano adeguato in base alla cronologia del carico di lavoro del database. È comunque possibile scegliere il piano supportato che si preferisce.


Per ridurre i passaggi da eseguire durante l'aggiornamento, è possibile disattivare il piano Web e Business per il database versione 11 prima di avviare l'aggiornamento. Questa operazione può essere eseguita [Portale di anteprima di Azure](http://portal.azure.com/).


In caso di incertezza sul livello di servizio a cui passare, il livello S2 del piano Standard rappresenta una scelta iniziale adeguata. Qualsiasi livello inferiore disporrà di un minor numero di risorse rispetto al livello Web e Business.


### Sospendere la replica geografica durante l'aggiornamento


Non è possibile eseguire l'aggiornamento alla versione 12 se nel database è attiva la replica geografica. È innanzitutto necessario riconfigurare il database per interrompere l'uso della replica geografica.


Al termine dell'aggiornamento sarà possibile configurare il database in modo che usi di nuovo la replica geografica.


##<a id="limitations"></a>Limitazioni durante e dopo l'aggiornamento alla versione 12


### Portali per la versione 12


Sono disponibili tre portali per Azure e ognuno dispone di capacità diverse relativamente alla versione 12 del database SQL.


- [http://portal.azure.com/](http://portal.azure.com/)<br/>Questo portale di Azure è nuovo ed è ancora in stato di anteprima. Questo portale non ha ancora raggiunto lo stato di disponibilità generale (GA). Portale in uso:
 - Consente di gestire i server e i database versione 12.
 - Consente l'aggiornamento del database versione 11 alla versione 12.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Questo portale, infine, potrebbe essere eliminato. Portale in uso:
 - Consente di gestire i server e i database versione 12.
 - *Non* consente l'aggiornamento del database versione 11 alla versione 12.


- (http://*yourservername*.database.windows.net)<br/> Portale di gestione database SQL di Azure:
 - *Non* consente la gestione di server versione 12.


Si consiglia di connettersi ai database SQL di Azure con Visual Studio 2013 (VS2013). VS2013 può essere usato per le seguenti attività:


- Per eseguire un'istruzione Transact-SQL.
- Per progettare uno schema.
- Per sviluppare un database, sia online che offline.


È invece possibile connettersi con [Visual Studio Community 2013,](https://www.visualstudio.com/it-it/news/vs2013-community-vs.aspx/) una versione gratuita e completa di VS2013.


Nella pagina del database del portale di Azure precedente, per avviare VS2013 nel computer e connettersi al database SQL di Azure è possibile fare clic su **Apri in Visual Studio**.


In alternativa, per connettersi al database SQL di Azure è possibile utilizzare SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/). Ulteriori dettagli sono in questo post di blog:<br/>[Aggiornamenti degli strumenti client per il database SQL di Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### Limitazione *durante* l'aggiornamento alla versione 12


Il database V11 rimane disponibile per l'accesso ai dati durante l'aggiornamento alla versione V12. Tuttavia è necessario considerare due limitazioni.


| Limitazione | Descrizione |
| :--- | :--- |
| Durata dell'aggiornamento | La durata dell'aggiornamento dipende dalla dimensione, dall'edizione e dal numero di database presenti nel server. Il processo di aggiornamento può durare da ore a giorni per i server, soprattutto per server che dispongono di database con le seguenti caratteristiche:<br/><br/>* Con una dimensione superiore a 50 GB oppure<br/>* A un livello di servizio non Premium<br/><br/>Anche la creazione di nuovi database nel server durante l'aggiornamento può aumentare la durata dell'aggiornamento. |
| Replica geografica non supportata | La replica geografica non è supportata in un server 12 sottoposto a un aggiornamento dalla versione 11. |


### Limitazione *dopo* l'aggiornamento alla versione 12


| Limitazione | Descrizione |
| :--- | :--- |
| Non è possibile ripristinare la versione 11 | Dopo un aggiornamento sul posto, il risultato non può essere annullato. |
| Livello Web o Business | Dopo l'avvio dell'aggiornamento, il server per il nuovo database versione 12 non è più in grado di riconoscere o accettare il livello di servizio Web o Business. |
| Sconto del 50% non riportato nelle schede di livello dei prezzi nel portale Azure | Durante il periodo di anteprima è disponibile uno sconto* di anteprima del 50% sui database registrati nell'ultimo aggiornamento di anteprima del database SQL di Azure (versione 12). Anche se non è visualizzato nel portale di anteprima nel pannello relativo ai piani tariffari per i servizi, lo sconto è in vigore.<br/><br/> Lo sconto del 50% rimane attivo in tutte le aree geografiche fino al **31 marzo 2015**, data in cui scadrà per tutte le aree. Lo sconto è valido anche nelle aree per cui è stato annunciato lo stato di disponibilità generale (GA).<br/><br/> * L'uso delle funzionalità dell'aggiornamento del database SQL di Azure più recente (versione 12) è soggetto ai termini di anteprima nel contratto di licenza (ad esempio, il contratto Enterprise Agreement, il Contratto di Microsoft Azure o il Contratto di Sottoscrizione Microsoft Online), nonché alle [Condizioni Supplementari per l’Utilizzo delle Anteprime di Microsoft Azure applicabili](http://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per la durata dell'anteprima, Microsoft (o il rivenditore locale, a seconda delle situazioni) addebiterà all'utente per tutti i database registrati in questa versione di anteprima la metà della tariffa prevista per la disponibilità generale (GA) in modo da applicare uno sconto del 50% per l'anteprima. Microsoft invierà un avviso via e-mail 30 giorni prima della scadenza del periodo di anteprima e la tariffa di anteprima scontata. |


### Esportazione e importazione *dopo* l'aggiornamento alla versione 12


È possibile esportare o importare un database versione 12 utilizzando il [Portale di anteprima di Azure](http://portal.azure.com/). In alternativa, è possibile esportare o importare usando uno dei seguenti strumenti:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Tuttavia, per usare gli strumenti, è innanzitutto necessario installare gli aggiornamenti più recenti per assicurarsi che supportino le nuove funzionalità della versione 12:


- [Aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Aggiornamento di febbraio 2015 di SQL Server Data Tools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Aggiornamento di febbraio 2015 di Data-Tier Application Framework (DacFx) per il database SLQ di Azure versione 12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE]I precedenti collegamenti sono stati aggiornati il giorno 2 marzo 2015 o in data successiva. Si consiglia di usare gli aggiornamenti più recenti di questi strumenti.


#### Esportazione automatizzata


L'esportazione automatizzata continua a essere disponibile come anteprima. Nessun aggiornamento degli strumenti client sono necessari quando si utilizza l'esportazione automatica. Se si sceglie di eseguire il file risultante e di importarlo in un server locale, gli aggiornamenti degli strumenti elencati in precedenza sono necessari affinché l'importazione abbia esito positivo.


### Ripristino di un database V11 eliminato nella versione V12

Il seguente scenario illustra che un database SQL di Azure versione 11 eliminato può essere ripristinato in un server di database SQL di Azure versione 12.

1. Si supponga di disporre di un server di database SQL di Azure versione 11. <br/> Nel server è presente un database al livello di servizio Web e Business obsoleto.
2. Il database viene eliminato.
3. Il server viene aggiornato alla versione 12.
4. In seguito il database viene ripristinato nel server. <br/> Il database viene quindi aggiornato alla versione 12, al livello S0 del livello di servizio Standard.
5. Per il database è possibile attivare qualsiasi livello di servizio supportato, se il livello S0 non è in linea con le proprie esigenze.


### Cmdlet di PowerShell


I cmdlet di PowerShell consentono di avviare, arrestare o monitorare un aggiornamento alla versione 12 del database SQL di Azure dalla versione 11 o qualsiasi altra versione precedente alla 12.


Per la documentazione di riferimento sui cmdlet di Powershell, vedere:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.


## Risoluzione in caso di errore


Se l'aggiornamento non riesce per qualche motivo, il database versione 11 rimane attivo e disponibile come di consueto.


> [AZURE.NOTE]Il database antecedente alla versione 12 per l'accesso ai dati *rimane disponibile* durante l'aggiornamento alla versione 12.


## Collegamenti correlati


- [Funzionalità di anteprima](http://azure.microsoft.com/services/preview/) di Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
 

<!---HONumber=July15_HO4-->