<properties 
	title="Split and Merge Service Tutorial" 
	pageTitle="Esercitazione per il servizio di divisione e unione di SQL di Azure" 
	description="Suddivisione e unione con Scalabilità elastica" 
	metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" 
	services="sql-database" documentationCenter=""  
	manager="jhubbard" authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" ms.devlang="na" 
	ms.topic="article" ms.date="03/05/2015" 
	ms.author="torsteng" />

# Esercitazione per il servizio di divisione e unione della scalabilità elastica

## Scaricare i pacchetti di divisione e unione 
1. Scaricare la versione più recente di NuGet dal sito Web [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Aprire un prompt dei comandi e passare alla directory in cui si è scaricato il file nuget.exe. 
3. Scaricare il pacchetto di divisione e unione più recente nella directory corrente usando il seguente comando: 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

I passaggi precedenti consentono di scaricare i file di divisione e unione nella directory corrente. I file vengono inseriti in una directory denominata **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** dove *x.x.xxx.x* rappresenta il numero di versione. Trovare i file del servizio di divisione e unione nella sottodirectory **content\splitmerge\service** e gli script di PowerShell di divisione e unione (compresi i file DLL client necessari) nella sottodirectory **content\splitmerge\powershell**.

## Prerequisiti 

1. Creare un database SQL di Azure che verrà usato come database per lo stato di divisione e unione. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com). Nella parte inferiore sinistra dello schermo fare clic su **Nuovo**, quindi scegliere **Data Services** -> **Database SQL** -> **Creazione personalizzata**. Compilare il nome del database e creare un nuovo utente e una password. Assicurarsi di prendere nota del nome e della password per l'uso successivo.

2. Assicurarsi che il server di database SQL di Azure consenta la connessione da parte dei servizi di Microsoft Azure. Passare al [portale di gestione di Azure](https://manage.windowsazure.com), quindi nel riquadro sinistro fare clic su **Database SQL**. Fare clic su **Server** nella barra multifunzione nella parte superiore dello schermo e selezionare il server. Fare clic su **Configura** nella parte superiore dello schermo e assicurarsi che l'opzione **Servizi di Microsoft Azure** sia impostata su **Sì**.

    ![Allowed services][1]

3. Creare un account di archiviazione di Azure che verrà usato per l'output di diagnostica. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com). In basso a sinistra fare clic su **Nuovo**, scegliere **Data Services**, **Archiviazione**, quindi **Creazione rapida**. 

4. Creare un servizio cloud di Azure che conterrà il servizio di divisione e unione.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com). In basso a sinistra fare clic su **Nuovo**, scegliere **Calcolo**, **Servizio cloud**, quindi **Creazione rapida**. 


## Configurare il servizio di divisione e unione 

### Configurazione del servizio di divisione e unione 

1. Nella cartella in cui sono stati scaricati gli elementi necessari per la divisione e l'unione creare una copia del file **ServiceConfiguration.Template.cscfg** fornito con **SplitMergeService.cspkg** e denominare tale copia **ServiceConfiguration.cscfg**.

2. Aprire ServiceConfiguration.cscfg in un editor di testo di propria scelta. Si consiglia di usare Visual Studio, in quanto quest'ultimo eseguirà la convalida degli input, come ad esempio quella del formato dell'identificazione personale del certificato. 

3. Creare un nuovo database o scegliere un database esistente da usare come database per lo stato delle operazioni di divisione e unione, quindi recuperare la stringa di connessione del database. Con il database SQL di Azure, la stringa di connessione presenta in genere un formato come il seguente:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Immettere la stringa di connessione nel file cscfg in entrambe le sezioni Role **SplitMergeWeb** e **SplitMergeWorker** dell'impostazione ElasticScaleMetadata.

5.    La configurazione della destinazione per i log di diagnostica richiede un account di archiviazione in Azure Per la configurazione della divisione e unione è necessaria la stringa di connessione all'account di archiviazione di Azure. La stringa di connessione deve essere nel seguente formato:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Per determinare la chiave di accesso, passare al [portale di gestione di Azure](https://manage.windowsazure.com), fare clic sulla scheda **Archiviazione** a sinistra, selezionare il nome corrispondente al proprio account di archiviazione, quindi fare clic su **Gestisci chiavi di accesso** in basso. Fare clic sul pulsante con il simbolo della **copia** accanto a **Chiave di accesso primaria**.

![manage access keys][2]

