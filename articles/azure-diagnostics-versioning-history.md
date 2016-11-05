---
title: Cronologia delle versioni di Diagnostica di Azure
description: Descrizione delle modifiche nelle diverse versioni di Diagnostica di Azure fornite con le diverse versioni di Microsoft Azure SDK.
services: multiple
documentationcenter: .net
author: rboucher
manager: jwhit
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2016
ms.author: robb

---
# Cronologia delle versioni di Diagnostica di Azure
Se si è un nuovo utente di Diagnostica di Azure, vedere [Panoramica di Diagnostica di Azure](azure-diagnostics.md).

In genere, ogni versione di Azure SDK viene fornita con una nuova versione di Diagnostica di Azure. Nella tabella seguente sono descritte le versioni di Azure SDK e le versioni di Diagnostica di Azure associate.

| Versione di Azure SDK | Versione di Diagnostica di Azure | Modello |
| --- | --- | --- |
| 1\.x |1\.0 |plug-in |
| Da 2.0 a 2.4 |1\.0 |" |
| 2\.5 |1\.2 |estensione |
| 2\.6 |1\.3 |" |
| 2\.7 |1\.4 |" |
| 2\.8 |1,5 |" |

La versione più recente è la 1.5, fornita con Azure SDK 2.8. La versione dell'estensione Diagnostica di Azure che viene fornita con l'SDK viene usata solo per scenari di emulatore locale. Durante l'esecuzione in Azure, qualsiasi applicazione distribuita usa automaticamente la versione più recente, indipendentemente dalla versione del SDK con cui è compilata. Tuttavia, a meno che non si installi l'SDK più recente, è possibile che non siano disponibili tutti gli strumenti che consentono di usare le nuove funzionalità.

Funzionalità e modifiche descritte di seguito.

## Azure SDK 2.8
Azure SDK 2.8 offre la possibilità di inviare dati di diagnostica ad [Application Insights](application-insights/app-insights-cloudservices.md) semplificando la diagnosi dei problemi nell'intera applicazione, nonché a livello di sistema e infrastruttura.

## Modifiche alla diagnostica di Azure 2.6
Sono state apportate le modifiche seguenti ai progetti dei servizi cloud di Azure SDK 2.6 in Visual Studio. Queste modifiche sono applicabili anche alle versioni successive di Azure SDK.

* L'emulatore locale supporta ora la diagnostica. È quindi possibile raccogliere dati di diagnostica e assicurare che l'applicazione crei le tracce corrette durante lo sviluppo e il testing in Visual Studio. La stringa di connessione `UseDevelopmentStorage=true` abilita la raccolta di dati di diagnostica durante l'esecuzione del progetto servizio cloud in Visual Studio mediante l'emulatore di archiviazione di Azure. Tutti i dati di diagnostica vengono raccolti nell'account di archiviazione (archivio di sviluppo).
* La stringa di connessione dell'account di archiviazione di diagnostica (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) viene archiviata ancora una volta nel file di configurazione (.cscfg) del servizio. In Azure SDK 2.5 l'account di archiviazione di diagnostica viene specificato nel file diagnostics.wadcfgx.

Il funzionamento della stringa di connessione presenta alcune differenze sostanziali tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.6 e versioni successive.

* In Azure SDK 2.4 e versioni precedenti la stringa di connessione viene usata come runtime dal plug-in di diagnostica per ottenere le informazioni sull'account di archiviazione per il trasferimento dei log di diagnostica.
* In Azure SDK 2.6 e versioni successive la stringa di connessione di diagnostica viene usata da Visual Studio per configurare l'estensione di diagnostica con le informazioni appropriate sull'account di archiviazione durante la pubblicazione. La stringa di connessione consente di definire diversi account di archiviazione per diverse configurazioni del servizio, che verranno usate da Visual Studio durante la pubblicazione. Poiché, tuttavia, il plug-in di diagnostica non è più disponibile (dopo Azure SDK 2.5), il solo file .cscfg non è in grado di abilitare l'estensione di diagnostica. È necessario abilitare l'estensione separatamente tramite strumenti quali Visual Studio o PowerShell.
* Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto da Visual Studio contiene anche il codice XML di configurazione pubblica per l'estensione di diagnostica per ogni ruolo. Visual Studio usa la stringa di connessione di diagnostica per popolare le informazioni sull'account di archiviazione presenti nella configurazione pubblica. I file di configurazione pubblica vengono creati nella cartella Extensions e seguono il modello PaaSDiagnostics.<RoleName>.PubConfig.xml. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.
* La stringa di connessione nel file .cscfg viene usata anche dal portale di Azure per accedere ai dati di diagnostica, per consentirne la visualizzazione nella scheda **Monitoraggio**. La stringa di connessione è necessaria per configurare il servizio, in modo da visualizzare i dati dettagliati del monitoraggio nel portale.

