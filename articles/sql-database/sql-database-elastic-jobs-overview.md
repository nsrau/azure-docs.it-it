<properties
	pageTitle="Gestione dei database cloud con scalabilità orizzontale | Microsoft Azure" 
	description="Viene illustrato il servizio processo di database elastico" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" 
    documentationCenter=""  
	manager="jhubbard" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="ddove" />

# Gestione dei database cloud con scalabilità orizzontale

Per la gestione dei database cloud con scalabilità orizzontale, la funzionalità **Processi di database elastico** (in anteprima) consente di eseguire uno script Transact-SQL (T-SQL) in modo affidabile o di eseguire DACPAC, un'[applicazione livello dati](https://msdn.microsoft.com/library/ee210546.aspx), in un gruppo di database, ad esempio:

* una raccolta personalizzata di database (illustrata di seguito)
* tutti i database in un[pool di database elastici](sql-database-elastic-pool.md)
* un set di partizioni, creato con la [libreria client dei database elastici](sql-database-elastic-database-client-library.md). 
 
## Documentazione

* [Installare i componenti di Processi di database elastico](sql-database-elastic-jobs-service-installation.md). 
* [Introduzione a Processi di database elastico](sql-database-elastic-jobs-getting-started.md).
* [Creare e gestire processi tramite PowerShell](sql-database-elastic-jobs-powershell.md).
* [Creare e gestire database SQL di Azure con scalabilità orizzontale](sql-database-elastic-jobs-getting-started.md)

**Processi database elastici** è attualmente un servizio cloud di Azure ospitato dal cliente che consente l'esecuzione di attività amministrative ad hoc e pianificate, dette **processi**. Con i processi è possibile gestire facilmente e in modo affidabile gruppi estesi di database SQL di Azure tramite l'esecuzione di script Transact-SQL per eseguire operazioni amministrative.

![Servizio processo di database elastico][1]

## Vantaggi offerti dai processi

**Manage**

Eseguire facilmente le modifiche dello schema, la gestione delle credenziali, gli aggiornamenti dei dati di riferimento, la raccolta dei dati sulle prestazioni o la raccolta dei dati di telemetria del tenant (cliente).

**Report**

Aggregare i dati di una raccolta di database SQL di Azure in una singola tabella di destinazione.

**Ridurre il sovraccarico**

In genere, è necessario connettersi a ciascun database in modo indipendente per eseguire istruzioni T-SQL o effettuare altre attività amministrative. Un processo gestisce le attività di accesso a ogni database nel gruppo di destinazione. È anche necessario definire, gestire e mantenere gli script T-SQL da eseguire su un gruppo di database SQL di Azure.

**Accounting**

I processi eseguono lo script e registrano lo stato di esecuzione per ogni database. È anche disponibile la ripetizione automatica in caso di errori.

**Flessibilità**

Definire gruppi personalizzati di database SQL di Azure e pianificazioni per l'esecuzione di un processo.

**Distribuzione**

Distribuire applicazioni livello dati (DACPAC).

> [AZURE.NOTE] Nel portale di Azure è disponibile solo un set ridotto di funzioni limitate ai pool elastici di SQL di Azure. Utilizzare le API di PowerShell per accedere alla serie completa delle funzionalità correnti.

## Applicazioni 

* Eseguire attività amministrative, quali la distribuzione di un nuovo schema.
* Aggiornare i dati di riferimento, ad esempio informazioni di prodotto comuni tra tutti i database. Pianificare aggiornamenti automatici ogni giorno della settimana, dopo l'orario di lavoro.
* Ricompilazione degli indici per migliorare le prestazioni delle query. La ricompilazione può essere configurata per essere eseguita in una raccolta di database su base periodica, ad esempio durante le fasce orarie non di punta.
* Raccogliere i risultati di query da un set di database in una tabella centrale su base costante. Le query di prestazione possono essere eseguite continuamente e configurate per l'esecuzione di attività aggiuntive di trigger.
* Eseguire query di elaborazione dei dati più lunghe per una vasta serie di database, ad esempio la raccolta della telemetria del cliente. I risultati vengono raccolti in una tabella di destinazione singola per ulteriori analisi.

