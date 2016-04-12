<properties
	pageTitle="Risoluzione dei problemi di connessione comuni al database SQL di Azure"
	description="Passaggi per identificare e risolvere gli errori di connessione comuni al database SQL di Azure."
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
	ms.date="03/29/2016"
	ms.author="daleche"/>

# Risoluzione dei problemi di connessione comuni al database SQL di Azure
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Le problematiche di connessione al database SQL di Azure possono essere classificate a grandi linee nelle seguenti categorie:

- Errori temporanei (di breve durata o intermittenti)
- Errori non temporanei o permanenti (gli errori che si ripetono regolarmente)

Se l'applicazione presenta errori temporanei, esaminare i seguenti argomenti per suggerimenti sulla risoluzione dei problemi e per ridurre la frequenza di questi errori:

- [Risoluzione dei problemi database &lt;x&gt; sul server &lt;y&gt; non disponibile (errore: 40613)](sql-database-troubleshoot-connection.md)
- [Risolvere, diagnosticare ed evitare gli errori di connessione SQL e gli errori temporanei per il database SQL](sql-database-connectivity-issues.md)

Se l'applicazione non riesce a connettersi in maniera costante al database SQL Azure, il problema è uno dei seguenti:

- Configurazione del firewall. Il firewall del database SQL di Azure o lato client blocca le connessioni al database SQL di Azure.
- Riconfigurazione della rete lato client: ad esempio un nuovo indirizzo IP o un nuovo server proxy.
- Errore dell'utente: ad esempio, digitazione errata dei parametri di connessione, come il nome del server nella stringa di connessione.

## Passaggi per risolvere problemi di connettività permanenti
1.	Impostare le [regole del firewall](sql-database-configure-firewall-settings.md) per consentire l'indirizzo IP del client.
2.	Assicurarsi che la porta 1433 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet. Per ulteriori indicazioni, vedere [Configurare il firewall di Windows per consentire l’accesso all’SQL Server](https://msdn.microsoft.com/library/cc646023.aspx).
3.	Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere la sezione sulla stringa di connessione nell'[argomento relativo ai problemi di connettività](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.	Controllare lo stato del servizio nel dashboard. Se si ritiene che si sia verificata un'interruzione regionale, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per i passaggi di ripristino in una nuova area.
5.	Se i problemi di connettività persistono, inviare una richiesta di supporto tecnico selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options).

<!---HONumber=AcomDC_0330_2016-->