6.    Immettere il nome dell'account di archiviazione e una delle chiavi di accesso fornite nei segnaposto della stringa di connessione al servizio di archiviazione. Questa stringa di connessione viene usata nelle sezioni dei ruoli **SplitMergeWeb** e **SplitMergeWorker** dell'impostazione **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Potenzialmente è possibile usare account di archiviazione diversi per ruoli diversi. 

### Configurazione della sicurezza 
Per istruzioni dettagliate sulla configurazione della sicurezza del servizio, vedere l'articolo relativo alle [configurazioni di sicurezza per la scalabilità elastica](../sql-database-elastic-scale-configure-security.md).

Ai fini di  una semplice distribuzione di prova utile per il completamento di questa esercitazione, verrà completata una serie minima di passaggi di configurazione per la messa in funzione del servizio. Questi passaggi abilitano unicamente la macchina/account che li esegue alla comunicazione con il servizio.

### Creazione di un certificato autofirmato 

Creare una nuova directory e, da questa, eseguire il seguente comando usando una finestra del [Prompt dei comandi per gli sviluppatori per Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Verrà richiesta una password per proteggere la chiave privata. Immettere una password complessa e confermarla. Dopo questo passaggio, verrà richiesto di usare la password una volta ancora. Fare clic su **Sì** alla fine per importarla nell'archivio delle Autorità di certificazione radice disponibili nell'elenco locale.

### Creare un file PFX 

Eseguire il seguente comando dalla stessa finestra in cui è stato eseguito makecert. Servirsi della stessa password usata per la creazione del certificato:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importare il certificato client nell'archivio personale
1. In Esplora risorse fare doppio clic su **MyCert.pfx**.
2. Nell'**Importazione guidata certificati** selezionare **Utente corrente**, quindi fare clic su **Avanti**.
3. Confermare il percorso del file e fare clic su **Avanti**.
4. Digitare la password, lasciare selezionata l'opzione **Includi tutte le proprietà estese** e fare clic su **Avanti**.
5. Lasciare selezionata l'opzione **Seleziona automaticamente l'archivio certificati[...]** e fare clic su **Avanti**.
6. Fare clic su **Fine**, quini su **OK**.

### Caricare il file PFX nel servizio cloud

Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

1. Selezionare **Servizi cloud**.
2. Selezionare il servizio cloud creato precedentemente per il servizio di divisione e unione.
3. Scegliere **Certificati** dal menu in alto.
4. Fare clic su **Carica** nella barra inferiore.
5. Selezionare il file PFX e immettere la stessa password usata sopra.
6. Una volta completata l'operazione, copiare l'identificazione personale del certificato dalla nuova voce nell'elenco.

### Aggiornare il file di configurazione del servizio

Incollare l'identificazione personale del certificato copiata sopra nell'attributo identificazione personale/valore delle seguenti impostazioni:
Per il ruolo Web:

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" /> 
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Per il ruolo di lavoro:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Si noti che per distribuzioni destinate alla produzione è necessario usare certificati separati per CA, crittografia, server e client. Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione della sicurezza](../sql-database-elastic-scale-configure-security.md).

### Distribuzione del servizio di divisione e unione
1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic sulla scheda **Servizi cloud** a sinistra, quindi selezionare il servizio cloud creato precedentemente. 
3. Fare clic su **Dashboard**. 
4. Scegliere l'ambiente di gestione temporanea, quindi fare clic su **Carica una nuova distribuzione di gestione temporanea**.

    ![Staging][3]

5. Nella finestra di dialogo immettere un'etichetta per la distribuzione. Per 'Package' e 'Configuration' fare clic su 'From Local', quindi scegliere il file **SplitMergeService.cspkg** e il proprio file con estensione cscfg configurato in precedenza.
6. Assicurarsi che la casella di controllo **Distribuire anche se uno o più ruoli contengono una singola istanza** sia selezionata.
7. Fare clic sul pulsante con il segno di spunta in basso a destra per avviare la distribuzione. Per il completamento dell'operazione sarà necessario attendere alcuni minuti.

![Upload][4]


## Risoluzione dei problemi relativi alla distribuzione
Se la messa in linea del proprio ruolo Web non riesce, è probabile che si tratti di un problema relativo alla configurazione della sicurezza. Verificare che SSL sia configurato come descritto sopra.

Se la messa online del proprio ruolo di lavoro non riesce, ma riesce quella del ruolo Web, è probabile che si tratti di un problema con la connessione al database per lo stato creato in precedenza. 