## Processi di database elastici: end-to-end 
1.	Installare i componenti dei **processi di database elastici**. Per altre informazioni, vedere [Installazione dei processi di database elastici](sql-database-elastic-jobs-service-installation.md). Se l'installazione non riesce, vedere [come disinstallare](sql-database-elastic-jobs-uninstall.md).
2.	Utilizzare le API di PowerShell per accedere a ulteriori funzionalità, ad esempio la creazione di raccolte di database personalizzati, l’aggiunta di pianificazioni e/o la raccolta di set di risultati. Usare il portale per un'installazione semplice e la creazione o il monitoraggio dei processi limitati all'esecuzione in un **pool di database elastici**. 
3.	Creare credenziali crittografate per l'esecuzione del processo e [aggiungere l'utente (o il ruolo) a ogni database nel gruppo](sql-database-security.md).
4.	Creare uno script T-SQL idempotente che può essere eseguito su ogni database nel gruppo. 
5.	Seguire questi passaggi per creare processi tramite il portale di Azure: [Creazione e gestione di processi di database elastici](sql-database-elastic-jobs-create-and-manage.md) 
6.	In alternativa, usare script di PowerShell: [Creare e gestire processi di database elastici del database SQL tramite PowerShell (anteprima)](sql-database-elastic-jobs-powershell.md).

## Script idempotenti

