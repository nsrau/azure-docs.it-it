<properties
	pageTitle="Pianificare l'aggiornamento a SQL Database V12 | Microsoft Azure"
	description="In questo argomento vengono descritte le operazioni di preparazione e le limitazioni relative all'aggiornamento del database SQL di Azure alla versione 12."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="genemi"/>


# Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL


Questo argomento descrive la pianificazione e i preparativi da effettuare per eseguire l'aggiornamento dei database SQL di Azure dalla versione 11 alla versione 12.


È disponibile un nuovo [portale di Azure](https://portal.azure.com/) per supportare l'aggiornamento alla versione 12.


Nella seguente tabella vengono elencati altri argomenti della Guida relativi alla versione 12.


| Titolo e collegamento | Descrizione del contenuto |
| :--- | :--- |
| [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md) | Fornisce informazioni dettagliate sulle funzionalità della versione 12 che avvicinano il database SQL di Azure a Microsoft SQL Server. |
| [Creare un database nella versione 12 del database SQL](sql-database-get-started.md) | Viene descritto come creare un nuovo database SQL di Azure versione 12. Illustra le varie opzioni oltre alla semplice creazione di un database vuoto. |


## Pianificazione


Le seguenti sottosezioni forniscono le informazioni da acquisire e indicano le decisioni da prendere prima di procedere con l'aggiornamento del database SQL di Azure alla versione 12.

### Chiarimento sulla versione


Questo documento riguarda l'aggiornamento del database SQL di Microsoft Azure dalla versione 11 alla versione 12. A livello più formale, i numeri di versione si avvicinano ai due seguenti valori, come indicato dall'istruzione Transact-SQL **SELECT @@version;**:


- 12\.0.2000.8 *(o appena superiore, V12)*
- 11\.0.9228.18 *(V11)*
 - V11 è indicato anche come SAWA v2.

### Pianificazione del livello di servizio


A partire dalla versione 12, il database SQL di Azure supporterà solo i livelli di servizio denominati Basic, Standard e Premium. I livelli di servizio Web e Business non sono supportati nella versione 12. Quindi, se si intende aggiornare un database SQL di Azure che attualmente è al livello di servizio Web o Business, è necessario decidere quale sarà il suo nuovo livello di servizio.


Per informazioni dettagliate sui livelli di servizio Basic, Standard e Premium, vedere:

- [Livelli di servizio del database SQL](sql-database-service-tiers.md)
- [Aggiornare le edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md)



### Rivedere la configurazione della replica geografica


Se il database SQL di Azure è configurato per la replica geografica, è necessario documentarne la configurazione corrente e interrompere la replica geografica prima di avviare i preparativi per l'aggiornamento. Al termine dell'aggiornamento occorre riconfigurare il database per la replica geografica.


La strategia consiste nel lasciare inalterata l'origine ed eseguire il test su una copia del database.


## Preparativi


Terminata la pianificazione, è possibile eseguire i passaggi descritti nelle seguenti sottosezioni per preparare la fase finale di aggiornamento.


Negli argomenti della Guida indicati all'inizio di questo argomento sono riportate descrizioni dettagliate della fase finale di aggiornamento.


### Azioni relative al livello di servizio


I livelli di prezzo Web e Business non sono supportati nella versione 12.


Se il database SQL di Azure versione 11 è un database Web o Business, durante il processo di aggiornamento verrà proposto di passare a un piano supportato. Viene suggerito un piano adeguato in base alla cronologia del carico di lavoro del database. È comunque possibile scegliere il piano supportato che si preferisce.


Per ridurre i passaggi da eseguire durante l'aggiornamento, è possibile disattivare il piano Web e Business per il database versione 11 prima di avviare l'aggiornamento. Questa operazione può essere eseguita nel [portale di Azure](https://portal.azure.com/).


In caso di incertezza sul livello di servizio a cui passare, il livello S2 del piano Standard rappresenta una scelta iniziale adeguata. Qualsiasi livello inferiore disporrà di un minor numero di risorse rispetto al livello Web e Business.


### Sospendere la replica geografica durante l'aggiornamento


Non è possibile eseguire l'aggiornamento alla versione 12 se nel database è attiva la replica geografica. È innanzitutto necessario riconfigurare il database per interrompere l'uso della replica geografica.


Al termine dell'aggiornamento sarà possibile configurare il database in modo che usi di nuovo la replica geografica.


### Client in una macchina virtuale di Azure


Se il programma client si connette a SQL Database V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire i seguenti intervalli di porta nella macchina virtuale:

- 11000-11999
- 14000-14999


Fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md) per ulteriori informazioni sulle porte per il Database SQL V12. Le porte sono necessarie per il miglioramento delle prestazioni in SQL Database V12.


##<a id="limitations"></a>Limitazioni durante e dopo l'aggiornamento alla versione 12


### Portali per la versione 12


Sono disponibili tre portali per Azure e ognuno dispone di capacità diverse relativamente alla versione 12 del database SQL.


- [http://portal.azure.com/](https://portal.azure.com/)<br/>Questo portale di Azure è nuovo ed è ancora in stato di anteprima. Questo portale non ha ancora raggiunto lo stato di disponibilità generale (GA). Portale in uso:
 - Consente di gestire i server e i database versione 12.
 - Consente l'aggiornamento del database versione 11 alla versione 12.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Questo portale di Azure classico, infine, potrebbe essere eliminato. Portale in uso:
 - Consente di gestire i server e i database versione 12.
 - *Non* consente l'aggiornamento del database versione 11 alla versione 12.


- (http://*yourservername*.database.windows.net)<br/> Portale classico di Database SQL di Azure:
 - *Non* consente la gestione di server versione 12.


Si consiglia di connettersi ai database SQL di Azure con Visual Studio 2013 (VS2013). VS2013 può essere usato per le seguenti attività:


- Per eseguire un'istruzione Transact-SQL.
- Per progettare uno schema.
- Per sviluppare un database, sia online che offline.


È invece possibile connettersi con [Visual Studio Community 2013,](https://www.visualstudio.com/it-IT/news/vs2013-community-vs.aspx/) una versione gratuita e completa di VS2013.


Nella pagina del database del portale di Azure classico precedente, per avviare VS2013 nel computer e connettersi al database SQL di Azure è possibile fare clic su **Apri in Visual Studio**.


In alternativa, per connettersi al database SQL di Azure è possibile utilizzare SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/). Ulteriori dettagli sono in questo post di blog:<br/>[Aggiornamenti degli strumenti client per il database SQL di Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### Limitazione *durante* l'aggiornamento alla versione 12


Il database V11 rimane disponibile per l'accesso ai dati durante l'aggiornamento alla versione V12. Tuttavia è necessario considerare due limitazioni.


| Limitazione | Descrizione |
| :--- | :--- |
| Durata dell'aggiornamento | La durata dell'aggiornamento dipende dalla dimensione, dall'edizione e dal numero di database presenti nel server. Il processo di aggiornamento può durare da ore a giorni per i server, soprattutto per server che dispongono di database con le seguenti caratteristiche:<br/><br/>* Con una dimensione superiore a 50 GB oppure<br/>* A un livello di servizio non Premium<br/><br/>Anche la creazione di nuovi database nel server durante l'aggiornamento può aumentare la durata dell'aggiornamento. |
| Replica geografica non supportata | La replica geografica non è supportata in un server 12 sottoposto a un aggiornamento dalla versione 11. |
| Il database è temporaneamente non disponibile nella fase finale dell'aggiornamento a V12 | I database che appartengono al server V11 restano disponibili durante il processo di aggiornamento. Tuttavia, la connessione al server e ai database è temporaneamente non disponibile nella fase finale, quando inizia la transizione da V11 al V12 pronto.<br/><br/>La durata del passaggio può variare da 40 secondi a 5 minuti. Per la maggior parte dei server, il passaggio avviene in 90 secondi. La durata del passaggio aumenta per i server che dispongono di un numero elevato di database, o quando i database hanno carichi di lavoro di scrittura pesanti. |


### Limitazione *dopo* l'aggiornamento alla versione 12


| Limitazione | Descrizione |
| :--- | :--- |
| Non è possibile ripristinare la versione 11 | Dopo un aggiornamento sul posto, il risultato non può essere annullato. |
| Livello Web o Business | Dopo l'avvio dell'aggiornamento, il server per il nuovo database versione 12 non è più in grado di riconoscere o accettare il livello di servizio Web o Business. |



### Esportazione e importazione *dopo* l'aggiornamento alla versione 12


È possibile esportare o importare un database versione 12 usando il [portale di Azure](https://portal.azure.com/). In alternativa, è possibile esportare o importare usando uno dei seguenti strumenti:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Tuttavia, per usare gli strumenti, è innanzitutto necessario installare gli aggiornamenti più recenti per assicurarsi che supportino le nuove funzionalità della versione 12:


- [Aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Aggiornamento di febbraio 2015 di SQL Server Data Tools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Aggiornamento di febbraio 2015 di Data-Tier Application Framework (DacFx) per il database SLQ di Azure versione 12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] I precedenti collegamenti sono stati aggiornati il giorno 2 marzo 2015 o in data successiva. Si consiglia di usare gli aggiornamenti più recenti di questi strumenti.


#### Esportazione automatizzata


L'esportazione automatizzata continua a essere disponibile come anteprima. Nessun aggiornamento degli strumenti client sono necessari quando si utilizza l'esportazione automatica. Se si sceglie di eseguire il file risultante e di importarlo in un server locale, gli aggiornamenti degli strumenti elencati in precedenza sono necessari affinché l'importazione abbia esito positivo.


### Ripristino di un database V11 eliminato nella versione V12

Il seguente scenario illustra che un database SQL di Azure versione 11 eliminato può essere ripristinato in un server di database SQL di Azure versione 12.

1. Si supponga di disporre di un server di database SQL di Azure versione 11. <br/> Nel server è presente un database al livello di servizio Web e Business obsoleto.
2. Il database viene eliminato.
3. Il server viene aggiornato alla versione 12.
4. In seguito il database viene ripristinato nel server. <br/> Il database viene quindi aggiornato alla versione 12, al livello S0 del livello di servizio Standard.
5. Per il database è possibile attivare qualsiasi livello di servizio supportato, se il livello S0 non è in linea con le proprie esigenze.


### Cmdlet PowerShell


I cmdlet di PowerShell consentono di avviare, arrestare o monitorare un aggiornamento alla versione 12 del database SQL di Azure dalla versione 11 o qualsiasi altra versione precedente alla 12.

- [Eseguire l'aggiornamento a database SQL V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)

Per la documentazione di riferimento sui cmdlet di Powershell, vedere:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.


## Risoluzione in caso di errore


Se l'aggiornamento non riesce per qualche motivo, il database versione 11 rimane attivo e disponibile come di consueto.


## Collegamenti correlati


- [Funzionalità di anteprima](https://azure.microsoft.com/services/preview/) di Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1

<!---HONumber=AcomDC_0427_2016-->