* Assicurarsi che la stringa di connessione nel file .cscfg sia corretta. 
* Verificare che il server e il database esistano e che l'ID utente e la password siano corretti.
* Per il database SQL di Azure, la stringa di connessione deve essere nel seguente formato: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Assicurarsi che il nome del server non inizi con **https://**.
* Assicurarsi che il server di database SQL di Azure consenta la connessione da parte dei servizi di Microsoft Azure. Per eseguire questa operazione, aprire https://manage.windowsazure.com, fare clic su "Database SQL" a sinistra, fare clic su "Server" in alto, quindi selezionare il proprio server. Fare clic su **Configura** nella parte superiore dello schermo e assicurarsi che l'opzione **Servizi di Microsoft Azure** sia impostata su "Sì" (vedere la sezione Prerequisiti all'inizio di questo articolo).

* Esaminare i log di diagnostica dell'istanza del servizio di divisione e unione. Aprire un'istanza di Visual Studio e nella barra dei menu fare clic su **Visualizza**, quindi su **Esplora server**. Fare clic sull'icona di **Microsoft Azure** per connettersi alla sottoscrizione Azure. Passare a Microsoft Azure -> Archiviazione -> <account_di_archiviazione> -> Tabelle -> WADLogsTable. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

    ![][5]

## Test della distribuzione del servizio di divisione e unione
### Connessione a un Web browser

Determinare l'endpoint Web del servizio di divisione e unione. È possibile trovarlo nel portale di gestione di Azure accedendo al **dashboard** del proprio servizio cloud e guardando in **URL sito** a destra. Sostituire **http://** con **https://** poiché le impostazioni di sicurezza predefinite disabilitano l'endpoint HTTP. Caricare la pagina per questo URL nel browser.

### Test con gli script di PowerShell

È possibile testare la distribuzione e l'ambiente eseguendo gli script di PowerShell di esempio inclusi.

I file di script inclusi sono i seguenti:

1. **SetupSampleSplitMergeEnvironment.ps1**. Imposta un livello di dati di test per divisione e unione (per una descrizione dettagliata, vedere la tabella sottostante).
2. **ExecuteSampleSplitMerge.ps1**. Esegue le operazioni di test sul livello di dati di test (per una descrizione dettagliata, vedere la tabella sottostante).
3. **GetMappings.ps1**. Script di esempio di primo livello che visualizza lo stato corrente dei mapping di partizione.
4. **ShardManagement.psm1**  . Script helper che include l'API ShardManagement.
5. **SqlDatabaseHelpers.psm1**. Script helper per creare e gestire database SQL.

<table style="width:100%">
  <tr>
    <th>File PowerShell</th>
    <th>Passaggi</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Crea un database di gestione delle mappe partizioni.</td>
  </tr>
  <tr>
    <td>2.    Crea 2 database partizioni. 
  </tr>
  <tr>
    <td>3.    Crea una mappa partizioni per tali database (elimina eventuali mappe partizioni esistenti per i database). </td>
  </tr>
  <tr>
    <td>4.    Crea una tabella di esempio di piccole dimensioni in entrambe le partizioni e popola la tabella in una delle partizioni.</td>
  </tr>
  <tr>
    <td>5.    Dichiara l'elemento SchemaInfo per la tabella partizionata.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>File PowerShell</th>
    <th>Passaggi</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Invia una richiesta di divisione al front-end Web del servizio di divisione e unione, che sposta la metà dei dati dalla prima partizione alla seconda partizione.</td>
  </tr>
  <tr>
    <td>2.    Esegue il polling del front-end Web per lo stato della richiesta di divisione e attende il completamento della richiesta.</td>
  </tr>
  <tr>
    <td>3.    Invia una richiesta di unione al front-end Web del servizio di divisione e unione, che sposta la metà dei dati dalla seconda partizione alla prima partizione.</td>
  </tr>
  <tr>
    <td>4.    Esegue il polling del front-end Web per lo stato della richiesta di unione e attende il completamento della richiesta.</td>
  </tr>
</table>

## Uso di PowerShell per la verifica della distribuzione

1.    Aprire una nuova finestra di PowerShell e passare alla directory in cui si è scaricato il pacchetto di divisione e unione, quindi passare alla directory "powershell".
2.    Creare un server di database SQL di Microsoft Azure (o sceglierne uno esistente) che conterrà il gestore dei mapping della partizione e le partizioni stesse. 

    Nota: Lo script SetupSampleSplitMergeEnvironment.ps1 crea tutti questi database sullo stesso server per impostazione predefinita, a scopo di semplificazione. Non si tratta di una restrizione del servizio di divisione e unione in sé stesso.

    Per spostare dati e aggiornare il mapping della partizione, sarà necessario un account di accesso di autenticazione SQL con accesso in lettura/scrittura ai database per il servizio di divisione e unione. Poiché il servizio di divisione e unione viene eseguito nel cloud, non supporta attualmente l'autenticazione integrata.

    Assicurarsi che il server SQL di Azure sia configurato per consentire l'accesso dall'indirizzo IP del computer che esegue questi script. È possibile trovare questa impostazione nel server SQL di Azure, in / configurazione / indirizzi IP consentiti.

