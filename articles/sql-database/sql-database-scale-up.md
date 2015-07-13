<properties 
   pageTitle="Modifica dei livelli di servizio e dei livelli di prestazioni del database" 
   description="In questo argomento vengono fornite informazioni su come effettuare dinamicamente la scalabilità verticale o orizzontale di un database basato su cloud mediante i livelli di servizio del database SQL di Azure. Questi ultimi possono essere, quindi, utilizzati per aumentare e ridurre le prestazioni in base alle esigenze aziendali e in termini di costi senza comportare tempi di inattività alle applicazioni." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# Modifica dei livelli di servizio e dei livelli di prestazioni del database 

Nel contenuto di questo argomento vengono descritti i passaggi necessari per spostare un database SQL di Azure tra vari livelli di servizio e di prestazioni.

## Modifica dei livelli di servizio 

Utilizzare le informazioni contenute negli argomenti [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-new-service-tiers.md) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

Tramite il portale di gestione di Azure, [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) o l'[API REST](https://msdn.microsoft.com/library/dn505719.aspx), è possibile passare facilmente da un livello di servizio a un altro .

Quando si passa da un livello di servizio a un altro, tenere presente quanto segue: prima dell'aggiornamento a livelli di servizio o di prestazioni diversi, assicurarsi che il server disponga della quota necessaria. Se occorrono quote aggiuntive, contattare l'assistenza clienti. I database federati non possono essere aggiornati ai livelli di servizio Basic Standard o Premium.

- Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. Per ulteriori informazioni sulla dimensione consentita per ogni livello di servizio, vedere la relativa tabella più avanti in questa sezione.

- Quando si aggiorna un database con la [replica geografica standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) o la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.

- Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Terminare una relazione di copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) per arrestare il processo di replica tra il database primario e i database secondari attivi.

- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).

- È possibile apportare fino a quattro modifiche di singoli database (livello di servizio o di prestazioni) nell'arco di un periodo di 24 ore.

- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

