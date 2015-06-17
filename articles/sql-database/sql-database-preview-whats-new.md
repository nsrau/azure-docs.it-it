<properties 
	pageTitle="Novità della versione 12 del database SQL" 
	description="Questo argomento descrive i miglioramenti più recenti per il database SQL di Azure aggiunti nel dicembre 2014 o successivamente." 
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


# Novità della versione 12 del database SQL


<!--
GeneMi, 05 marzo 2015 giovedì 20:06
Rimuovere 'Azure' dal titolo dell'argomento, aggiungere '12' (J.G.).
-->



Lo stato di disponibilità generale (GA) per la versione 12 del database SQL di Azure è stata annunciata nel gennaio 2015. Con questo aggiornamento principale, il database SQL è ora quasi completamente compatibile con il motore di Microsoft SQL Server. Il database SQL è in grado in tal modo di offrire prestazioni più elevate ai clienti. Questi miglioramenti semplificano la migrazione delle applicazioni di SQL Server nel database SQL e sono di aiuto per i clienti con carichi di lavoro di database pesanti.


Solo in pochissime aree geografiche lo stato della versione 12 è ancora di anteprima, in attesa che si renda disponibile lo stato di disponibilità generale (GA). Individuare l'area di interesse nella [tabella delle aree con disponibilità generale](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Fino a quando in un'area è disponibile lo stato di disponibilità generale (GA), la versione 12 è più adatta a testare database piuttosto che database di produzione.


