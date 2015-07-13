<properties 
	pageTitle="Installazione dei processi di database elastici" 
	description="Installazione dettagliata della funzionalità dei processi elastici." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# Installazione dei componenti del processo di database elastico

Il [pool di database elastico (anteprima)](sql-database-elastic-pool-portal.md) fornisce un modello stimabile per la distribuzione di un numero elevato di database. Dopo aver creato un pool di database elastico, è possibile utilizzare **processi database elastici** per eseguire attività amministrative in ogni database nel pool di database elastico. Ad esempio, è possibile distribuire il nuovo schema, come l'impostazione di un criterio RLS su ciascun database per limitare i dati unicamente all'utente con le credenziali corrette per visualizzare i dati riservati. Di seguito viene illustrato come installare i **processi di database elastici**.

**Tempo previsto per il completamento:** 10 minuti

## Prerequisiti
* Una sottoscrizione di Azure. Per una versione di valutazione gratuita, vedere [Versione di valutazione gratuita di un mese](http://azure.microsoft.com/pricing/free-trial/).
* Un pool di database elastico Vedere [Creare un pool elastico di database SQL di Azure (anteprima)](sql-database-elastic-pool-portal.md).

## Installare i componenti del servizio
Innanzitutto, accedere al [portale di anteprima di Azure](https://ms.portal.azure.com/#).


1. Dalla vista dashboard del pool di database elastico, fare clic su **Crea processo**.
2. Se si sta creando un processo per la prima volta, è necessario installare **processi di database elastici** facendo clic su **ANTEPRIMA TERMINI**. 
3. Accettare i termini selezionando la casella di controllo.
4. Nella vista "Installa servizi", fare clic su **CREDENZIALI PROCESSO**.

	![Installazione dei servizi][1]

5. Digitare un nome utente e una password per un amministratore del database. Se un utente comune per l'esecuzione di script esiste già in ogni database nel pool di database elastico, è consigliabile utilizzare questo stesso utente per eliminare la necessità di aggiungere un nuovo utente a ciascun database per l'esecuzione di script. Come parte dell'installazione, viene creato un nuovo server di database SQL di Azure. All'interno di questo nuovo server, viene creato un nuovo database, noto come database di controllo, che viene utilizzato per contenere i metadati per i processi di database elastici. Il nome utente e la password creati qui vengono utilizzati per due scopi: (1) per accedere al database di controllo e (2), come credenziali utilizzate per accedere a ogni database nel pool elastico ogni volta che si esegue un processo per l’esecuzione di script.
 
	![Creare nome utente e password][2]
6. Fare clic sul pulsante OK. I componenti vengono creati automaticamente in pochi minuti in un nuovo [gruppo di risorse](../resource-group-portal.md). Il nuovo gruppo di risorse viene bloccato sulla schermata iniziale, come illustrato di seguito. Una volta creati, i processi di database elastici (Servizio cloud, database SQL, Bus di servizio e Archiviazione) vengono creati tutti nel gruppo.

	![gruppo di risorse nella schermata iniziale][3]


7. Se si tenta di creare o gestire un processo mentre si installano i processi di database elastici, nel momento in cui vengono fornite le **credenziali** verrà visualizzato il messaggio seguente.

	![Distribuzione ancora in corso][4]

8. Se l'installazione non riesce per qualche motivo, eliminare il gruppo di risorse. Vedere [Come disinstallare i componenti del processo di database elastico](sql-database-elastic-jobs-uninstall.md).


## Passaggi successivi

Se durante l’installazione dei processi di database elastici viene fornita una nuova credenziale che non è già presente in ciascun database nel pool di database elastico con i diritti appropriati per l'esecuzione di script, è necessario creare le credenziali per ciascun database. Vedere [Come aggiungere utenti a un pool di database elastico](sql-database-elastic-jobs-add-logins-to-dbs.md). Per comprendere la creazione del processo, vedere [Creazione e gestione di un processo di database elastico](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=62-->