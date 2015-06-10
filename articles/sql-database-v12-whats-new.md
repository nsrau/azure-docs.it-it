<properties 
	pageTitle="Novità della versione 12 del database SQL" 
	description="Vengono descritte le funzionalità più recenti che sono state aggiunte al database SQL di Azure per la versione 12, ma solo fino a maggio 2015." 
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
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# Novità della versione 12 del database SQL


La versione 12 del database SQL di Azure ([in anteprima in alcune aree geografiche](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) ora fornisce quasi una completa compatibilità con il motore di Microsoft SQL Server. I recenti miglioramenti consentono di semplificare le migrazioni delle applicazioni di SQL Server al database SQL e consentono il sistema di elaborare in modo affidabile carichi di lavoro di database complessi.


[Iscriversi](https://portal.azure.com) e ottenere un database SQL per [iniziare a utilizzare](sql-database-get-started.md) questa nuova generazione in Microsoft Azure. È necessaria innanzitutto una sottoscrizione a Microsoft Azure. È possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database).


Il percorso per la pianificazione e l'aggiornamento dei database da una versione precedente alla versione 12 inizia dalla pagina [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md).


> [AZURE.TIP]La pagina Web principale per informazioni sulle nuove funzionalità del database SQL di Azure è la pagina Web "Aggiornamenti dei servizi", in [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/). Nella pagina Web gli aggiornamenti dei servizi nel controllo**Servizi** controllo selezionare **Database SQL**.


## Caratteristiche in primo piano


- Il **Portale di anteprima di Azure** è [disponibile](http://portal.azure.com/) per creare database e server SQL nella versione 12 o, facoltativamente, in una versione precedente. Nel Portale di anteprima di Azure è possibile specificare un database SQL, quindi indicare un server che possa ospitare il database SQL.
 - Nell'aprile del 2015 la [versione precedente del portale di Azure](http://manage.windowsazure.com/) è ancora supportata.


- **Scegliere una versione** del server di database SQL quando si utilizza il portale di anteprima di Azure per creare un nuovo database. Il valore predefinito è V12, ma è possibile scegliere la versione precedente del server di database SQL.


- La **sicurezza** risulta migliorata grazie alla nuova funzionalità relativa agli [utenti in database indipendenti](sql-database-v12-whats-new.md#UsersInContainedDatabases). Altre due funzionalità sono rappresentate dalla [sicurezza a livello di riga](sql-database-v12-whats-new.md#RowLevelSecurity) e dal [mascheramento dei dati dinamici](sql-database-v12-whats-new.md#DynamicDataMasking), benché entrambe le funzionalità si trovino ancora in stato di anteprima e non di disponibilità generale.


- **Gestione semplificata**  di database di grandi dimensioni per il supporto di carichi di lavoro più pesanti con query parallele (solo livello Premium), [partizionamento delle tabelle](http://msdn.microsoft.com/library/ms187802.aspx), [indicizzazione online](http://msdn.microsoft.com/library/ms188388.aspx), ricompilazione senza problemi di indici di grandi dimensioni grazie alla rimozione del limite di 2 GB e altre opzioni per il comando [ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx).


- **Supporto delle funzioni chiave di programmabilità** per una progettazione delle applicazioni più affidabile grazie all'[integrazione con CLR](http://msdn.microsoft.com/library/ms189524.aspx), alle [funzioni finestra](http://msdn.microsoft.com/library/ms189461.aspx) Transact-SQL, agli [indici XML](http://msdn.microsoft.com/library/bb934097.aspx) e al [rilevamento delle modifiche](http://msdn.microsoft.com/library/bb933875.aspx) per i dati.


- **Prestazioni all'avanguardia** con supporto di query di [indicizzazione columnstore](http://msdn.microsoft.com/library/gg492153.aspx) in memoria (solo livello Premium) per data mart e piccoli carichi di lavoro di analisi.


- **Monitoraggio e risoluzione dei problemi** migliorati con visibilità su oltre 100 nuove visualizzazioni tabella in un ampio set di viste di gestione database ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)).


- Il **nuovo livello di prestazioni S3 per il livello Standard:** offre una maggiore flessibilità di [prezzo](sql-database-upgrade-new-service-tiers.md) tra i livelli Standard e Premium. Il livello S3 fornirà più DTU (unità di velocità effettiva del database) e tutte le funzionalità disponibili nel livello Standard.


## Miglioramenti nella versione 12: gestione estesa del database


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***1 maggio 2015*** |
| PowerShell per l'aggiornamento alla versione 12 dalla versione precedente | Sono disponibili nuovi cmdlet di Powershell per avviare, annullare o monitorare un aggiornamento al database SQL di Azure versione 12 dalla versione 11 o qualsiasi altra versione precedente alla versione 12.<br/><br/>Per la documentazione di riferimento sui cmdlet, vedere:<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***Aprile 2015:*** |
| TRANSPARENT DATA ENCRYPTION | La funzionalità Transparent Data Encryption (anteprima) consente di proteggersi da attività dannose eseguendo in tempo reale crittografia e decrittografia dei file di log delle transazioni inattivi, dei database e dei backup associati. La funzionalità Transparent Data Encryption non richiede modifiche all'applicazione.<br/><br/>Per ulteriori informazioni, vedere:<br/> [Transparent Data Encryption il database SQL di Azure](http://msdn.microsoft.com/library/dn948096.aspx) -Istruzioni dettagliate.<br/>- [Transparent Data Encryption (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) -Descrizione generale. |
| Funzioni di crittografia, chiavi e certificati di Transact-SQL | Certificati, chiavi simmetriche e chiavi asimmetriche possono ora essere create nella versione 12 del database SQL di Azure. Oggi sono supportate la maggior parte delle funzioni di crittografia.<br/><br/>Per ulteriori informazioni, vedere:<br/>- [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [Funzioni di crittografia (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| Pool elastico di database SQL | Le attività semplici divengono complesse quando si passa a migliaia di database e questo è l'aspetto negativo della crescita esponenziale. Gli sviluppatori SaaS passano molte ore a scrivere procedure logiche complesse per gestire le modifiche dello schema e altre operazioni amministrative con la crescita dell'azienda.<br/><br/>I [database elastici](sql-database-elastic-pool.md) semplificano la vita. Eseguire uno script, inviarlo come un processo e il database SQL di Azure farà il resto. |
| Indicazioni di livello dei prezzi e STA | Quando si passa il database dalle edizioni Web o Business a uno dei nuovi livelli di servizio Basic, Standard o Premium, è ora possibile ottenere indicazioni sui livelli dei prezzi direttamente dal portale di anteprima di Azure.<br/><br/>Il servizio del database SQL di Azure consente di analizzare i requisiti in termini di prestazioni e funzionalità per i database esistenti. Il [Service Tier Advisor (STA)](sql-database-service-tier-advisor.md) legge i dati sulle prestazioni per consigliare il livello di servizio ottimale per il database. |
| Autorizzazioni DMV | Per diverse DMV (viste a gestione dinamica), l'esecuzione in precedenza richiedeva l'autorizzazione VIEW SERVER STATE. Ora nella versione 12 del database SQL di Azure, in molti casi le DMV possono essere eseguite dall'account di amministratore del database SQL nel database al livello di servizio Basic o Standard.<br/><br/>Nel livello Premium, è possibile concedere l'accesso per le DMV ad altri utenti tramite l'autorizzazione VIEW DATABASE STATE nel database.<br/><br/>Per ulteriori informazioni vedere la sezione relativa alle autorizzazioni per un determinato individuo [DMV](http://msdn.microsoft.com/library/ms188754.aspx). |
| DBCC SQLPERF | Il comando [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) è ora disponibile nella versione 12 del database SQL di Azure. Il *attesa di reimpostazione* e *latch statistiche* opzioni non sono supportate in SQL Database V12. |
| . | ***Dicembre 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Utenti contenuti nei database | È ora possibile creare utenti in un database indipendente senza dover disporre di un account di accesso corrispondente nel database master. Questi sono denominati *utenti contenuti*. In questo modo lo spostamento di un database in un altro server risulta notevolmente semplificato. Il codice di connessione nelle applicazioni client rimane lo stesso a prescindere se si utilizzano o meno utenti di database contenuti.<br/><br/>L'utilizzo di questa funzionalità rappresenta un ottimo modo per trarre vantaggio dai contratti di servizio con garanzie più elevate.<br/><br/>Si consiglia in genere di utilizzare questa funzionalità. Tuttavia, potrebbero proporsi scenari specifici che rendono la tradizionale combinazione *accesso + utente* la scelta migliore in un determinato momento.<br/><br/>Per ulteriori informazioni, vedere:<br/>- [Linee guida e limitazioni per la sicurezza per il database SQL di Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [ Gestione di database e account di accesso in database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Database indipendenti](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partizionamento delle tabelle | Le precedenti limitazioni sul [partizionamento delle tabelle](http://msdn.microsoft.com/library/ms190787.aspx) sono state eliminate. |
| Transazioni di dimensioni maggiori | Con la versione 12 non esiste più il limite di 2 GB per le modifiche dei dati in un'unica transazione. <br/><br/> Uno dei vantaggi è che la ricompilazione degli indici di grandi dimensioni non è più vincolata al limite di 2 GB per la dimensione delle transazioni. Per informazioni generali, vedere [Limiti delle risorse del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Compilazione e ricompilazione dell'indice online | Prima della versione 12, il database SQL di Azure supportava a livello generale la clausola (ONLINE = ON) dell'istruzione [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx), che non era però supportata per gli indici in una colonna di tipo BLOB. La versione 12 supporta (ONLINE = ON) anche per gli indici nelle colonne BLOB.<br/><br/> La funzionalità ONLINE consente l'uso di indici nelle query anche durante la ricompilazione dell'indice. |
| Supporto di CHECKPOINT | La versione 12 consente di eseguire l'istruzione [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) Transact-SQL sul database. |
| Miglioramento di ALTER TABLE | Consente l'esecuzione di diverse azioni di modifica delle colonne mentre la tabella rimane disponibile. Per altre informazioni, vedere [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Miglioramento di TRUNCATE TABLE | Consente il troncamento di partizioni specifiche. Per altre informazioni, vedere [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Ulteriori opzioni per ALTER DATABASE | La versione 12 supporta un numero superiore di opzioni per il comando [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx). <br/><br/> Per altre informazioni, vedere [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Ulteriori comandi DBCC | Nella versione 12 sono disponibili diversi nuovi comandi [DBCC](http://msdn.microsoft.com/library/ms188796.aspx). Per informazioni dettagliate, vedere la [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


## supporto programmazione e applicazioni


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***1 maggio 2015*** |
| Dimensioni dell'indice aumentate | Nella versione 12 del database SQL, fino a 32 colonne possono essere combinate in una chiave di indice composto. La dimensione massima consentita dei valori di indice è 900 byte per un indice cluster o 1700 byte per un indice non cluster.<br/><br/>I limiti sono di 16 colonne e 900 byte per la versione 11 e altre versioni precedenti alla versione 12 del database SQL. |
| . | ***Aprile 2015:*** |
| Anteprima di ricerca full-Text | La [Ricerca full-Text (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) consente di eseguire una query su colonne basate su caratteri in modo più efficace rispetto all'operatore LIKE. Ad esempio:<br/><br/>-FREETEXT: ricerche di frasi che soddisfano il *significato* della frase di ricerca, anche quando non corrisponde all'esatta formulazione.<br/>-CONTAINS: cerca i valori delle celle che contengono due termini di ricerca in *prossimità fisica* reciproca.<br/><br/>**NOTA:** questa funzionalità è in stato di anteprima e non è stata ancora annunciata per la disponibilità generale per un uso di produzione. La funzionalità di anteprima della gamma di funzioni di ricerca è un subset dell'intervallo FTS in Microsoft SQL Server. |
| . | ***Febbraio 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Anteprima di mascheramento dei dati dinamici | Quando viene generato un set di righe da una query, un criterio di mascheramento dei dati definito può sostituire parti dei dati con caratteri 'X' per nascondere e proteggere informazioni riservate. Al termine dell'operazione di mascheramento, il set di righe modificate viene inviato al client.<br/><br/>Un esempio di utilizzo potrebbe essere il mascheramento di tutte tranne le ultime cifre di un numero di carta di credito.<br/><br/>**NOTA: ** questa funzionalità è in stato di anteprima e non è stata ancora annunciata per la disponibilità generale per un uso di produzione.<br/><br/>Per informazioni dettagliate, vedere la pagina relativa all'[introduzione al mascheramento dei dati dinamici del database SQL di Azure.](sql-database-dynamic-data-masking-get-started.md). |
| . | ***Gennaio 2015::*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Anteprima della sicurezza a livello di riga | Il nuovo comando [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) in Transact-SQL consente di implementare la sicurezza a livello di riga. Questa funzionalità fa sì che il server di database aggiunga condizioni che escludono alcune righe di dati prima che un set di righe venga restituito al chiamante.<br/>Nel settore la sicurezza a livello di riga viene talvolta definita anche controllo dell'accesso con granularità fine.<br/><br/><br/>**NOTA:** questa funzionalità è in stato di anteprima e non è stata ancora annunciata per la disponibilità generale per un uso di produzione.<br/><br/>Per un esempio di codice e altre informazioni, vedere la pagina relativa all'[anteprima della sicurezza a livello di riga](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dicembre 2014:*** |
| Funzioni finestra nelle query Transact-SQL | Le funzioni finestra ANSI sono accessibili con la [clausola OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan ha pubblicato un eccellente [post di blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) che illustra in dettaglio le funzioni finestra e la clausola OVER. |
| Integrazione con .NET CLR | Il CLR (Common Language Runtime) di .NET Framework è integrato nella versione 12. <br/><br/> Sono supportati solo gli assembly SAFE completamente compilati in codice binario. Per informazioni dettagliate, vedere [Restrizioni relative al modello di programmazione dell'integrazione con CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> È possibile trovare informazioni su questa funzionalità negli argomenti seguenti: <br/> * [Introduzione all'integrazione CLR di SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Rilevamento modifiche per i dati | È ora possibile configurare il rilevamento delle modifiche per i dati a livello di database o di tabella. <br/><br/> Per altre informazioni sul rilevamento delle modifiche, vedere [Informazioni sul rilevamento delle modifiche (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Indici XML | La versione 12 consente di usare le istruzioni Transact-SQL [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) e [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx). |
| Tabella come heap | La versione 12 consente di creare una tabella senza indice cluster. <br/><br/> Questa funzionalità è particolarmente utile per il supporto dell'istruzione Transact-SQL [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx), che consente di creare una tabella da un risultato di query. |
| Ruoli applicazione | Per il controllo della sicurezza e delle autorizzazioni, la versione 12 consente di eseguire comandi [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) e sui [ruoli applicazione](http://msdn.microsoft.com/library/ms190998.aspx). |


## migliore conoscenza dei clienti


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Dicembre 2014:*** |
| DMV (viste a gestione dinamica) | Nella versione 12 sono state aggiunte diverse DMV. Per informazioni dettagliate, vedere la [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Rilevamento modifiche | La versione 12 supporta completamente il rilevamento delle modifiche. <br/><br/>Per informazioni dettagliate su questa funzionalità, vedere [Abilitare e disabilitare il rilevamento delle modifiche (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |
| Indici ColumnStore | Un indice columnstore migliora le prestazioni di sistema dei data warehouse quando una colonna indicizzata contiene ripetizioni dello stesso valore. L'[indice columnstore comprime](http://msdn.microsoft.com/library/gg492088.aspx) i valori duplicati per ridurre il numero di righe fisiche accessibili durante le query. |


## Miglioramento delle prestazioni al livello di servizio Premium


Questi miglioramenti delle prestazioni si applicano ai livelli P2 e P3 del livello di servizio Premium.


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Dicembre 2014:*** |
| Elaborazione parallela per le query | La versione 12 offre un grado superiore di parallelismo per le query che possono trarne vantaggio. |
| Minore latenza di I/O | La versione 12 offre una latenza molto minore per le operazioni di input/output. |
| Numero superiore di operazioni IOPS | La versione 12 è in grado di elaborare una quantità maggiore di input/output al secondo (IOPS). |
| Frequenza di registrazione | La versione 12 consente di registrare un numero maggiore di modifiche ai dati al secondo. |


## La versione 12 diventa la versione predefinita dell'API a partire dal 1 agosto 2015  


| Funzionalità | Descrizione |
| :--- | :--- |
| . | ***Agosto 2015:*** |
| Creare server utilizzando REST o PowerShell | Quando si crea un server senza specificare una versione del server, la versione predefinita cambierà da V11 in V12.<br/><br/>Ciò è in linea con il [Portale di anteprima di Azure](http://portal.azure.com). |
| Creare database utilizzando Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) o [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) | Nei server versione 11, quando si crea un nuovo database senza specificare un'edizione o l'obiettivo, l'obiettivo di servizio predefinito sarà [S0](http://azure.microsoft.com/pricing/details/sql-database/) anziché Web e Business. Ciò è in linea con i server versione 12 nel Portale di anteprima di Azure. |


## Riepilogo dei miglioramenti


La versione 12 eleva il database SQL alla compatibilità funzionale pressoché completa con il prodotto SQL Server ed è incentrata sulle funzionalità più diffuse e sul supporto della programmabilità, per rendere lo sviluppo più efficiente e piacevole. Spostare le applicazioni di database SQL nel cloud è ancora più semplice.


Al livello Premium, la versione 12 offre importanti miglioramenti delle prestazioni. In alcune applicazioni sarà possibile ottenere guadagni significativi della velocità delle query. Le applicazioni con carichi di lavoro intensi usufruiranno di una velocità effettiva elevata e affidabile.


## <a name="V12AzureSqlDbPreviewGaTable"></a>Stato di disponibilità generale (GA) della versione 12 per ogni area


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) I prezzi durante l'anteprima sono stati scontati. Torneranno al livello GA per tutte le aree il 31 marzo 2015.


| Area di Azure | Versione corrente<br/>stato della versione 12 | Data di innalzamento al livello<br/>di disponibilità generale |
| :--- | :--- | :--- |
| Stati Uniti centro-meridionali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti centrali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti centro-settentrionali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti occidentali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti Orientali | Disponibilità generale (GA) | 9 febbraio 2015 |
| Stati Uniti orientali 2 | Disponibilità generale (GA) | 9 febbraio 2015 |
| Asia orientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Asia sudorientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Giappone occidentale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Giappone orientale | Disponibilità generale (GA) | 24 febbraio 2015 |
| Europa settentrionale | Disponibilità generale (GA) | 29 gennaio 2015 |
| Europa occidentale | Disponibilità generale (GA) | 29 gennaio 2015 |
| Brasile meridionale | Disponibilità generale (GA) | Il 21 aprile 2015 |
| Australia orientale | Anteprima | Previsione per il secondo trimestre 2015 |
| Australia sudorientale | Anteprima | Previsione per il secondo trimestre 2015 |


Per le aree in cui è stato raggiunto il livello GA, tutte le nuove sottoscrizioni e i relativi database usano l'architettura di servizi versione 12 e pertanto hanno accesso alle funzionalità più recenti. Il presente articolo elenca le nuove funzionalità introdotte con la versione 12.


Per tutte le aree n anteprima e non ancora in disponibilità generale, è necessario [attivare l'opzione per utilizzare i database versione 12](sql-database-v12-sign-up.md).


Nello stato di disponibilità generale, se si dispone di server e database con la versione precedente alla versione 12, è possibile aggiornare i database (o spostare)all'architettura di servizio della versione 12. È quindi possibile utilizzare le nuove funzionalità per la produzione. I database versione 12 devono essere al [livello di prezzo](sql-database-upgrade-new-service-tiers.md) Basic, Standard o Premium.


## Come procedere


Per informazioni su come aggiornare il database dalla versione 11 alla versione 12 del database SQL di Azure, vedere  [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL di Azure](sql-database-v12-plan-prepare-upgrade.md).


I numeri di versione, come 12, fanno riferimento al valore restituito dalla seguente istruzione Transact-SQL:<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(o appena superiore, V12)*


Per la versione più recente dei prezzi della versione 12, vedere  [Database SQL - Prezzi](http://azure.microsoft.com/pricing/details/sql-database/).


## Avvertenze per l'aggiornamento versione 12


È necessario tenere presente le limitazioni durante e dopo un aggiornamento del database versione 11 alla versione 12. È possibile leggere i dettagli relativi a questo collegamento per un [punto intermedio](sql-database-v12-plan-prepare-upgrade.md#limitations) nell'argomento *Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL*.


## Altre fonti di informazioni più recenti


Questo argomento relativo alle *Novità* della versione 12 del database SQL di Azure verrà finalizzato e non aggiornato, probabilmente dopo il 30 aprile 2015. Le informazioni sulle nuove funzionalità e altri annunci si stanno trasferendo al collegamento seguente:


- [Annunci](http://azure.microsoft.com/updates/?service=sql-database) per il database SQL di Azure nella pagina Web **Aggiornamenti dei servizi**.
 - È possibile scegliere l'icona **RSS** nella pagina Web, quando il controllo dei servizi è impostato su *Database SQL*.
- Seguici su Twitter: **@SQLTechCenter**.


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58-->