Tenere presente quanto segue: i livelli di servizio Business e Web verranno ritirati dal mercato nel settembre del 2015. Per ulteriori informazioni, vedere [Domande frequenti sull'interruzione delle edizioni Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx).

<note included> È importante tenere presente che l'implementazione corrente delle [federazioni verrà ritirata insieme ai livelli di servizio Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). È consigliabile utilizzare la funzione di [Scalabilità elastica per database SQL di Azure](sql-database-elastic-scale-get-started.md) per creare una soluzione partizionata a scalabilità orizzontale nel database SQL di Azure. Per provarla, vedere Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)

## Eseguire l'aggiornamento a un livello di servizio superiore
Per aggiornare un database, utilizzare uno dei metodi seguenti. I passaggi sono specifici per l'aggiornamento a un livello di servizio Premium, ma si applicano a tutti gli aggiornamenti.

### Uso del portale di gestione di Azure
1. Utilizzare il proprio account Microsoft per accedere al portale di gestione di Azure.
2. Passare alla scheda relativa ai **DATABASE SQL**.
3. Selezionare un database dal **relativo** elenco. Verranno aperte le pagine relative al **dashboard del database** o di **avvio rapido**.
4. Selezionare la scheda relativa alla **scalabilità** del database.
5. Nella sezione **Generale** selezionare **PREMIUM** come livello di servizio.
6. Per il **livello di prestazioni**, selezionare**P1**, **P2** o **P3**. Le risorse alla base di ogni livello di prestazioni sono rappresentate in DTU. Per ulteriori informazioni sui livelli di prestazioni e sulle unità DTU, vedere Livelli di servizio e livelli di prestazioni del database SQL di Azure
8. Nella barra dei comandi nella parte inferiore della schermata, fare clic sul pulsante di **salvataggio**.
9. Verrà visualizzata una **di conferma**. Leggere le informazioni fornite e selezionare la casella di controllo per confermare.


### Uso di Azure PowerShell
1. Utilizzare Set-AzureSqlDatabase per specificare il nuovo livello di servizio, la dimensione massima e il livello di prestazioni del database. Per un elenco delle dimensioni di database supportate dai diversi livelli di servizio, vedere Livelli di servizio (edizioni) del database SQL di Azure.
2. Impostare il contesto server con il cmdlet New-AzureSqlDatabaseServerContext. La sintassi di esempio è disponibile nella sezione relativa all'uso dei comandi PowerShell di Azure.
3. Individuare un handle per il database e il livello di prestazioni di destinazione. Specificare il livello di prestazioni utilizzando Set-AzureSqlDatabase – ServiceObjective

**Esempio di utilizzo**: in questo esempio viene illustrato l'aggiornamento a un livello di servizio Premium. Vengono creati l'handle $db che fa riferimento al nome di database "somedb"e l'handle $P1 che fa riferimento al livello di prestazioni 1 del livello di servizio Premium. Il livello di prestazioni del database $db viene, quindi, impostato su $P1.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## Downgrade a un livello di servizio inferiore
Per effettuare il downgrade di un database a un livello di servizio inferiore, utilizzare uno dei metodi seguenti:

### Uso del portale di gestione di Azure
1. Utilizzare il proprio account Microsoft per accedere al portale di gestione di Azure.
2. Passare alla scheda relativa ai **DATABASE SQL**.
3. Selezionare la scheda relativa alla **SCALABILITÀ** per il database desiderato.
4. Nella sezione relativa alle impostazioni **generale** selezionare il livello di servizio verso il quale si desidera effettuare il downgrade.
5. Nella barra dei comandi nella parte inferiore della schermata, fare clic sul pulsante di **salvataggio**.
6. Se applicabile, nella pagina di **conferma**, leggere le informazioni fornite e selezionare la casella di controllo per confermare la modifica.

### Uso di Azure PowerShell
1. Utilizzare Set-AzureSqlDatabase per specificare il livello di servizio, il livello di prestazioni e la dimensione massima del database.
2. Impostare il contesto server utilizzando il cmdlet New-AzureSqlDatabaseServerContext dalla sintassi di esempio illustrata nella sezione Utilizzo dei comandi PowerShell di Azure
3. Eseguire le operazioni seguenti:
 - Ottenere un handle per il database.
 - Ottenere un handle per il livello di prestazioni.
 - Utilizzare il cmdlet Set-AzureSqlDatabase per specificare il livello di servizio, il livello di prestazioni e la dimensione massima del database.

**Esempio di utilizzo**

In questo esempio viene illustrato come effettuare il downgrade da un database con livello di servizio Premium a un database con livello di servizio Standard:

- Vengono creati l'handle $db che fa riferimento al nome di database "somedb"

- e la variabile $S2 che fa riferimento al livello di prestazioni S2 del livello di servizio Standard.

- Il livello di prestazioni del database $db viene, quindi, impostato su $S2.

- Specificare il livello di servizio e la dimensione massima del database specificando i parametri-Edition e -MaxSizeGB. Il valore specificato per il parametro –MaxSizeGB deve essere compatibile con il livello di servizio di destinazione. Una tabella con i valori del parametro -MaxSize per ogni livello di servizio è illustrata in precedenza in questo argomento.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##Modifica dei livelli di prestazioni
È possibile aumentare o diminuire i livelli di prestazioni di un database Standard o Premium utilizzando uno dei metodi seguenti. La modifica del livello di prestazioni del database. Per ulteriori informazioni, vedere la sezione seguente [Impatto delle modifiche al database di livello Premium](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact).

Se si modifica il livello di prestazioni di un database Premium con relazioni di replica geografica attive configurate, procedere nel seguente ordine per il database primario e i database attivi secondari:

Infatti, i database secondari attivi devono disporre di un livello di prestazioni uguale o maggiore rispetto a quello primario. Se si passa da un livello di prestazioni superiore a un livello di prestazioni inferiore, iniziare prima con il database primario seguito da uno o più database secondari attivi.

- Se si passa da un livello di prestazioni inferiore a un livello di prestazioni superiore, iniziare con i database secondari attivi, quindi con il database primario.

###Uso del portale di gestione di Azure
1. Utilizzare il proprio account Microsoft per accedere al portale di gestione di Azure.
2. Passare alla scheda relativa ai **DATABASE SQL**.
3. Selezionare un database dal **relativo** elenco per l'account o per un server specifico. Verranno aperte le pagine relative al **dashboard del database** o di **avvio rapido**.
5. Selezionare la scheda relativa alla **scalabilità** del database.
6. Selezionare un **livello di prestazioni** per la relativa opzione
7. Nella barra dei comandi nella parte inferiore della schermata, fare clic sul pulsante di **salvataggio**

###Uso di Azure PowerShell
1. Utilizzare il cmdlet Set-AzureSqlDatabase per specificare il livello di prestazioni del database.
2. Impostare il contesto server con il cmdlet New-AzureSqlDatabaseServerContext La sintassi di esempio è disponibile nella sezione relativa all'uso dei comandi PowerShell di Azure.
3. Eseguire le operazioni seguenti:
- Ottenere un handle per il database.
- Ottenere un handle per il livello di prestazioni.
- Specificare il livello di prestazioni con il cmdlet Set-AzureSqlDatabase – ServiceObjective.

**Esempio di utilizzo**

Esempio:

- Vengono creati l'handle $db che fa riferimento al nome di database "somedb"

- e l'handle $P2 che fa riferimento al livello di prestazioni 2 del livello di servizio Premium.

- Il livello di prestazioni del database $db viene, quindi, impostato su $P2.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##Impatto delle modifiche al database
In questa sezione vengono fornite informazioni sugli effetti dell'aggiornamento a un livello di servizio Standard o Premium o dell'applicazione di modifiche a livello di prestazioni al database.

###Gestione di connessioni dell'applicazione durante le modifiche al database
Le connessioni al database potrebbero essere temporaneamente interrotte quando vengono effettuati una modifica, un aggiornamento o un downgrade del livello di prestazioni e potrebbero trascorrere alcuni secondi prima di poterle ristabilire. Le applicazioni del database SQL devono essere sviluppate in modo da essere resilienti rispetto alle disconnessioni. Ciò può accadere, infatti, in qualsiasi momento nel database SQL quando si verifica un errore in un computer del data center e servizio relativo al database SQL effettua failover del database. Per utilizzare un database Premium o apportare modifiche a un livello di prestazioni del database Premium non è richiesta alcuna modifica a livello di implementazione.

###Informazioni e valutazione della latenza relative alle modifiche del database
Una modifica SLO di un database comporta spesso lo spostamento di dati, quindi potrebbero trascorrere diverse ore prima che la richiesta di modifica venga completata e che venga attivata la modifica alla modalità di fatturazione associata. Lo spostamento di dati si verifica per le modifiche che comportano l'aggiornamento o il downgrade di un database e può inoltre verificarsi quando si modifica il livello di prestazioni del database.

**Latenza per modifiche SLO che comportano lo spostamento di dati**

Dopo aver determinato la dimensione di archiviazione del database, è possibile stimare la latenza di una richiesta di modifica SLO mediante l'euristica indicata di seguito:

3 x (5 minuti + dimensione del database / 150 MB al minuto)

Ad esempio, se le dimensioni del database sono 50 GB, la latenza della richiesta di modifica SLO viene stimata mediante l'euristica indicata di seguito:

3 x (5 minuti + 50 GB x 1024 MB/GB / 150 MB al minuto) ≈17 ore

Le stime del limite inferiore e superiore che risultano da questa euristica variano da 15 minuti per un database vuoto fino a circa 2 giorni per un database di 150 GB. Le stime possono variare ulteriormente in base alle condizioni del data center.

**Latenza per la modifica da un livello di prestazioni superiore a un livello di prestazioni inferiore**

In genere, non avviene alcuno spostamento di dati durante il passaggio da un livello di prestazioni superiore a uno di livello inferiore. In questi casi, la latenza delle modifiche SLO è molto inferiore e in genere si calcola nell'ordine di secondi.

Avviso: l'istruzione precedente si applica solo ai downgrade dal livello di servizio Premium a quello Standard. Il downgrade a un livello di servizio Web, Business o Basic implica lo spostamento di dati.

###Verifica dello stato di modifica del Database
È possibile verificare lo stato del database durante un aggiornamento o un downgrade tra livelli di servizio diversi o quando si modifica il livello di prestazioni mediante uno dei metodi seguenti.

####Uso del portale di gestione di Azure
1. Utilizzare il proprio account Microsoft per accedere al portale di gestione di Azure.
2. Selezionare un database dal **relativo** elenco. Verranno aperte le pagine relative al **dashboard del database** o di **avvio rapido**.
3. Nella sezione di **riepilogo rapido** del **dashboard del database**, è possibile verificare la disponibilità di eventuali informazioni di stato relativi all'**edizione**.
4. L'obiettivo del livello di servizio (SLO) rappresenta il livello di prestazioni nell'ambito del livello di servizio.


##Uso dei comandi Azure PowerShell
In questa sezione vengono indicati i prerequisiti per l'utilizzo dei comandi Azure PowerShell.

**Prerequisiti**

Per utilizzare i cmdlet Azure PowerShell descritti in questo argomento, è necessario che il seguente software sia installato nel computer in cui viene eseguito PowerShell. 1. Scaricare una versione di Windows PowerShell almeno 3.0, livello http://www.microsoft.com/it-it/download/details.aspx?id=34595.

2. Scaricare Azure PowerShell dalla sezione relativa allo strumento da riga di comando in [Azure SDK and Tool Downloads](http://azure.microsoft.com/downloads/).

Effettuare quanto segue: dalla schermata **Start** o dal **relativo** menu, individuare e avviare Azure PowerShell.

Digitare il nome utente e la password relativi al server.

Impostare il contesto server con il cmdlet **New-AzureSqlDatabaseServerContext**.

**Esempio**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Riferimenti di Azure PowerShell** Per informazioni dettagliate sui cmdlet di Azure PowerShell utilizzati in questo argomento, vedere [Cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/dn546726.aspx).

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=62-->