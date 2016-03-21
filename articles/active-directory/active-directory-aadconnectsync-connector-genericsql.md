<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: connettore SQL generico | Microsoft Azure"
   description="Questo articolo descrive come configurare il connettore Generic SQL di Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# Documentazione tecnica sul connettore Generic SQL

Questo articolo descrive il connettore Generic SQL ed è applicabile ai prodotti seguenti:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario usare l'hotfix 4.1.3461.0 o versione successiva ([KB2870703](https://support.microsoft.com/kb/2870703)).

Per MIM2016 e FIM2010R2 il connettore è disponibile come download dall'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Per vedere come funziona questo connettore, vedere l'articolo relativo alle [istruzioni dettagliate per il connettore SQL generico](active-directory-aadconnectsync-connector-genericsql-step-by-step.md).

## Panoramica del connettore Generic SQL

Il connettore Generic SQL consente di integrare il servizio di sincronizzazione con un sistema di database che offre la connettività ODBC.

A livello generale, le funzionalità seguenti sono supportate dalla versione corrente del connettore:

| Funzionalità | Supporto |
| --- | --- |
| Origine dati connessa | Il connettore è supportato con tutti i driver ODBC a 64 bit. È stato testato con quanto segue: <li>Microsoft SQL Server e SQL Azure</li><li>IBM DB2 10. x</li><li>IBM DB2 9</li><li>Oracle 10 & 11g</li><li>MySQL 5. x</li>
| Scenari | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione delle password</li> |
| Operazioni | <li>Importazione completa e delta, esportazione</li><li>Per l'esportazione: aggiunta, eliminazione, aggiornamento e sostituzione</li><li>Impostazione di password, modifica di password</li>
| Schema | <li>Individuazione dinamica di oggetti e attributi</li>

### Prerequisiti

Prima di usare il connettore, verificare che nel server di sincronizzazione sia disponibile quanto segue, oltre a eventuali hotfix indicati in precedenza:

- Microsoft .NET 4.5.2 Framework o versione successiva
- Driver client ODBC a 64 bit

### Autorizzazioni nell'origine dati connessa

Per creare o eseguire una delle operazioni supportate nel connettore Generic SQL, è necessario avere:

- db\_datareader
- db\_datawriter

### Porte e protocolli

Per le porte necessarie per il funzionamento del driver ODBC, vedere la documentazione del fornitore del database.

## Creare un nuovo connettore

