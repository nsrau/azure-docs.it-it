<properties 
	title="Elastic database jobs overview" 
	pageTitle="Panoramica dei processi di database elastici" 
	description="Viene illustrato il servizio processo di database elastico" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# Panoramica dei processi di database elastici

La funzionalità**processi database elastici**(anteprima) consente di eseguire in maniera affidabile uno script Transact-SQL (T-SQL) o di applicare DACPAC in un gruppo di database che include una raccolta personalizzata di database, in tutti i database di un [pool di database elastici (anteprima)](sql-database-elastic-pool.md) o in una partizione impostata (creata utilizzando la [libreria client di database elastici](sql-database-elastic-database-client-library.md)). In anteprima,**processi database elastici**è attualmente un servizio Cloud Azure ospitato dal cliente che consente l'esecuzione di attività amministrative ad hoc e pianificate, dette processi. Utilizzando questa funzionalità, è possibile gestire Database SQL di Azure in maniera semplice ed affidabile su larga scala in un intero gruppo di database tramite l'esecuzione di script Transact-SQL per eseguire operazioni amministrative, come ad esempio modifiche dello schema, gestione delle credenziali, aggiornamenti dei dati di riferimento, raccolta dei dati delle prestazioni o raccolta di dati di telemetria tenant (cliente). In genere, è necessario connettersi a ciascun database in modo indipendente per eseguire istruzioni T-SQL o effettuare altre attività amministrative. I **processi di database elastici** consentono di gestire l’attività di esecuzione dell’accesso e di eseguire in modo automatico e affidabile l'esecuzione dello script, registrando al contempo lo stato di esecuzione per ciascun database. Per istruzioni sull'installazione, vedere [Installazione dei componenti del processo di database elastico](sql-database-elastic-jobs-service-installation.md).

![Servizio processo di database elastico][1]

## Vantaggi
* Definire gruppi personalizzati di database SQL di Azure
* Definire, gestire e mantenere gli script T-SQL da eseguire su un gruppo di database SQL di Azure 
* Distribuire un'applicazione livello dati (DACPAC)
* Eseguire gli script Transact-SQL o l’applicazione dei DACPAC in modo affidabile con la ripetizione automatica e su larga scala
* Tenere traccia dello stato di esecuzione dei processi
* Definire pianificazioni di esecuzione
* Eseguire la raccolta dei dati in una raccolta di database SQL di Azure salvando i risultati in una tabella di destinazione singola

> [AZURE.NOTE]La funzionalità **Processi database elastici** nel portale di Azure utilizza una serie di funzionalità ridotte limitate ai pool elastici di SQL Azure. Utilizzare le API di PowerShell per accedere alla serie completa delle funzionalità correnti.

## Scenari

* Esecuzione di attività amministrative, ad esempio distribuire un nuovo schema
* Aggiornare i dati di riferimento, ad esempio informazioni sui prodotti più comuni in tutti i database, anche utilizzando pianificazioni per eseguire gli aggiornamenti in automatico ogni giorno della settimana dopo l’orario di chiusura.
* Ricompilazione degli indici per migliorare le prestazioni delle query. La ricompilazione può essere configurata per essere eseguita in una raccolta di database su base periodica, ad esempio durante le fasce orarie non di punta.
* Raccogliere i risultati di query da un set di database in una tabella centrale su base costante. Le query di prestazione possono essere eseguite continuamente e configurate per l'esecuzione di attività aggiuntive di trigger.
* Eseguire query di elaborazione dei dati più lunghe per una vasta serie di database, ad esempio la raccolta della telemetria del cliente. I risultati vengono raccolti in una tabella di destinazione singola per ulteriori analisi.

## Revisione end-to-end semplice dei processi di database elastici
1.	Installare i componenti dei **processi di database elastici**. Per ulteriori informazioni, vedere [Installare processi di database elastici](sql-database-elastic-jobs-service-installation.md). Se l'installazione non riesce, vedere [come disinstallare](sql-database-elastic-jobs-uninstall.md).
2.	Utilizzare le API di PowerShell per accedere a ulteriori funzionalità, ad esempio la creazione di raccolte di database personalizzati, l’aggiunta di pianificazioni e/o la raccolta di set di risultati. Utilizzare il portale per l'installazione semplice e la creazione/monitoraggio di processi limitati da eseguire su un **pool di database elastici**. 
3.	Creare le credenziali crittografate per l'esecuzione del processo e [aggiungere l'utente (o il ruolo) a ogni database nel gruppo](sql-database-elastic-jobs-add-logins-to-dbs.md).
4.	Creare uno script T-SQL idempotente che può essere eseguito su ogni database nel gruppo.
5.	Per eseguire lo script attenersi alla seguente procedura: [Creazione e gestione dei processi di database elastici](sql-database-elastic-jobs-create-and-manage.md) 

