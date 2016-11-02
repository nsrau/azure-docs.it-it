<properties
   pageTitle="SQL Azure con Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come usare SQL Azure con Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# SQL Azure con Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Spesso, quando i clienti scelgono di ospitare le applicazioni Windows nel cloud con Azure RemoteApp, vogliono anche eseguire la migrazione dei dati, ad esempio dei server SQL, nel cloud per un'intera distribuzione cloud. Ciò consente di ospitare nel cloud l'intera soluzione che risulta accessibile ovunque in qualsiasi momento da qualsiasi dispositivo con Azure RemoteApp. Di seguito sono elencati collegamenti e riferimenti, oltre al materiale sussidiario utile in questo processo.

## Eseguire la migrazione dei dati di SQL

Iniziare con la [migrazione di un database SQL Server nel database SQL di Azure](../sql-database/sql-database-cloud-migrate.md).

## Configurare Azure RemoteApp
Ospitare l'applicazione Windows in Azure RemoteApp. Di seguito sono elencati i passaggi generali:

1.     Creare la [VM con il modello di Azure RemoteApp](remoteapp-imageoptions.md).
2.     Installare l'applicazione necessaria nella VM.
3.     Configurare l'applicazione in modo che si connetta al database SQL e verificare che funzioni.
4.     Preparare la VM con Sysprep e arrestarla. Acquisirla come immagine da usare con Azure. **Nota:** sarà necessario assicurarsi che l'applicazione possa conservare le informazioni sulla connettività con il database per tutto il processo sysprep. Se l'applicazione non riesce a conservare le informazioni sulla connessione con il database, è consigliabile consultare il fornitore dell'applicazione e chiedere come specificare la stringa di connessione.
5.     Importare l'immagine personalizzata nella libreria di Azure RemoteApp selezionando l'esatta posizione geografica in cui si trova la distribuzione di SQL Azure.
6.     Distribuire una raccolta RemoteApp nello stesso data center della distribuzione di SQL Azure usando il modello precedente e pubblicare l'applicazione. La distribuzione di Azure RemoteApp nello stesso data center della distribuzione di SQL Azure assicura la massima velocità di connessione e riduce la latenza.

## Considerazioni sulla configurazione dell'app e di SQL:
Quando si Azure SQL con RemoteApp, è opportuno considerare alcuni aspetti:

Vedere [Configurare le regole del firewall per il database SQL di Azure](../sql-database/sql-database-firewall-configure.md). In un punto dell'articolo si afferma: "Inizialmente, tutti gli accessi al server di database SQL di Azure sono bloccati dal firewall. Per iniziare a utilizzare il server di database SQL di Azure, è necessario accedere al portale classico e specificare una o più regole del firewall a livello di server che consentano di accedere al server di database SQL di Azure. Usare le regole del firewall per specificare quali intervalli di indirizzi IP di Internet sono consentiti e se le applicazioni Azure possono provare a connettersi al server di database SQL di Azure oppure no."

Quando poi un computer cerca di connettersi al server di database da Internet, il firewall confronta l'indirizzo IP di origine della richiesta con il set completo di regole del firewall a livello di server e, se necessario, a livello di database. "Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, è consentita la connessione al server di database SQL di Azure." Quindi è possibile usare intervalli IP e non solo singoli indirizzi IP di origine.

Per specificare l'intervallo IP, seguire le istruzioni dettagliate in [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](../sql-database/sql-database-configure-firewall-settings.md). Quando si configurano le regole del firewall SQL, fornire l'intervallo IP della subnet specificato per la raccolta Azure RemoteApp. Ciò consentirà ai server Azure RemoteApp di connettersi al database SQL anche se avranno indirizzi IP assegnati in modo dinamico.

## Risoluzione dei problemi
Se si è rallentati dall'uso di un'applicazione client ospitata in Azure RemoteApp che si connette a un database SQL ospitato in Azure o in locale, i motivi possono essere più di uno.

- La latenza di rete dal dispositivo ad Azure è elevata. Per prestazioni ottimali, usare la connessione di rete più efficiente e rapida possibile. Usare [azurespeed.com](http://azurespeed.com/) come strumento generale per testare la latenza dei dispositivi nel data center di Azure.
- L'app client ospitata in Azure RemoteApp è in condizione di stress. Selezionare un altro piano di fatturazione, ad esempio la fatturazione Premium, per migliorare le prestazioni. Un altro espediente consiste nel monitorare le risorse utilizzate dall'applicazione: durante una sessione attiva eseguire una sequenza di tasti CTRL+ALT+FINE per avviare la schermata della firma di accesso condiviso, selezionare Gestione attività e osservare l'utilizzo delle risorse per l'app.
- SQL Server è in condizioni di stress o non è ottimizzato. Seguire le indicazioni relative a SQL per la risoluzione dei problemi.

<!---HONumber=AcomDC_0817_2016-->