**[Iscriversi](https://portal.azure.com) e ottenere un database SQL per sfruttare la nuova generazione del database in Microsoft Azure. È necessaria innanzitutto una sottoscrizione a Microsoft Azure. È possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database).**


Il percorso per la pianificazione e l'aggiornamento dei database dalla versione 11 alla versione 12 inizia dalla pagina relativa alla [pianificazione e alla predisposizione dell'aggiornamento all'ultima versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Caratteristiche in primo piano


Le caratteristiche principali della versione 12 del database SQL di Azure includono i seguenti punti:


- La **sicurezza** risulta migliorata grazie alla nuova funzionalità relativa agli [utenti in database indipendenti](../sql-database-preview-whats-new/#UsersInContainedDatabases). Altre due funzionalità sono rappresentate dalla [sicurezza a livello di riga](../sql-database-preview-whats-new/#RowLevelSecurity) e dal [mascheramento dei dati dinamici](../sql-database-preview-whats-new/#DynamicDataMasking), benché entrambe le funzionalità si trovino ancora in stato di anteprima e non di disponibilità generale.


- **Gestione semplificata** di database di grandi dimensioni per il supporto di carichi di lavoro più pesanti con query parallele (solo livello Premium), [partizionamento delle tabelle](http://msdn.microsoft.com/library/ms187802.aspx), [indicizzazione online](http://msdn.microsoft.com/library/ms188388.aspx), ricompilazione senza problemi di indici di grandi dimensioni grazie alla rimozione del limite di 2 GB e altre opzioni per l'istruzione [alter database](http://msdn.microsoft.com/library/bb522682.aspx).


- **Supporto delle funzioni chiave di programmabilità** per una progettazione delle applicazioni più affidabile grazie all'[integrazione con CLR](http://msdn.microsoft.com/library/ms189524.aspx), alle [funzioni finestra](http://msdn.microsoft.com/library/ms189461.aspx)T-SQL, agli [indici XML](http://msdn.microsoft.com/library/bb934097.aspx) e al [rilevamento delle modifiche](http://msdn.microsoft.com/library/bb933875.aspx) per i dati.


- **Prestazioni all'avanguardia** con supporto di query [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) in memoria (solo livello Premium) per data mart e piccoli carichi di lavoro di analisi.


- **Monitoraggio e risoluzione dei problemi** migliorati con visibilità su oltre 100 nuove visualizzazioni tabella in un ampio set di viste di gestione database ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)).


- Il **nuovo livello di prestazioni S3 per il livello Standard** offre una maggiore flessibilità di prezzo tra i livelli Standard e Premium. Il livello S3 fornirà più DTU (unità di velocità effettiva del database) e tutte le funzionalità disponibili nel livello Standard.


## 1. Descrizione dettagliata dei miglioramenti della versione 12


Questa sezione descrive le nuove funzionalità di ogni categoria.


### 1.1 Categoria: gestione estesa del database


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Dicembre 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Utenti nei database indipendenti | È ora possibile creare utenti in un database indipendente senza dover disporre di un account di accesso corrispondente nel database master. In questo modo lo spostamento di un database in un altro server risulta notevolmente semplificato. Il codice di connessione nelle applicazioni client rimane inalterato, indipendentemente dal fatto che si utilizzino utenti del database indipendente.<br/><br/>L'impiego di questa funzionalità può risultare necessario per gli utenti che desiderano trarre vantaggio dai contratti di servizio con garanzie più elevate.<br/><br/>In genere si consiglia agli utenti di prendere in considerazione l'impiego di questa funzionalità. Alcuni scenari specifici possono tuttavia rendere l'associazione tradizionale *login+user* la scelta migliore al momento.<br/><br/>Per altre informazioni, vedere:<br/>- [Linee guida e limitazioni per la sicurezza per il database SQL di Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Gestione di database e account di accesso in database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Database indipendenti](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partizionamento delle tabelle | Le precedenti limitazioni sul [partizionamento delle tabelle](http://msdn.microsoft.com/library/ms190787.aspx) sono state eliminate. |
| Transazioni di dimensioni maggiori | Con la versione 12 non esiste più il limite di 2 GB per le modifiche dei dati in un'unica transazione. <br/><br/> Uno dei vantaggi è che la ricompilazione degli indici di grandi dimensioni non è più vincolata al limite di 2 GB per la dimensione delle transazioni. Per informazioni generali, vedere [Limiti delle risorse del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Compilazione e ricompilazione dell'indice online | Prima della versione 12, il database SQL di Azure supportava a livello generale la clausola (ONLINE = ON) dell'istruzione ALTER INDEX, che non era però supportata per gli indici in una colonna di tipo BLOB. La versione 12 supporta (ONLINE = ON) anche per gli indici nelle colonne BLOB.<br/><br/> La funzionalità ONLINE consente l'uso di indici nelle query anche durante la ricompilazione dell'indice. |
| Supporto di CHECKPOINT | La versione 12 consente di eseguire l'istruzione CHECKPOINT T-SQL sul database. |
| Miglioramento di ALTER TABLE | Consente l'esecuzione di diverse azioni di modifica delle colonne mentre la tabella rimane disponibile. Per altre informazioni, vedere [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Miglioramento di TRUNCATE TABLE | Consente il troncamento di partizioni specifiche. Per altre informazioni, vedere [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Ulteriori opzioni per ALTER DATABASE | La versione 12 supporta un numero superiore di opzioni per l'istruzione ALTER DATABASE. <br/><br/> Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) o la [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Ulteriori comandi DBCC | Nella versione 12 sono disponibili diversi nuovi comandi DBCC. Per informazioni dettagliate, vedere la [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


### 1.2 Categoria: supporto programmazione e applicazioni


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Febbraio 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Anteprima di mascheramento dei dati dinamici | Quando viene generato un set di righe da una query, un criterio di mascheramento dei dati definito può sostituire parti dei dati con caratteri 'X' per nascondere e proteggere informazioni riservate. Al termine dell'operazione di mascheramento, il set di righe modificato viene inviato al client.<br/><br/>Un esempio consiste nel coprire tutte le cifre di un numero di carta di credito tranne le ultime.<br/><br/>**NOTA:** questa funzionalità è in stato di anteprima e non è stata ancora annunciata per la disponibilità generale per un uso di produzione.<br/><br/>Per informazioni dettagliate, vedere la pagina relativa all'[introduzione al mascheramento dei dati dinamici del database SQL di Azure](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***Gennaio 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Anteprima della sicurezza a livello di riga | **Attenzione:** la funzionalità di sicurezza a livello di riga è disponibile attualmente solo nello stato di *preview*, anche in aree geografiche in cui la versione 12 si trova nello stato di disponibilità generale (GA). Finché non è nello stato GA, la funzionalità di sicurezza a livello di riga non è idonea per essere usata in un database di produzione di importanza critica per un'attività.<br/><br/>La nuova istruzione CREATE SECURITY POLICY in T-SQL consente di implementare la sicurezza a livello di riga. Questa funzionalità fa sì che il server di database aggiunga condizioni che escludono alcune righe di dati prima che un set di righe venga restituito al chiamante.<br/><br/>Nel settore la sicurezza a livello di riga viene talvolta definita anche controllo dell'accesso con granularità fine.<br/><br/>Per un esempio di codice e altre informazioni, vedere la pagina relativa all'[anteprima della sicurezza a livello di riga](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dicembre 2014:*** |
| Funzioni finestra nelle query T-SQL | Le funzioni finestra ANSI sono accessibili con la [clausola OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan ha pubblicato un eccellente [post di blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) che illustra in dettaglio le funzioni finestra e la clausola OVER. |
| Integrazione con .NET CLR | Il CLR (Common Language Runtime) di .NET Framework è integrato nella versione 12. <br/><br/> Sono supportati solo gli assembly SAFE completamente compilati in codice binario. Per informazioni dettagliate, vedere [Restrizioni relative al modello di programmazione dell'integrazione con CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Per informazioni su questa funzionalità, vedere gli argomenti seguenti: <br/> * [Introduzione all'integrazione CLR di SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Rilevamento modifiche per i dati | È ora possibile configurare il rilevamento delle modifiche per i dati a livello di database o di tabella. <br/><br/> Per altre informazioni sul rilevamento delle modifiche, vedere [Informazioni sul rilevamento delle modifiche (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Indici XML | La versione 12 consente di usare le istruzioni T-SQL CREATE XML INDEX e CREATE SELECTIVE XML INDEX. <br/><br/> Per informazioni sugli indici XML, vedere gli argomenti seguenti: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [Creare, modificare o eliminare indici XML selettivi](http://msdn.microsoft.com/library/jj670109.aspx) |
| Tabella come heap | La versione 12 consente di creare una tabella senza indice cluster. <br/><br/> Questa funzionalità è particolarmente utile per il supporto dell'istruzione T-SQL SELECT...INTO, che consente di creare una tabella da un risultato di query. |
| Ruoli applicazione | Per il controllo della sicurezza e delle autorizzazioni, la versione 12 consente di eseguire istruzioni GRANT - DENY - REMOVE sui [ruoli applicazione](http://msdn.microsoft.com/library/ms190998.aspx). |


### 1.3 Categoria: migliore conoscenza dei clienti


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Dicembre 2014:*** |
| DMV (viste a gestione dinamica) | Nella versione 12 sono state aggiunte diverse DMV. Per informazioni dettagliate, vedere la [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Rilevamento modifiche | La versione 12 supporta completamente il rilevamento delle modifiche. <br/><br/> Per informazioni dettagliate su questa funzionalità, vedere [Abilitare e disabilitare il rilevamento delle modifiche (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### 1.4 Miglioramenti delle prestazioni al livello di servizio Premium


Questi miglioramenti delle prestazioni si applicano ai livelli P2 e P3 del livello di servizio Premium.


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Dicembre 2014:*** |
| Elaborazione parallela per le query | La versione 12 offre un grado superiore di parallelismo per le query che possono trarne vantaggio. |
| Minore latenza di I/O | La versione 12 offre una latenza molto minore per le operazioni di input/output. |
| Numero superiore di operazioni IOPS | La versione 12 è in grado di elaborare una quantità maggiore di input/output al secondo (IOPS). |
| Frequenza di registrazione | La versione 12 consente di registrare un numero maggiore di modifiche ai dati al secondo. |


### 1.5 Riepilogo dei miglioramenti


La versione 12 eleva il database SQL alla compatibilità funzionale pressoché completa con il prodotto SQL Server ed è incentrata sulle funzionalità più diffuse e sul supporto della programmabilità, per rendere lo sviluppo più efficiente e piacevole.  Spostare le applicazioni di database SQL nel cloud è ancora più semplice.


Al livello Premium, la versione 12 offre importanti miglioramenti delle prestazioni. In alcune applicazioni sarà possibile ottenere guadagni significativi della velocità delle query. Le applicazioni con carichi di lavoro intensi usufruiranno di una velocità effettiva elevata e affidabile.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. Stato di disponibilità generale (GA) della versione 12 per ogni area


La versione 12 del database SQL di Azure è stata rilasciata solo per l'anteprima e per i test di dicembre 2014 nelle aree indicate nella seguente tabella . Il passaggio dallo stato di anteprima a quello di disponibilità generale (GA) completa si verifica in date diverse a seconda dell'area. La seguente tabella indica lo stato corrente del rilascio della versione 12 per ogni area.


> [AZURE.NOTE]
> I [prezzi](http://azure.microsoft.com/pricing/details/sql-database/) durante l'anteprima sono stati scontati. Torneranno al livello GA per tutte le aree il 31 marzo 2015.


| Area di Azure | Stato corrente del rilascio<br/>della versione 12 | Data di innalzamento di livello<br/>a GA |
| :--- | :--- | :--- |
| Stati Uniti centro-meridionali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti centrali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti centro-settentrionali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti occidentali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti orientali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti orientali 2 | Disponibilità generale (GA) | 9 febbraio 2015 |
| Asia orientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Asia sudorientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Giappone occidentale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Giappone orientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Europa settentrionale | Disponibilità generale (GA) | 29 gennaio 2015 |
| Europa occidentale | Disponibilità generale (GA) | 29 gennaio 2015 |
| Brasile meridionale | Non disponibile | Previsione per il terzo trimestre 2015 |
| Australia orientale | Anteprima | Previsione per il secondo trimestre 2015 |
| Australia sudorientale | Anteprima | Previsione per il secondo trimestre 2015 |


Per le aree in cui è stato raggiunto il livello GA, tutte le nuove sottoscrizioni e i relativi database usano l'architettura di servizi versione 12 e pertanto hanno accesso alle funzionalità più recenti. Il presente articolo elenca le nuove funzionalità introdotte con la versione 12.


A livello di disponibilità generale (GA), i clienti con server e database antecedenti alla versione 12 possono scegliere di aggiornare (o spostare) i database all'architettura di servizi versione 12 per usare queste nuove funzionalità per la produzione. I database versione 12 devono essere al [livello dei prezzi](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) di base, standard o premium.


## 3. Come procedere


Per informazioni su come aggiornare il database dalla versione 11 alla versione 12 del database SQL di Azure, vedere [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL di Azure (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


I numeri di versione, come 12, fanno riferimento al valore restituito dalla seguente istruzione Transact-SQL:<br/>
**SELECT @@version;**


- 11.0.9228.18 *(11)*
- 12.0.2000.8 *(o appena superiore, 12)*


*Sconto:* durante il periodo di anteprima della versione 12 è possibile usare tale versione a un prezzo scontato. Il nuovo livello S3 offre 100 DTU (unità di velocità effettiva del database) e tutte le funzionalità disponibili nel livello Standard per $ 0,2016 l'ora. Nel periodo di anteprima, il prezzo è scontato a $ 0,1008 l'ora. Per altre informazioni, vedere la pagina relativa ai [prezzi del database SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## 4. Avvertenze per l'anteprima 12


Tenere conto delle seguenti avvertenze riguardanti l'aggiornamento e il post-aggiornamento per la versione 12 del database SQL di Azure (anteprima).


### 4.1 Portale di anteprima per la versione 12


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


### 4.2 Avvertenze *during* l'aggiornamento alla versione 12


> [AZURE.NOTE]
> Il database antecedente alla versione 12 *remains available* per l'accesso ai dati durante l'aggiornamento alla versione 12.


- Per i database di dimensioni maggiori di 50 GB, l'aggiornamento alla versione 12 può richiedere fino a 24 ore.
- Durante l'aggiornamento di un database a un server della versione 12 di database SQL di Azure non è possibile eseguire le seguenti operazioni sul server finché non viene completato l'aggiornamento:
 - Creazione di un nuovo database
 - Copia di un database nel server
 - Ripristino di un database eliminato
 - Ripristino di un database nello stato in cui trovava in un determinato momento
 - Replica geografica
- Dal momento in cui viene completato l'aggiornamento alla versione 12, il sistema richiede alcuni minuti per aggiornare la voce Domain Name System (DNS) corrispondente al database versione 12. L'applicazione client può connettersi al database dopo l'aggiornamento del DNS.
- I livelli di prezzo Web e Business non sono supportati nella versione 12 e non saranno supportati nelle versioni future.


### 4.3 Avvertenze *after* l'aggiornamento alla versione 12


- Non è possibile ripristinare la versione precedente dei database aggiornati sul posto alla versione 12.
- Lo sconto del 50% per la versione 12 del database SQL di Azure è attivo in tutte le aree geografiche fino alla scadenza di martedì 31 marzo 2015. Lo sconto è valido per le aree in cui sono disponibili gli stati di anteprima e di disponibilità generale.


### 4.4 Esportazione e importazione *after* l'aggiornamento alla versione 12


È possibile esportare o importare un database versione 12 usando il [portale Web Azure](http://portal.azure.com/). In alternativa, è possibile esportare o importare usando uno dei seguenti strumenti:


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Tuttavia, per usare gli strumenti, è innanzitutto necessario installare gli aggiornamenti più recenti per assicurarsi che supportino le nuove funzionalità della versione 12:


- [Pacchetto di aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Aggiornamento di febbraio 2015 di SQL Server Data Tools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Aggiornamento di febbraio 2015 di Data-Tier Application Framework (DacFx) per il database SLQ di Azure versione 12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] I precedenti collegamenti sono stati aggiornati il giorno 2 marzo 2015 o in data successiva. Si consiglia di usare gli aggiornamenti più recenti di questi strumenti.


[2. Stato di disponibilità generale (GA) della versione 12 per ogni area]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 