3.    Eseguire lo script SetupSampleSplitMergeEnvironment.ps1 per creare l'ambiente di esempio. 

    L'esecuzione di questo script comporterà la cancellazione di tutte le strutture di dati di gestione dei mapping della partizione nel relativo database, nonché di tutte le partizioni. Potrebbe risultare utile eseguire nuovamente lo script se si vuole inizializzare nuovamente il mapping della partizione o le partizioni stesse.

    Riga di comando di esempio:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Eseguire lo script Getmappings.ps1 per visualizzare i mapping esistenti nell'ambiente di esempio.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Eseguire lo script ExecuteSampleSplitMerge.ps1 per realizzare un'operazione di divisione (spostamento della metà dei dati dalla prima partizione alla seconda) e successivamente un'operazione di unione (spostando nuovamente i dati nella prima partizione). Se si è configurato SSL e si è lasciato l'endpoint http disabilitato, assicurarsi di usare l'endpoint https://.

    Riga di comando di esempio:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Se viene visualizzato il seguente errore, è probabile che di tratti di un problema con il certificato dell'endpoint Web. Provare a connettersi all'endpoint con un Web browser e controllare se è presente un errore di certificato.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Se la connessione riesce, l'output dovrebbe risultare simile al seguente:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Ora è possibile sperimentare il processo con altri tipi di dati. Questi script accettano un parametro facoltativo, ShardKeyType, che consente di specificare il tipo di chiave. Il tipo predefinito è Int32, ma è anche possibile specificare Int64, Guid o Binary. 

## Creazione di richieste personalizzate 

Il servizio può essere usato tramite l'interfaccia utente Web o l'importazione nonché mediante il modulo PowerShell SplitMerge.psm1 che invierà le richieste tramite il ruolo web.

Il servizio di divisione e unione può spostare i dati in tabelle partizionate e tabelle di riferimento. Una tabella partizionata ha una colonna chiave di partizionamento e ospita dati di riga diversi per ogni partizione. Una tabella di riferimento non è partizionata e può così contenere gli stessi dati di riga in ogni partizione. Le tabelle di riferimento sono utili per i dati che non vengono modificati di frequente e vengono usate per il JOIN con tabelle partizionate nelle query.

Per eseguire un'operazione di divisione e unione, è necessario dichiarare le tabelle partizionate e le tabelle di riferimento che si vogliono spostare. Questa operazione viene eseguita con l'API **SchemaInfo**. Tale API si trova nello spazio dei nomi **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Per ogni tabella partizionata, creare un oggetto **ShardedTableInfo** che descriva il nome dello schema padre della tabella (facoltativo, il valore predefinito è "dbo"), il nome della tabella e il nome della colonna della tabella che contiene la chiave di partizionamento orizzontale.
2.    Per ogni tabella di riferimento, creare un oggetto **ReferenceTableInfo** che descriva il nome dello schema padre della tabella (facoltativo, il valore predefinito è "dbo") e il nome della tabella.
3.    Aggiungere gli oggetti TableInfo precedenti a un nuovo oggetto **SchemaInfo**.
4.    Ottenere un riferimento a un oggetto **ShardMapManager**, quindi chiamare **GetSchemaInfoCollection**.
5.    Aggiungere **SchemaInfo** a **SchemaInfoCollection**, fornendo il nome della mappa di partizionamento.

È possibile consultare un esempio nello script SetupSampleSplitMergeEnvironment.ps1.

Si noti che il servizio di divisione e unione non crea automaticamente il database di destinazione (o lo schema per tutte le tabelle nel database). Questi devono essere creati precedentemente, prima di inviare richieste al servizio.


## Risoluzione dei problemi
Quando si eseguono gli script di PowerShell di esempio, è possibile che venga visualizzato un messaggio simile a quello riportato di seguito:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Questo errore indica che il certificato SSL non è configurato correttamente. Seguire le istruzioni contenute nella sezione 'Connecting with a web browser'.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png

<!--HONumber=47-->
 