## Componenti e prezzi 
I seguenti componenti interagiscono per creare un servizio Cloud di Azure che consente l'esecuzione ad hoc dei processi di amministrazione. I componenti vengono installati e configurati automaticamente durante la configurazione, nella sottoscrizione. È possibile identificare i servizi poiché hanno tutti lo stesso nome generato automaticamente. Il nome è univoco ed è costituito dal prefisso "edj" seguito da 21 caratteri generati casualmente.

* **Servizio cloud di Azure**: i processi di database elastica (anteprima) vengono recapitati come servizio cloud di Azure ospitato dal cliente per l'esecuzione delle attività richieste. Dal portale, il servizio viene distribuito e ospitato nella sottoscrizione Microsoft Azure. Il servizio predefinito distribuito viene eseguito con un numero minimo di due ruoli di lavoro per la disponibilità elevata. La dimensione predefinita di ogni ruolo di lavoro (ElasticDatabaseJobWorker) viene eseguita in un'istanza A0. Per informazioni sui prezzi, vedere [Servizi cloud Prezzi](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Database SQL di Azure**: il servizio usa un database SQL di Azure noto come **database di controllo** per archiviare tutti i metadati del processo. Il livello di servizio predefinito è S0. Per informazioni sui prezzi, vedere [Database SQL Prezzi](http://azure.microsoft.com/pricing/details/sql-database/).
* **Bus di servizio di Azure**: è destinato alla coordinazione del lavoro all'interno del servizio cloud di Azure. Vedere [Bus di servizio Prezzi](http://azure.microsoft.com/pricing/details/service-bus/).
* **Archiviazione di Azure**: un account di Archiviazione di Azure viene utilizzato per archiviare la registrazione dell’output nell’evento che un problema richieda un debugging ulteriore (una pratica comune per la [diagnostica di Azure](../cloud-services-dotnet-diagnostics.md)). Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](http://azure.microsoft.com/pricing/details/storage/).

## Funzionano dei processi di database elastico
1.	A un database SQL di Azure viene designato un database di controllo che archivia tutti i dati dello stato e i metadati.
2.	Accedere al database di controllo da **processi database elastici** per avviare e tenere traccia dei processi da eseguire.
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

## Flusso di lavoro di esecuzione del processo End-to-end
1.	Tramite il portale o l'API di PowerShell, un processo viene inserito nel **database di controllo**. Il processo richiede l'esecuzione di uno script Transact-SQL su un gruppo di database che utilizza credenziali specifiche.
2.	Il controller identifica il nuovo processo. Le attività di processo vengono create ed eseguite per suddividere lo script e per aggiornare i database del gruppo. Infine, un nuovo processo viene creato ed eseguito per espandere il processo e creare nuovi processi figlio in cui ogni processo figlio è specificato per eseguire lo script Transact-SQL su un singolo database nel gruppo.
3.	Il controller identifica i processi figlio creati. Per ogni processo, il controller crea e avvia un'attività di processo per eseguire lo script su un database. 
4.	Dopo aver completato tutte le attività di processo, il controller aggiorna i processi nello stato ‘completato’. In qualsiasi momento durante l'esecuzione del processo, l'API di PowerShell può essere utilizzata per visualizzare lo stato corrente dell'esecuzione del processo. Tutte le volte restituite dalle API PowerShell sono rappresentate in formato UTC. Se si desidera, è possibile avviare una richiesta di annullamento per interrompere un processo. 

## Passaggi successivi
[Installare i componenti](sql-database-elastic-jobs-service-installation.md), quindi [creare e aggiungere un log a ciascun database nel gruppo di database](sql-database-elastic-jobs-add-logins-to-dbs.md). Per comprendere la creazione e la gestione del processo, vedere [Creazione e gestione di processi elastici di database](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO1-->