Gli script devono essere [idempotenti](https://en.wikipedia.org/wiki/Idempotence). In altre parole, "idempotente" significa che se lo script riesce e viene eseguito di nuovo, si ottiene lo stesso risultato. Uno script potrebbe non riuscire a causa di problemi di rete temporanei. In tal caso, il processo ritenterà automaticamente l'esecuzione dello script per un numero di volte predefinito prima di desistere. Uno script idempotente ha lo stesso risultato anche se è stato eseguito correttamente due volte.

Una semplice strategia consiste nel verificare l'esistenza di un oggetto prima di crearlo.

	IF NOT EXIST (some_object)
	-- Create the object 
	-- If it exists, drop the object before recreating it.

Analogamente, uno script deve poter essere eseguito correttamente verificando in modo logico e risolvendo qualsiasi condizione trovata.

## Errori e log

Se uno script non riesce dopo diversi tentativi, il processo registra l'errore e continua. Dopo il termine di un processo, ovvero un'esecuzione in tutti i database nel gruppo, è possibile controllare l'elenco dei tentativi non riusciti. I log forniscono informazioni dettagliate per il debug degli script difettosi.

## Tipi di gruppo e creazione

Esistono due tipi di gruppi:

1. Set di partizioni
2. Gruppi personalizzati

I gruppi di set di partizioni vengono creati usando gli [strumenti di database elastici](sql-database-elastic-scale-introduction.md). Quando si crea un gruppo di set di partizioni, i database vengono aggiunti o rimossi automaticamente dal gruppo. Ad esempio, una nuova partizione viene creata automaticamente nel gruppo quando viene aggiunta alla mappa partizioni. Quindi è possibile eseguire un processo sul gruppo.

I gruppi personalizzati, d'altra parte, sono definiti rigidamente. È necessario aggiungere o rimuovere i database in modo esplicito dai gruppi personalizzati. Se viene eliminato un database nel gruppo, il processo tenterà di eseguire lo script sul database, generando infine un errore. I gruppi creati tramite il portale di Azure attualmente sono gruppi personalizzati.


## Componenti e prezzi
 
I seguenti componenti interagiscono per creare un servizio Cloud di Azure che consente l'esecuzione ad hoc dei processi di amministrazione. I componenti vengono installati e configurati automaticamente durante la configurazione, nella sottoscrizione. È possibile identificare i servizi poiché hanno tutti lo stesso nome generato automaticamente. Il nome è univoco ed è costituito dal prefisso "edj" seguito da 21 caratteri generati casualmente.

* **Servizio cloud di Azure**: i processi di database elastica (anteprima) vengono recapitati come servizio cloud di Azure ospitato dal cliente per l'esecuzione delle attività richieste. Dal portale, il servizio viene distribuito e ospitato nella sottoscrizione Microsoft Azure. Il servizio predefinito distribuito viene eseguito con un numero minimo di due ruoli di lavoro per la disponibilità elevata. La dimensione predefinita di ogni ruolo di lavoro (ElasticDatabaseJobWorker) viene eseguita in un'istanza A0. Per informazioni sui prezzi, vedere [Servizi cloud Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Database SQL di Azure**: il servizio usa un database SQL di Azure noto come **database di controllo** per archiviare tutti i metadati del processo. Il livello di servizio predefinito è S0. Per informazioni sui prezzi, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).
* **Bus di servizio di Azure**: è destinato alla coordinazione del lavoro all'interno del servizio cloud di Azure. Vedere [Bus di servizio Prezzi](https://azure.microsoft.com/pricing/details/service-bus/).
* **Archiviazione di Azure**: un account di Archiviazione di Azure viene usato per archiviare la registrazione dell'output di diagnostica, nel caso in cui un problema richieda un ulteriore debugging. Vedere [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## Funzionano dei processi di database elastico

1.	Un database SQL di Azure viene designato come **database di controllo** per l'archiviazione di tutti i dati di stato e i metadati.
2.	Il **servizio processi** accede al database di controllo per avviare e tenere traccia dei processi da eseguire.
3.	Due diversi ruoli comunicano con il database di controllo: 
	* Controller: Determina quali processi richiedono attività per eseguire il processo richiesto e ritenta di eseguire i processi non riusciti tramite la creazione di nuove attività di processo.
	* Esecuzione dell'attività di processo: Esegue le attività di processo.

### Tipi di attività di processo

Sono disponibili più tipi di attività di processo che eseguono l'esecuzione di processi:

* ShardMapRefresh: Esegue una query del mapping della partizione per determinare tutti i database utilizzati come partizioni
* ScriptSplit: Divide lo script tra le istruzioni ‘GO’ in batch
* ExpandJob: Crea processi figlio per ogni database da un processo destinato a un gruppo di database
* ScriptExecution: Esegue uno script su un particolare database utilizzando le credenziali definite
* Dacpac: Applica un DACPAC a un determinato database utilizzando determinate credenziali

## Flusso di lavoro completo di esecuzione del processo

1.	Un processo viene inserito nel **database di controllo** tramite il portale o l'API di PowerShell. Il processo richiede l'esecuzione di uno script Transact-SQL su un gruppo di database che utilizza credenziali specifiche.
2.	Il controller identifica il nuovo processo. Le attività di processo vengono create ed eseguite per suddividere lo script e per aggiornare i database del gruppo. Infine, un nuovo processo viene creato ed eseguito per espandere il processo e creare nuovi processi figlio in cui ogni processo figlio è specificato per eseguire lo script Transact-SQL su un singolo database nel gruppo.
3.	Il controller identifica i processi figlio creati. Per ogni processo, il controller crea e avvia un'attività di processo per eseguire lo script su un database. 
4.	Dopo aver completato tutte le attività di processo, il controller aggiorna i processi nello stato ‘completato’. In qualsiasi momento durante l'esecuzione del processo, l'API di PowerShell può essere utilizzata per visualizzare lo stato corrente dell'esecuzione del processo. Tutte le volte restituite dalle API PowerShell sono rappresentate in formato UTC. Se si desidera, è possibile avviare una richiesta di annullamento per interrompere un processo. 

## Passaggi successivi
[Installare i componenti](sql-database-elastic-jobs-service-installation.md), quindi [creare e aggiungere un log a ciascun database nel gruppo di database](sql-database-security.md). Per comprendere la creazione e la gestione del processo, vedere [Creazione e gestione di processi elastici di database](sql-database-elastic-jobs-create-and-manage.md). Vedere anche [Introduzione a Processi di database elastico](sql-database-elastic-jobs-getting-started.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

 

<!---HONumber=AcomDC_0601_2016-->