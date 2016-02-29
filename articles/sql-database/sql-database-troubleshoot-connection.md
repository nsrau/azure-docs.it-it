<properties
	pageTitle="Risoluzione dell'errore Il database nel server non è attualmente disponibile per il database SQL di Azure"
	description="Passaggi per identificare e risolvere gli errori di connessione per il database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="daleche"/>

# Risoluzione dell'errore "Il database nel server non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento." e altri errori di connessione.
"Il database <dbname> nel server <servername> non è attualmente disponibile..." è l'errore di connessione temporaneo più comune per il database SQL di Azure. Gli errori di connessione temporanei sono in genere quelli che si verificano a causa di un evento pianificato, ad esempio un aggiornamento del software, o di un evento non pianificato, ad esempio l'arresto anomalo di un processo. Tali errori hanno in genere breve durata, da un minimo di pochi secondi a un massimo di un minuto. Se si visualizza un errore diverso, esaminare il [messaggio di errore](sql-database-develop-error-messages.md) per ottenere indicazioni sulla causa, determinare se il problema è temporaneo o permanente e seguire le indicazioni riportate in questo argomento.

## Passaggi per risolvere problemi di connettività temporanei
1.	Verificare il [Dashboard servizi di Microsoft Azure](https://azure.microsoft.com/status) per le interruzioni note.
2.	Assicurarsi che l'app usi una logica di tentativi. Per le strategie generali di ripetizione dei tentativi, vedere i [problemi di connettività](sql-database-connectivity-issues.md) e le [procedure consigliate e le linee guida di progettazione](sql-database-connect-central-recommendations.md). Per informazioni dettagliate, vedere anche gli [esempi di codice](sql-database-develop-quick-start-client-code-samples.md).
3.	Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si sia verificato un problema di connettività temporaneo. Vedere la pagina relativa alla [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4.	Se i problemi di connettività persistono, inviare una richiesta di supporto tecnico selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options).

## Passaggi per risolvere problemi di connettività permanenti
Se l'app non è in grado di connettersi, in genere si tratta di un problema con la configurazione IP e firewall. Può trattarsi di un problema di riconfigurazione di rete sul lato client, ad esempio un nuovo indirizzo IP o un nuovo proxy. È anche comune che siano presenti parametri di connessione errati, ad esempio la stringa di connessione.

1.	Impostare le [regole del firewall](sql-database-configure-firewall-settings.md) per consentire l'indirizzo IP del client.
2.	Assicurarsi che la porta 1433 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet.
3.	Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere la sezione sulla stringa di connessione nell'[argomento relativo ai problemi di connettività](sql-database-connectivity-issues.md).
4.	Controllare lo stato del servizio nel dashboard. Se si ritiene che si sia verificata un'interruzione regionale, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per i passaggi di ripristino in una nuova area.
5.	Se i problemi di connettività persistono, inviare una richiesta di supporto tecnico selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options).

<!---HONumber=AcomDC_0218_2016-->