Per creare un connettore Generic SQL, in **Synchronization Service** selezionare **Management Agent** e quindi **Create**. Selezionare il connettore **Generic SQL (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### Connettività

Il connettore usa un file DSN ODBC per la connettività. Creare il file DSN tramite **Origini dati ODBC** disponibile nel menu Start in **Strumenti di amministrazione**. Nello strumento di amministrazione creare un **DSN file** da passare al connettore.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

La schermata Connectivity è la prima visualizzata quando si crea un nuovo connettore Generic SQL. Prima di tutto è necessario specificare le informazioni seguenti:

- Percorso del file DSN
- Autenticazione
    - User Name
    - Password

Il database deve supportare uno dei metodi di autenticazione riportati di seguito.

- **Autenticazione di Windows**: il database di autenticazione userà le credenziali di Windows per verificare l'utente. Il nome utente e la password specificati verranno utilizzati per l'autenticazione nel database. Questo account dovrà avere autorizzazioni sul database.
- **Autenticazione SQL**: il database di autenticazione userà il nome utente e la password definiti nella schermata Connectivity per connettersi al database. Se si archiviano il nome utente e la password nel file DSN, le credenziali fornite nella schermata Connectivity hanno la precedenza.
- **Autenticazione del database SQL di Azure**: per altre informazioni, vedere [Connettersi al database SQL con l'autenticazione di Azure Active Directory](..\sql-database\sql-database-aad-authentication.md)

**DN is Anchor**: se si seleziona questa opzione, il DN verrà usato anche come attributo di ancoraggio. Può essere usato per un'implementazione semplice, ma presenta anche le limitazioni seguenti:

-	Il connettore supporta solo 1 tipo di oggetto. Di conseguenza gli attributi di riferimento possono fare riferimento solo allo stesso tipo di oggetto.

**Export Type: Object Replace**: durante l'esportazione se solo alcuni attributi sono stati modificati, verrà esportato l'intero oggetto, con tutti gli attributi, e sostituirà l'oggetto esistente.

### Schema 1 (rilevare i tipi di oggetto)

In questa pagina si configura il modo in cui il connettore troverà i diversi tipi di oggetto nel database.

Ogni tipo di oggetto verrà presentato come una partizione e configurato ulteriormente in **Configure Partitions and Hierarchies**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Object Type detection method**: il connettore supporta questi metodi di rilevamento del tipo di oggetto.

- **Fixed Value**: specificare l'elenco dei tipi di oggetto con un elenco con valori delimitati da virgole. Ad esempio, User,Group,Department. ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Table/View/Stored Procedure**: fornire il nome della tabella/vista/stored procedure, quindi il nome della colonna che fornirà l'elenco dei tipi di oggetto. Se si usa una stored procedure, fornire anche i relativi parametri nel formato **[Nome]:[Direzione]:[Valore]**. Specificare ogni parametro su una riga separata (usare CTRL+INVIO per ottenere una nuova riga). ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL Query**: questa opzione consente di fornire una query SQL che restituisce una singola colonna con i tipi di oggetto, ad esempio `SELECT [Column Name] FROM TABLENAME`. La colonna restituita deve essere di tipo stringa (varchar).

### Schema 2 (rilevare i tipi di attributo)

In questa pagina si configura in che modo verranno rilevati i nomi e i tipi di attributi. Le opzioni di configurazione sono elencate per ogni tipo di oggetto rilevato nella pagina precedente.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Attribute Type detection method**: il connettore supporta questi metodi di rilevamento del tipo di attributo con ogni tipo di oggetto rilevato nella schermata di Schema 1.

- **Table/View/Stored Procedure**: specificare il nome della tabella/vista/stored procedure da usare per trovare i nomi di attributo. Se si usa una stored procedure, fornire anche i relativi parametri nel formato **[Nome]:[Direzione]:[Valore]**. Specificare ogni parametro su una riga separata (usare CTRL+INVIO per ottenere una nuova riga). Per rilevare i nomi degli attributi in un attributo multivalore, fornire un elenco di tabelle o viste separate da virgole. Gli scenari multivalore non sono supportati se le tabelle padre e figlio hanno gli stessi nomi di colonna.
- **SQL Query**: questa opzione consente di fornire una query SQL che restituisce una singola colonna con nomi di attributo, ad esempio `SELECT [Column Name] FROM TABLENAME`. La colonna restituita deve essere di tipo stringa (varchar).

### Schema 3 (definire ancoraggio e DN)

Questa pagina consente di configurare l'attributo di ancoraggio e DN per ogni tipo di oggetto rilevato. È possibile selezionare più attributi per rendere univoco l'ancoraggio.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Non sono elencati gli attributi multivalore e booleani.
- Lo stesso attributo non può essere usato per DN e ancoraggio, a meno che nella pagina Connectivity non sia selezionata l'opzione **DN is Anchor**.
- Se **DN is Anchor** è selezionata nella pagina Connectivity, questa pagina richiede solo l'attributo DN. Questo attributo verrà usato anche come attributo di ancoraggio. ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### Schema 4 (definire tipo di attributo, riferimento e direzione)

Questa pagina consente di configurare il tipo di attributo come valore integer, riferimento, stringa, binario o booleano e la direzione per ogni attributo. Tutti gli attributi della pagina **Schema 2** sono elencati con i relativi attributi multivalore.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **DataType**: usato per associare il tipo di attributo a quelli noti al motore di sincronizzazione. Per impostazione predefinita, viene usato lo stesso tipo, come rilevato in SQL schema, ma DateTime e il Reference non sono facilmente rilevabili. Per questi è necessario specificare **DateTime** o **Reference**.
- **Direction**: è possibile impostare la direzione dell'attributo su Import, Export o ImportExport. ImportExport è il valore predefinito. ![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Note:

- Se un tipo di attributo non è rilevabile dal connettore, usa il tipo di dati stringa.
- **Nested tables** possono essere considerate tabelle di database di una colonna. Oracle archivia le righe di una tabella annidata senza un ordine particolare. Tuttavia, quando si recupera la tabella annidata in una variabile PL/SQL, alle righe vengono assegnati pedici consecutivi a partire da 1. In questo modo si avrà un accesso di tipo matrice alle singole righe.
- **VARRYS** non sono supportati nel connettore.

### Schema 5 (definire la partizione per gli attributi di riferimento)

In questa pagina si configura, per tutti gli attributi di riferimento, a quale partizione, ad esempio tipo di oggetto, fa riferimento un attributo.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se si usa **DN is anchor** è necessario usare lo stesso tipo di oggetto da cui si fa riferimento. Non è possibile fare riferimento a un altro tipo di oggetto.

### Parametri globali

La pagina Global Parameters viene usata per configurare l'importazione delta, il formato di data/ora e il metodo di password.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Il connettore Generic SQL supporta i seguenti metodi per l'importazione delta:

- **Trigger**: vedere l'articolo relativo alla [generazione di visualizzazioni delta con trigger](https://technet.microsoft.com/library/cc708665.aspx).
- **Watermark**: questo è un approccio generico e può essere usato con qualsiasi database. L'opzione Water Mark Query è già popolata in base al fornitore del database. In ogni tabella o visualizzazione usata deve essere presente una colonna Watermark. Deve tenere traccia di inserimenti e aggiornamenti delle tabelle, nonché delle tabelle dipendenti (multivalore o figlio). Gli orologi tra il server di database e il servizio di sincronizzazione devono essere sincronizzati. In caso contrario, potrebbero essere omesse alcune voci nell'importazione delta. Limitazione:
    - La strategia Watermark non supporta gli oggetti eliminati.
- **Snapshot** (funziona solo con Microsoft SQL Server) [Generazione di visualizzazioni delta con snapshot](https://technet.microsoft.com/library/cc720640.aspx)
- **Change Tracking** (funziona solo con Microsoft SQL Server) [Informazioni sul rilevamento delle modifiche (SQL Server)](https://msdn.microsoft.com/library/bb933875.aspx) Limitazioni:
    - L'attributo Anchor & DN deve essere parte della chiave primaria per l'oggetto selezionato nella tabella.
    - La query SQL non è supportata durante l'importazione e l'esportazione con il rilevamento delle modifiche.

**Additional Parameters**: specificare il fuso orario del server di database che indica dove si trova il server di database. Questo valore viene usato per supportare il formato degli attributi di data e ora.

Il connettore archivierà sempre data e data-ora in formato UTC. Per poter convertire correttamente la data e ora, è necessario specificare il fuso orario del server di database e il formato usato. Il formato deve essere espressa nel formato .Net.

Durante l'esportazione è necessario fornire al connettore ogni attributo di data e ora in formato UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Password Configuration**: il connettore fornisce funzionalità di sincronizzazione password e supporta l'impostazione e la modifica della password.

Il connettore fornisce due metodi per supportare la sincronizzazione password:

- **Stored Procedure**: questo metodo richiede due stored procedure per supportare l'impostazione e la modifica della password. Digitare tutti i parametri per aggiungere e modificare l'operazione di password impostare rispettivamente Set Password SP e Change Password SP Parameters come nell'esempio seguente. ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Password Extension**: questo metodo richiede la DLL di estensione delle password. È necessario fornire il nome della DLL di estensione che implementa l'interfaccia [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx). L'assembly di estensione della password deve essere inserito nella cartella dell'estensione in modo che il connettore possa caricare la DLL in fase di esecuzione. ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

È anche necessario attivare la gestione delle password nella pagina **Configure Extension**. ![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### Configurare partizioni e gerarchie

Nella pagina di configurazione di partizioni e gerarchie selezionare tutti i tipi di oggetto. Ogni tipo di oggetto si trova nella propria partizione.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

È anche possibile sostituire i valori definiti nella pagina **Connectivity** o **Global Parameters**.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### Configurare gli ancoraggi

Questa pagina è di sola lettura perché l'ancoraggio è già stato definito. L'attributo di ancoraggio selezionato viene sempre aggiunto con il tipo di oggetto in modo che rimanga univoco in tutti i tipi di oggetto.

![ancoraggi](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## Configurare il parametro per l'esecuzione dei passaggi

Questi passaggi vengono configurati nei profili di esecuzione del connettore. Queste configurazioni eseguiranno l'effettiva operazione di importazione ed esportazione dei dati.

### Importazione completa e delta

Il connettore Generic SQL supporta l'importazione completa e delta usando questi metodi:

- Table
- View
- Stored Procedure
- SQL Query

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Table/View**

Per importare gli attributi multivalore per un oggetto, è necessario specificare i nomi di tabella o vista delimitati da virgole in **Name of Multi-Valued table/views** e le rispettive condizioni di join in **Join condition** con la tabella padre.

Esempio: si vuole importare l'oggetto Employee e tutti i relativi attributi multivalore. Sono presenti due tabelle denominate Employee (tabella principale) e Department (multivalore). Eseguire le operazioni seguenti:

- Digitare **Employee** in **Table/View/SP**.
- Digitare Department in **Name of Multi-Valued table/views**.
- Digitare la condizione di join tra Employee e Department in **Join Condition**, ad esempio, `Employee.DEPTID=Department.DepartmentID`. ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Stored procedure**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Nel caso di una grande quantità di dati, è consigliabile implementare la paginazione con le stored procedure.
- Per consentire alla stored procedure di supportare la paginazione, è necessario fornire Start Index e End Index. Vedere: [Paging in modo efficiente di grandi quantità di dati](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndex e @EndIndex verranno sostituiti in fase di esecuzione con il rispettivo valore di dimensioni della pagina configurato nella pagina **Configure Step**. Esempio: se il connettore recupera prima la pagina e le dimensioni della pagina sono impostate su 500, in questa situazione @StartIndex sarà 1 e @EndIndex sarà considerato come 500. Questi valori aumentano mentre il connettore recupera le pagine successive e il valore di @StartIndex e @EndIndex viene modificato.
- Per eseguire la stored procedure con parametri, specificare i parametri nel formato `[Name]:[Direction]:[Value]`. Immettere ogni parametro su una riga separata (usare CTRL+INVIO per ottenere una nuova riga).
- Il connettore Generic SQL supporta anche l'operazione di importazione da un ambiente distribuito, ad esempio, i server collegati in Microsoft SQL Server. Nel caso in cui le informazioni debbano essere recuperate da una tabella nel server collegato, la tabella deve essere fornita nel formato: `[ServerName].[Database].[Schema].[TableName]`
    - Negli ambienti distribuiti il connettore supporta solo il server collegato Microsoft.
- Il connettore Generic SQL supporta solo gli oggetti che presentano una struttura simile (sia nome alias che tipo di dati) tra le informazioni sui passaggi esecuzione e il rilevamento dello schema. Se l'oggetto selezionato dallo schema e le informazioni fornite nel passaggio di esecuzione sono diverse, il connettore SQL non potrà supportare questo tipo di scenari.

**Query SQL**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Le query con più set di risultati non sono supportate.
- La query SQL supporta la paginazione e fornisce i valori per Start Index e End Index di una variabile per supportare la paginazione.

### Importazione delta

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

La configurazione dell'importazione delta richiede un'ulteriore configurazione con gli altri metodi supportati come nell'importazione completa.

- Se l'utente sceglie l'approccio Trigger o Snapshot per rilevare le modifiche delta, può specificare History Table o Snapshot Database nella casella **History Table or Snapshot database name**.
- L'utente deve anche fornire una condizione di join tra History table e Parent table. Esempio: `Employee.ID=History.EmployeeID`
- Per tenere traccia della transazione nella tabella padre dalla tabella della cronologia, l'utente deve fornire il nome della colonna che contiene le informazioni sull'operazione come (Add/Update/Delete).
- Se l'utente sceglie Watermark per tenere traccia delle modifiche delta, deve fornire il nome della colonna che contiene le informazioni sull'operazione in **Water Mark Column Name**. Il connettore può quindi prendere in considerazione questa colonna durante l'esecuzione dell'importazione delta.
- La colonna **Change Type Attribute** è necessaria per il tipo di modifica. Questa colonna è associata a una modifica che si verifica nella tabella primaria o nella tabella multivalore per un tipo di modifica nella visualizzazione delta. Questa colonna può contenere il tipo di modifica Modify\_Attribute per la modifica a livello di attributo o un tipo di modifica Add, Modify o Delete per un tipo di modifica a livello di oggetto. Se è diverso dal valore predefinito per Add, Modify o Delete, l'utente può definire i valori usando questa opzione.

### Esporta

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Il connettore Generic SQL supporta l'esportazione tramite quattro metodi supportati, ad esempio:

- Table
- View
- Stored Procedure
- SQL Query

**Table/View**

Se l'utente sceglie l'opzione Table/View, il connettore genera le rispettive query ed esegue l'esportazione.

**Stored procedure**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se l'utente sceglie l'opzione Stored Procedure, l'esportazione richiede 3 diverse stored procedure per eseguire diverse operazioni Insert/Update/Delete.

- **Add SP Name**, questo SP viene eseguito se un oggetto qualsiasi perviene al connettore per l'inserimento nella rispettiva tabella.
- **Update SP Name**, questo SP viene eseguito se un oggetto qualsiasi perviene al connettore per l'aggiornamento nella rispettiva tabella.
- **Delete SP Name**, questo SP viene eseguito se un oggetto qualsiasi perviene al connettore per l'eliminazione nella rispettiva tabella.
- L'attributo selezionato nello schema viene usato come valore di parametro per la stored procedure. Esempio: EmployeeName: INPUT: @EmployeeName (EmployeeName viene selezionato nello schema del connettore e il connettore sostituire il valore durante l'esecuzione di esportazione).
- Per eseguire la stored procedure con parametri, digitare i parametri nel formato `[Name]:[Direction]:[Value]`. Immettere ogni parametro su una riga separata (usare CTRL+INVIO per ottenere una nuova riga).

**Query SQL**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se l'utente sceglie l'opzione SQL query, l'esportazione richiede 3 diverse query per eseguire diverse operazioni Insert/Update/Delete.

- **Insert Query**, questa query viene eseguita se un oggetto qualsiasi perviene al connettore per l'inserimento nella rispettiva tabella.
- **Update Query**, questa query viene eseguita se un oggetto qualsiasi perviene al connettore per l'aggiornamento nella rispettiva tabella.
- **Delete Query**, questa query viene eseguita se un oggetto qualsiasi perviene al connettore per l'eliminazione dalla rispettiva tabella.
- L'attributo selezionato nello schema viene usato come valore di parametro per la query. Esempio: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## Risoluzione dei problemi

-	Per informazioni su come abilitare la registrazione per risolvere i problemi relativi al connettore, vedere l'articolo relativo a [come abilitare la traccia ETW per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0309_2016-->