### Migrazione di progetti in Azure SDK 2.6 e versioni successive
Quando si esegue la migrazione da Azure SDK 2.5 ad Azure SDK 2.6 o versioni successivi, eventuali account di archiviazione di diagnostica specificati nel file con estensione wadcfgx rimarranno in tale posizione. Per sfruttare la flessibilità di utilizzo di account di archiviazione diversi per diverse configurazioni di archiviazione, è necessario aggiungere manualmente la stringa di connessione al progetto. Se si sta eseguendo la migrazione di un progetto da Azure SDK 2.4 o versioni precedenti ad Azure SDK 2.6, le stringhe di connessione di diagnostica vengono mantenute. Occorre tuttavia notare le modifiche relative al modo in cui le stringhe di connessione vengono gestite in Azure SDK 2.6, come illustrato nella sezione precedente.

### Modalità di determinazione dell'account di archiviazione di diagnostica da parte di Visual Studio
* Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio la userà per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.
* Se nel file .cscfg non viene specificata alcuna stringa di connessione di diagnostica, Visual Studio userà di nuovo l'account di archiviazione specificato nel file con estensione wadcfgx per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.
* La stringa di connessione di diagnostica nel file .cscfg ha la priorità rispetto all'account di archiviazione specificato nel file con estensione wadcfgx. Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio userà tale stringa e ignorerà l'account di archiviazione specificato nel file con estensione wadcfgx.

### Funzione della casella di controllo "Aggiorna stringhe di connessione risorsa di archiviazione sviluppo…"
La casella di controllo **Aggiorna le stringhe di connessione dell'archivio di sviluppo per la diagnostica e la memorizzazione nella cache con le credenziali dell'account di archiviazione di Microsoft Azure quando si esegue la pubblicazione in Microsoft Azure** offre un modo semplice per aggiornare eventuali stringhe di connessione dell'account di archiviazione con l'account di archiviazione di Azure specificato durante la pubblicazione.

Ad esempio, si supponga di selezionare questa casella di controllo e che la stringa di connessione di diagnostica specifichi `UseDevelopmentStorage=true`. Quando si pubblica il progetto in Azure, Visual Studio aggiornerà automaticamente la stringa di connessione di diagnostica con l'account di archiviazione specificato nella Pubblicazione guidata. Se, tuttavia, un account di archiviazione effettivo è stato specificato come stringa di connessione di diagnostica, verrà usato invece tale account.

## Differenze della funzionalità di diagnostica tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.5 e versioni successive
Se si aggiorna il progetto da Azure SDK 2.4 ad Azure SDK 2.5 o versioni successive, è consigliabile ricordare le differenze seguenti a livello di funzionalità di diagnostica.

* **Le API di configurazione sono deprecate**: la configurazione a livello di codice della diagnostica è disponibile in Azure SDK 2.4 o versioni precedenti, ma è deprecata in Azure SDK 2.5 e versioni successive. Se la configurazione di diagnostica è attualmente definita nel codice, sarà necessario riconfigurare completamente tali impostazioni nel progetto sottoposto a migrazione, in modo da mantenere il funzionamento della diagnostica. Il file di configurazione della diagnostica per Azure SDK 2.4 è diagnostics.wadcfg, mentre è diagnostics.wadcfgx per Azure SDK 2.5 e versioni successive.
* **La diagnostica per le applicazioni del servizio cloud può essere configurata solo a livello di ruolo, non a livello di istanza.**
* **Ogni volta che si distribuisce l'app, le configurazioni di diagnostica vengono aggiornate**: ciò può provocare problemi di parità se si modifica la configurazione di diagnostica da Esplora server e quindi si ridistribuisce l'app.
* **In Azure SDK 2.5 e versioni successive i dump di arresto anomalo del sistema sono configurati nel file di configurazione, non nel codice**: se i dump di arresto anomalo del sistema sono configurati nel codice, sarà necessario trasferire manualmente la configurazione dal codice al file di configurazione, perché i dump di arresto anomalo del sistema non vengono trasferiti durante la migrazione in Azure SDK 2.6.

<!---HONumber=AcomDC_0302_2016-->