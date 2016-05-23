<properties
	pageTitle="Risoluzione dei problemi end-to-end mediante le metriche e la registrazione di Archiviazione di Azure, AzCopy e Message Analyzer | Microsoft Azure"
	description="Esercitazione che illustra la risoluzione dei problemi end-to-end mediante Analisi archiviazione di Azure, AzCopy e Microsoft Message Analyzer"
	services="storage"
	documentationCenter="dotnet"
	authors="robinsh"
	manager="carmonm"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="robinsh"/>


# Risoluzione dei problemi end-to-end mediante le metriche e la registrazione di Archiviazione di Azure, AzCopy e Message Analyzer

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## Panoramica

Diagnostica e risoluzione dei problemi sono competenze fondamentali per la creazione e il supporto di applicazioni client con Archiviazione di Microsoft Azure. Data la natura distribuita di un'applicazione Azure, la diagnostica e la risoluzione dei problemi di prestazioni possono risultare più complesse che in ambienti tradizionali.

In questa esercitazione viene illustrato come identificare alcuni errori che potrebbero influire sulle prestazioni e come risolverli in modo end-to-end usando gli strumenti forniti da Microsoft e Archiviazione di Azure, per ottimizzare l'applicazione client.

Questa esercitazione offre un'esplorazione pratica di uno scenario di risoluzione dei problemi end-to-end. Per una guida approfondita sui concetti relativi alla risoluzione dei problemi delle applicazioni di archiviazione di Azure, vedere [Monitorare, diagnosticare e risolvere i problemi dell'Archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## Strumenti per la risoluzione dei problemi delle applicazioni di archiviazione di Azure

Per risolvere i problemi relativi alle applicazioni client mediante Archiviazione di Microsoft Azure, è possibile usare una combinazione di strumenti per determinare quando si è verificato un problema e quale potrebbe essere la sua causa. Questi strumenti comprendono:

- **Analisi archiviazione di Azure**. [Analisi archiviazione di Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) fornisce metriche e registrazioni per Archiviazione di Azure.
	- Le **metriche di archiviazione** tengono traccia delle metriche relative alle transazioni e alla capacità per l'account di archiviazione. Le metriche consentono di determinare le prestazioni dell'applicazione in base a un'ampia gamma di misure diverse. Per altre informazioni sui tipi di metriche di cui Analisi archiviazione tiene traccia, vedere [Schema di tabella della metrica di Analisi archiviazione](http://msdn.microsoft.com/library/azure/hh343264.aspx).

	- La **registrazione di archiviazione** registra tutte le richieste in arrivo ai servizi di Archiviazione di Azure in un log sul lato server. Nel log vengono registrati dati dettagliati per ogni richiesta, tra cui l'operazione eseguita, lo stato dell'operazione e le informazioni sulla latenza. Per altre informazioni sui dati di richiesta e risposta che vengono scritti nei log di Analisi di archiviazione, vedere [Formato del log di Analisi archiviazione](http://msdn.microsoft.com/library/azure/hh343259.aspx).

> [AZURE.NOTE] Per gli account di archiviazione con un tipo di replica di archiviazione con ridondanza della zona (ZRS) al momento non sono abilitate le funzionalità di metrica e registrazione.

- **Portale di Azure**. Nel [portale di Azure](https://portal.azure.com) è possibile configurare le metriche e la registrazione per l'account di archiviazione. È possibile anche visualizzare grafici che mostrano le prestazioni dell'applicazione nel tempo e configurare gli avvisi per ricevere una notifica se le prestazioni dell'applicazione si discostano dal previsto per una determinata metrica.

	Per informazioni sulla configurazione del monitoraggio nel portale di Azure, vedere [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md).

- **AzCopy**. I log di Archiviazione di Azure vengono memorizzati come BLOB, quindi è possibile usare AzCopy per copiare i BLOB di log in una directory locale per l'analisi mediante Microsoft Message Analyzer. Per altre informazioni su AzCopy, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

- **Microsoft Message Analyzer**. Message Analyzer è uno strumento che utilizza i file di log e visualizza i dati di log in un formato visivo che ne semplifica il filtraggio, la ricerca e il raggruppamento in set utili da usare per analizzare gli errori e i problemi di prestazioni. Per altre informazioni su Message Analyzer, vedere [la guida operativa di Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

## Informazioni sullo scenario di esempio

In questa esercitazione verrà esaminato uno scenario in cui le metriche di Archiviazione di Azure indicano una bassa percentuale di operazioni riuscite per un'applicazione che chiama Archiviazione di Azure. La metrica relativa alla bassa percentuale di operazioni riuscite (visualizzata come **PercentSuccess** nel [portale di Azure](https://portal.azure.com) e nelle tabelle metriche) tiene traccia delle operazioni che hanno esito positivo, ma che restituiscono con un codice di stato HTTP maggiore di 299. Nei file di log dell'archiviazione sul lato server, queste operazioni vengono registrate con stato della transazione **ClientOtherErrors**. Per maggiori dettagli sulla metrica relativa alla bassa percentuale di operazioni riuscite, vedere [Le metriche indicano un valore PercentSuccess basso o le voci del log contengono operazioni con stato della transazione ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Le operazioni di Archiviazione di Azure possono restituire codici di stato HTTP maggiori di 299 in condizioni di funzionalità normali. In alcuni casi, tuttavia, questi errori indicano che è possibile ottimizzare l'applicazione client per migliorare le prestazioni.

In questo scenario la percentuale di operazioni riuscite sarà considerata bassa se inferiore al 100%. È comunque possibile scegliere un diverso livello di metrica, in base alle esigenze. Durante il test dell'applicazione è consigliabile definire una tolleranza di base per le metriche delle prestazioni chiave. Ad esempio, sulla base dei test è possibile che si stabilisca che l'applicazione deve avere una percentuale di operazioni riuscite costante del 90% o 85%. Se i dati di metrica mostrano una deviazione dell'applicazione da tale valore, è possibile indagare per individuare la causa dell'aumento.

Nello scenario di esempio, dopo avere stabilito che la metrica della percentuale di operazioni riuscite è inferiore al 100%, si esamineranno i log per individuare gli errori correlati alle metriche, che saranno usati per risalire al motivo per cui la percentuale di operazioni riuscite è inferiore. Si osserveranno in particolare gli errori nella fascia 400, esaminando in dettaglio gli errori 404 (Non trovato).

### Alcune cause degli errori della fascia 400

Gli esempi seguenti presentano un campione di errori della serie 400 per le richieste nell'archivio BLOB di Azure e le possibili cause. Tutti questi errori, così come quelli nella fascia 300 e 500, possono contribuire a una bassa percentuale di operazioni riuscite.

Tenere presente che gli elenchi riportati sotto sono tutt'altro che completi. Per informazioni dettagliate sugli errori generali di Archiviazione di Azure e sugli errori specifici dei singoli servizi di archiviazione, vedere [Codici di stato e di errore](http://msdn.microsoft.com/library/azure/dd179382.aspx) su MSDN.

**Esempi di codice di stato 404 (Non trovato)**

Si verifica quando un'operazione di lettura in un contenitore o BLOB non riesce perché non viene trovato il BLOB o il contenitore.

- Si verifica se un contenitore o BLOB è stato eliminato da un altro client prima di questa richiesta.
- Si verifica se si usa una chiamata API che crea il contenitore o il BLOB dopo averne controllato l'esistenza. Le API CreateIfNotExists effettuano una chiamata HEAD per verificare l'esistenza del contenitore o del BLOB. Se non esiste, viene restituito un errore 404 e quindi viene eseguita una seconda chiamata PUT per scrivere il contenitore o il BLOB.

**Esempi di codice di stato 409 (Conflitto)**

- Si verifica se si usa un'API di creazione per creare un nuovo contenitore o BLOB, senza controllarne prima l'esistenza, ed esiste già un contenitore o BLOB con lo stesso nome.
- Si verifica se è in corso l'eliminazione di un contenitore e si tenta di crearne uno nuovo con lo stesso nome prima del completamento dell'operazione di eliminazione.
- Si verifica se si specifica un lease in un contenitore o BLOB ed è già presente un lease.

**Esempi di codice di stato 412 (Condizione preliminare non riuscita)**

- Si verifica quando la condizione specificata da un'intestazione condizionale non viene soddisfatta.
- Si verifica quando l'ID lease specificato non corrisponde all'ID lease per il contenitore o BLOB.

## Generare file di log per l'analisi

In questa esercitazione viene usato Message Analyzer per utilizzare tre diversi tipi di file di log, anche se è possibile scegliere di utilizzarne uno:

- Il **log del server**, che viene creato quando abilita la registrazione di Archiviazione di Azure. Il log del server contiene dati relativi a ogni operazione chiamata in uno dei servizi di Archiviazione di Azure, ovvero BLOB, accodamento, tabelle e file. Il log del server indica l'operazione che è stato chiamata e il codice di stato restituito, nonché altri dettagli sulla richiesta e risposta.
- Il **log del client .NET**, che viene creato quando si abilita la registrazione sul lato client dall'applicazione .NET. Il log del client include informazioni dettagliate sul modo in cui il client prepara la richiesta e riceve ed elabora la risposta.
- Il **log di traccia di rete HTTP**, che raccoglie i dati relativi alle richieste e risposte HTTP/HTTPS, anche per le operazioni in Archiviazione di Azure. In questa esercitazione, la traccia di rete verrà generata mediante Message Analyzer.

### Configurare le metriche e la registrazione sul lato server

In primo luogo è necessario configurare la registrazione e le metriche di Archiviazione di Azure, in modo da disporre di dati dell'applicazione client per l'analisi. La registrazione e le metriche possono essere configurate in diversi modi: tramite il [portale di Azure](https://portal.azure.com), tramite PowerShell o a livello di codice. Per informazioni dettagliate sulla registrazione e sulle metriche, vedere [Abilitazione di Metriche di archiviazione e visualizzazione dei dati di metrica](http://msdn.microsoft.com/library/azure/dn782843.aspx) e [Abilitazione di Registrazione archiviazione e accesso ai dati di log](http://msdn.microsoft.com/library/azure/dn782840.aspx) su MSDN.

**Tramite il portale di Azure**

Per configurare registrazione e metrica per l'account di archiviazione tramite il [portale di Azure](https://portal.azure.com), seguire le istruzioni in [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Non è possibile impostare la metrica al minuto mediante il portale di Azure. Tuttavia, è consigliabile impostarla ai fini di questa esercitazione e per l'analisi dei problemi di prestazioni relativi all'applicazione. La metrica al minuto può essere impostata tramite PowerShell, come mostrato di seguito, o a livello di codice o tramite la libreria del client di archiviazione.
>
> Si noti che il portale di Azure non consente di visualizzare la metrica al minuto, ma solo la metrica oraria.

**Tramite PowerShell**

Per informazioni introduttive su PowerShell per Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

1. Usare il cmdlet [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) per aggiungere l'account utente di Azure alla finestra di PowerShell:

	```
	Add-AzureAccount
	```

2. Nella finestra di **accesso a Microsoft Azure** digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.
3. Impostare l'account di archiviazione predefinito sull'account di archiviazione usato per l'esercitazione eseguendo i comandi seguenti nella finestra di PowerShell:

	```
	$SubscriptionName = 'Your subscription name'
	$StorageAccountName = 'yourstorageaccount'
	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
	```

4. Abilitare la registrazione di archiviazione per il servizio BLOB:

	```
	Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
	```
5. Abilitare la metrica di archiviazione per il servizio BLOB, assicurandosi di impostare **-MetricsType** su `Minute`:

	```
	Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
	```

### Configurare la registrazione sul lato client .NET

Per configurare la registrazione sul lato client per un'applicazione .NET, abilitare la diagnostica .NET nel file di configurazione dell'applicazione (web.config o app.config). Per informazioni dettagliate, vedere [Registrazione lato client con la libreria client di archiviazione .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) e [Registrazione lato client con Microsoft Azure Storage SDK per Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) su MSDN.

Il log lato client include informazioni dettagliate sul modo in cui il client prepara la richiesta e riceve ed elabora la risposta.

La libreria client di archiviazione archivia i dati di log lato client nel percorso specificato nel file di configurazione dell'applicazione (web.config or app.config).

### Raccogliere una traccia di rete

È possibile usare Message Analyzer per raccogliere una traccia di rete HTTP/HTTPS mentre l'applicazione client è in esecuzione. Message Analyzer usa [Fiddler](http://www.telerik.com/fiddler) sul lato back-end. Prima di raccogliere la traccia di rete, è consigliabile configurare Fiddler per registrare il traffico HTTPS non crittografato:

1. Installare [Fiddler](http://www.telerik.com/download/fiddler).
2. Avviare Fiddler.
2. Selezionare **Strumenti | Opzioni Fiddler**.
3. Nella finestra di dialogo Opzioni, verificare che siano selezionate le opzioni **Acquisisci HTTPS CONNECTs** e **Decrittografa il traffico HTTPS**, come illustrato di seguito.

![Configurare le opzioni Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Per l'esercitazione, raccogliere e salvare una traccia di rete in Message Analyzer, quindi creare una sessione di analisi per analizzare la traccia e i log. Per raccogliere una traccia di rete in Message Analyzer:

1. In Message Analyzer selezionare **File | Traccia rapida | HTTPS non crittografato**.
2. La traccia inizierà immediatamente. Selezionare **Interrompi** per interrompere la traccia in modo da poterla configurare solo per il traffico di archiviazione.
3. Selezionare **Modifica** per modificare la sessione di traccia.
4. Selezionare il collegamento **Configura** a destra del provider ETW **Microsoft-Pef-WebProxy**.
5. Nella finestra di dialogo **Impostazioni avanzate**, fare clic sulla scheda **Provider**.
6. Nel campo **Filtro Hostname Filter**, specificare gli endpoint di archiviazione, separati da spazi. Ad esempio, è possibile specificare gli endpoint nel modo seguente, specificando il nome del proprio account di archiviazione al posto di `storagesample`:

	```
	storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
	```

7. Chiudere la finestra di dialogo e fare clic su **Riavvia** per avviare la raccolta della traccia con il filtro relativo al nome host impostato, in modo da includere nella traccia solo il traffico di rete di Archiviazione di Azure.

>[AZURE.NOTE] Al termine della raccolta della traccia di rete, è consigliabile ripristinare le impostazioni modificate in Fiddler per decrittografare il traffico HTTPS. Nella finestra di dialogo Opzioni Fiddler deselezionare le caselle di controllo **Acquisisci HTTPS CONNECTs** e **Decrittografa il traffico HTTPS**.

Per informazioni dettagliate, vedere [l'argomento relativo all'uso delle funzionalità di traccia di rete](http://technet.microsoft.com/library/jj674819.aspx) su Technet.

## Esaminare i dati di metrica nel portale di Azure

Dopo un certo periodo di esecuzione dell'applicazione, è possibile esaminare i grafici delle metriche visualizzati nel [portale di Azure](https://portal.azure.com) per verificare le prestazioni del servizio. Innanzitutto, passare all'account di archiviazione nel portale di Azure e aggiungere un grafico per la metrica **Percentuale di successo**.

Nel portale di Azure, si vedrà ora **Percentuale di successo** all’interno del grafico di monitoraggio, insieme alle altre metriche che sono state aggiunte. La percentuale di operazioni riuscite è leggermente inferiore al 100%. Questo è lo scenario che si passerà a esaminare analizzando i log in Message Analyzer.

Per maggiori dettagli sull'aggiunta di metriche alla pagina di monitoraggio, vedere [Procedura: aggiungere metriche alla relativa tabella](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Dopo avere abilitato la metrica di archiviazione, è possibile che la visualizzazione dei dati corrispondenti nel portale di Azure non sia immediata. Questo dipende dal fatto che la metrica oraria per l'ora precedente viene visualizzata nel portale di Azure solo allo scadere dell'ora in corso. Inoltre, la metrica oraria non è attualmente visualizzata nel portale di Azure. Quindi, per visualizzare i dati relativi alla metrica può essere necessaria fino a un'ora, a seconda del momento in cui è stata abilita.

## Usare AzCopy per copiare i log del server in una directory locale

Archiviazione di Azure scrive i dati di log del server nei BLOB, mentre le metriche vengono scritti nelle tabelle. I BLOB di log sono disponibili nel noto contenitore `$logs` per l'account di archiviazione. Dato che i BLOB sono denominati in modo gerarchico per anno, mese, giorno e ora, è possibile individuare facilmente l'intervallo di tempo da esaminare. Ad esempio, nell'account `storagesample`, il contenitore per i BLOB di log relativi al 02/01/2015, dalle 8 alle 9, è `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. I singoli BLOB nel contenitore sono denominati in sequenza, a partire da `000000.log`.

È possibile usare lo strumento da riga di comando AzCopy per scaricare questi file di log lato server nel percorso desiderato sul computer locale. Ad esempio, tramite il comando seguente è possibile scaricare i file di log per le operazioni BLOB verificatesi il 2 gennaio 2015 nella cartella`C:\Temp\Logs\Server`, sostituendo `<storageaccountname>` con il nome del proprio account di archiviazione e `<storageaccountkey>` con la chiave di accesso dell'account:

	AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy è disponibile per il download nella pagina [Download di Azure](https://azure.microsoft.com/downloads/). Per informazioni dettagliate sull'uso di AzCopy, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

Per altre informazioni sul download dei log sul lato server, vedere [Download dei dati di log della registrazione di archiviazione](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## Usare Microsoft Message Analyzer per analizzare i dati di log

Microsoft Message Analyzer è uno strumento per l'acquisizione, la visualizzazione e l'analisi del traffico di messaggistica del protocollo, degli eventi e di altri messaggi del sistema o dell'applicazione in scenari di diagnostica e risoluzione dei problemi. Message Analyzer consente anche di caricare, aggregare e analizzare i dati da log e file di traccia salvati. Per altre informazioni su Message Analyzer, vedere la [guida operativa di Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer include risorse per Archiviazione di Azure che consentono di analizzare i log di rete, client e server. In questa sezione viene spiegato come usare questi strumenti per risolvere il problema della bassa percentuale di operazioni riuscite nel log di archiviazione.

### Scaricare e installare Message Analyzer e le risorse per Archiviazione di Azure

1. Scaricare [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) dall'Area download Microsoft ed eseguire il programma di installazione.
2. Avviare Message Analyzer.
3. Dal menu **Strumenti** selezionare **Gestione asset**. Nella finestra di dialogo **Gestione asset** selezionare **Download** quindi filtrare in base ad **Archiviazione di Azure**. Verranno visualizzate le risorse per Archiviazione di Azure, come mostrato nell'immagine seguente.
4. Fare clic su **Sincronizza tutti gli elementi visualizzati** per installare le risorse per Archiviazione di Azure. Le risorse disponibili includono:
	- **Regole dei colori di Archiviazione di Azure:** le regole dei colori di Archiviazione di Azure consentono di definire filtri speciali che usano il colore, il testo e il tipo di carattere per evidenziare i messaggi contenenti specifiche informazioni in una traccia.
	- **Grafici di Archiviazione di Azure:** i grafici di Archiviazione di Azure sono grafici predefiniti in cui vengono riportati i dati di log del server. Si noti che, per utilizzare grafici di Archiviazione di Azure in questa fase, è possibile unicamente caricare il log del server nella griglia di analisi.
	- **Parser di Archiviazione di Azure:** i parser di Archiviazione di Azure analizzano i log HTTP, client e server di Archiviazione di Azure per visualizzarli nella griglia di analisi.
	- **Filtri di Archiviazione di Azure:** i filtri di Archiviazione di Azure sono criteri predefiniti che è possibile usare per eseguire query sui dati nella griglia di analisi.
	- **Layout di Archiviazione di Azure:** i layout di visualizzazione di Archiviazione di Azure sono layout di colonna e raggruppamenti predefiniti nella griglia di analisi.
4. Riavviare Message Analyzer dopo aver installato le risorse.

![Gestione asset di Message Analyzer](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Installare tutte le risorse di Archiviazione di Azure per questa esercitazione.

### Importare i file di log in Message Analyzer

È possibile importare tutti i file di log salvati (lato server, lato client e rete) in un'unica sessione di Microsoft Message Analyzer per l'analisi.

1. Scegliere **Nuova sessione** dal menu **File** in Microsoft Message Analyzer, quindi fare clic su **Sessione vuota**. Nella finestra di dialogo **Nuova sessione** immettere un nome per la sessione di analisi. Nel pannello **Dettagli sessione**, fare clic sul pulsante **File**.
1. Per caricare i dati della traccia di rete generati da Message Analyzer, fare clic su **Aggiungi file**, passare al percorso in cui è stato salvato il file MATP della sessione di traccia Web, selezionare il file MATP e fare clic su **Apri**.
1. Per caricare i dati di log lato server, fare clic su **Aggiungi file**, passare al percorso in cui sono stati scaricati i log lato server, selezionare i file di log relativi all'intervallo di tempo che si vuole analizzare e fare clic su **Apri**. Quindi, nel pannello **Dettagli sessione**, selezionare **AzureStorageLog** nell'elenco a discesa **Configurazione log di testo** per ogni file di log lato server, per assicurarsi che Microsoft Message Analyzer possa analizzare correttamente il file di log.
1. Per caricare i dati di log lato client, fare clic su **Aggiungi file**, passare al percorso in cui sono stati salvati i log lato client, selezionare i file di log da analizzare e fare clic su **Apri**. Quindi, nel pannello **Dettagli sessione** selezionare **AzureStorageClientDotNetV4** nell'elenco a discesa **Configurazione log di testo** per ogni file di log lato client per assicurarsi che Microsoft Message Analyzer possa analizzare correttamente il file di log.
1. Fare clic su **Avvia** nella finestra di dialogo **Nuova sessione** per caricare e analizzare i dati di log. I dati di log vengono visualizzati nella griglia di analisi di Message Analyzer.

La figura seguente mostra una sessione di esempio configurata con i file di log di traccia di rete, client e server.

![Configurare la sessione di Message Analyzer](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Si noti che Message Analyzer carica i file di log in memoria. Se si dispone di un ampio set di dati di log, è consigliabile filtrarlo per ottenere prestazioni ottimali da Message Analyzer.

Determinare innanzitutto l'intervallo di tempo da esaminare e ridurlo al minimo. In molti casi è sufficiente esaminare al massimo un periodo di qualche minuto o ora. Importare il set di log più piccolo possibile in grado di soddisfare le proprie esigenze.

Se la quantità di dati di log è comunque considerevole, è possibile specificare un filtro di sessione per filtrare i dati prima di caricarli. Nel riquadro **Filtro sessione** fare clic sul pulsante **Libreria** e selezionare un filtro predefinito. Ad esempio, scegliere **Global Time Filter I** nei filtri di Archiviazione di Azure per filtrare in base a un intervallo di tempo. È quindi possibile modificare i criteri di filtro per specificare il timestamp di inizio e fine per l'intervallo che si vuole visualizzare. È anche possibile filtrare in base a un codice di stato specifico, ad esempio caricando solo le voci di log il cui codice di stato è 404.

Per altre informazioni sull'importazione dei dati di log in Microsoft Message Analyzer, vedere [l'argomento relativo al recupero dei dati dei messaggi su](http://technet.microsoft.com/library/dn772437.aspx) TechNet.

### Usare l'ID richiesta client per correlare i dati dei file di log

La libreria client di archiviazione di Azure genera automaticamente un ID richiesta client univoco per ogni richiesta. Questo valore viene scritto nel log del client, nel log del server e nella traccia di rete e può quindi essere usato per correlare i dati nei tre log all'interno di Message Analyzer. Per altre informazioni sull'ID richiesta client, vedere [ID richiesta client](storage-monitoring-diagnosing-troubleshooting.md#client-request-id).

Nelle sezioni seguenti viene descritto come usare i layout preconfigurati e personalizzati per correlare e raggruppare i dati in base all'ID richiesta client.

### Selezionare un layout per la visualizzazione nella griglia di analisi

Le risorse di archiviazione per Message Analyzer includono i layout di visualizzazione di Archiviazione di Azure, viste preconfigurate che è possibile usare per presentare i dati con colonne e raggruppamenti utili per i diversi scenari. È anche possibile creare layout di visualizzazione personalizzati e salvarli per riutilizzarli in futuro.

La figura seguente mostra il menu **Visualizza layout**, disponibile selezionando **Visualizza layout** sulla barra multifunzione. I layout di visualizzazione per Archiviazione di Azure sono raggruppati nel nodo **Archiviazione di Azure** del menu. È possibile cercare `Azure Storage` nella casella di ricerca per filtrare solo i layout di visualizzazione di Archiviazione di Azure. È anche possibile fare clic sulla stella accanto a un layout di visualizzazione per impostarlo come preferito e visualizzarlo nella parte superiore del menu.

![Menu Visualizza layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Per iniziare, selezionare **Raggruppati per ClientRequestID e modulo**. In questo layout di visualizzazione sono raggruppati i dati dei tre log prima in base all'ID richiesta client, quindi in base al file di log di origine (o **Modulo** in Message Analyzer). Questa visualizzazione consente di eseguire il drill-down in un particolare ID richiesta client e visualizzare i dati dei tre file di log per tale ID richiesta client.

L'immagine seguente mostra questa visualizzazione di layout applicata ai dati di log di esempio, con un subset di colonne visualizzato. Si noterà che, per un determinato ID richiesta client, nella griglia di analisi sono visualizzati i dati tratti dal log del client, dal log del server e dalla traccia di rete.

![Layout di visualizzazione di Archiviazione di Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Dato che nei diversi file di log possono essere presenti colonne diverse, quando nella griglia di analisi vengono visualizzati i dati da più file di log, è possibile che alcune colonne non contengano dati per una particolare riga. Ad esempio, nell’immagine precedente, nelle righe relative al log del client non vengono visualizzati i dati per le colonne **Timestamp**, **TimeElapsed**, **Source** e **Destination**, in quanto queste colonne non sono presenti nel log del client, ma lo sono nella traccia di rete. Analogamente, nella colonna **Timestamp** vengono visualizzati i dati di timestamp del log del server, ma non vengono visualizzati dati per le colonne **TimeElapsed**, **Source** e **Destination**, che non fanno parte del log del server.

Oltre a usare i layout di visualizzazione di Archiviazione di Azure, è possibile definire e salvare un layout di visualizzazione personalizzato. È anche possibile selezionare altri campi per il raggruppamento dei dati e salvare questo raggruppamento all'interno del layout personalizzato.

### Applicare le regole colore alla griglia di analisi

Le risorse di archiviazione includono anche regole colore, che consentono di identificare visivamente i diversi tipi di errore nella griglia di analisi. Le regole colore predefinite sono valide per gli errori HTTP, quindi sono disponibili solo per il log del server e per la traccia di rete.

Per applicare le regole colore, selezionare **Regole colori** sulla barra multifunzione. Nel menu sono presenti le regole colore di Archiviazione di Azure. Per l'esercitazione selezionare **Errori del client (StatusCode tra 400 e 499)**, come mostrato nell'immagine seguente.

![Layout di visualizzazione di Archiviazione di Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Oltre a usare le regole colore di Archiviazione di Azure, è possibile definire e salvare regole colore personalizzate.

### Raggruppare e filtrare i dati di log per individuare gli errori nella fascia 400

A questo punto i dati di log vengono raggruppati e filtrare per trovare tutti gli errori inclusi nella fascia 400.

1. Individuare la colonna **StatusCode** nella griglia di analisi, fare clic con il pulsante destro del mouse sull'intestazione di colonna e scegliere **Raggruppa**.
2. Quindi raggruppare in base alla colonna **ClientRequestId**. I dati nella grigli di analisi verranno organizzati in base al codice di stato e all'ID richiesta client.
1. Visualizzare la finestra degli strumenti View Filter se non è visualizzata. Sulla barra multifunzione selezionare **Finestre degli strumenti**, quindi **Filtro visualizzazione**.
2. Per filtrare i dati di log in modo da visualizzare solo gli errori della fascia 400, aggiungere i seguenti criteri di filtro nella finestra **Filtro visualizzazione** e fare clic su **Applica**:

		(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

L'immagine seguente mostra i risultati di questo raggruppamento e filtro. Se si espande il campo **ClientRequestID** sotto il raggruppamento per codice di stato 409, ad esempio, viene visualizzata un'operazione che ha generato questo codice di stato.

![Layout di visualizzazione di Archiviazione di Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Dopo aver applicato il filtro, si noterà che vengono escluse le righe del log del client, dato che in questo log non è presente la colonna **StatusCode**. Si inizierà esaminando i log di traccia di rete e del server per individuare gli errori 404, quindi si tornerà al log del client per esaminare le attività client da cui hanno avuto origine.

>[AZURE.NOTE] È possibile filtrare in base alla colonna **StatusCode** e visualizzare comunque i dati di tutti e tre i log, compreso il log del client, se si aggiunge un'espressione di filtro che include le voci di log in cui il codice di stato è null. Per costruire questa espressione di filtro, usare:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Questo filtro restituisce tutte le righe del log del client e solo righe del log del server e del log HTTP in cui il codice di stato è maggiore di 400. Se viene applicato al layout di visualizzazione raggruppato per ID richiesta client e modulo, è possibile cercare o scorrere le voci di log per trovare quelle in cui sono rappresentati tutte e tre i log.

### Filtrare i dati di log per trovare gli errori 404

Le risorse di archiviazione includono filtri predefiniti che possono essere usati per limitare i dati di log per trovare gli errori o le tendenze che si stanno cercando. A questo punto verranno applicati due filtri predefiniti: uno che filtra il log del server e il log della traccia di rete per trovare gli errori 404 e uno che filtra i dati in un determinato intervallo di tempo.

1. Visualizzare la finestra degli strumenti View Filter se non è visualizzata. Sulla barra multifunzione selezionare **Finestre degli strumenti**, quindi **Filtro visualizzazione**.
2. Nella finestra Filtro visualizzazione selezionare **Libreria** e cercare in `Azure Storage` per trovare i filtri di Archiviazione di Azure. Selezionare il filtro **Messaggi 404 (non trovato) in tutti i log**.
3. Visualizzare nuovamente il menu **Libreria**, quindi individuare e selezionare **Filtro tempo di esecuzione globale**.
4. Modificare i timestamp presenti nel filtro impostando l'intervallo che si vuole visualizzare. In questo modo si limiterà l'intervallo di date da analizzare.
5. Il filtro dovrebbe risultare analogo a quello riportato nell'esempio seguente. Fare clic su **Applica** per applicare il filtro alla griglia di analisi.

		((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
		(#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Layout di visualizzazione di Archiviazione di Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### Analizzare i dati di log

Con i dati raggruppati e filtrati, è possibile esaminare i dettagli delle singole richieste che hanno generato errori 404. Nel layout di visualizzazione corrente i dati sono raggruppati per ID richiesta client, quindi in base all'origine del log. Poiché vengono filtrate le richieste il cui campo StatusCode contiene 404, saranno visualizzati solo i dati del server e della traccia di rete, non i dati del log del client.

L'immagine seguente mostra una richiesta specifica in cui un'operazione Get Blob ha restituito un errore 404 perché il BLOB era inesistente. Tenere presente che alcune colonne sono state rimosse dalla visualizzazione standard per mostrare i dati pertinenti.

![Log della traccia di rete e del server filtrati](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Successivamente, questo ID richiesta client verrà correlato con i dati del log del client per mostrare le azioni che il client stava effettuando quando si è verificato l'errore. È possibile ottenere una nuova visualizzazione della griglia di analisi per la sessione corrente per visualizzare i dati del log del client, che viene aperto in una seconda scheda:

1. Copiare innanzitutto il valore del campo **ClientRequestId** negli Appunti. A questo scopo, selezionare una delle due righe, trovare il campo **ClientRequestId**, fare clic con il pulsante destro del mouse sul valore dei dati e quindi scegliere **Copia 'ClientRequestId**.
1. Sulla barra multifunzione selezionare **Nuovo visualizzatore**, quindi selezionare **Griglia analisi** per aprire una nuova scheda. Nella nuova scheda sono visualizzati tutti i dati presenti dei file di log, senza raggruppamenti, filtri o regole colore.
2. Sulla barra multifunzione selezionare **Visualizza layout**, quindi selezionare **Tutte le colonne del client .NET** nella sezione **Archiviazione di Azure **. In questo layout di visualizzazione sono presenti dati tratti dal log del client, nonché dal log del server e dal log della traccia di rete. Per impostazione predefinita, è ordinato in base alla colonna **MessageNumber**.
3. Cercare quindi l'ID richiesta client nel log del client. Sulla barra multifunzione selezionare **Trova messaggi**, quindi specificare un filtro personalizzato in base all'ID richiesta client nel campo **Trova**. Usare questa sintassi per il filtro, specificando il proprio ID richiesta client:

		*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Message Analyzer individua e seleziona la prima voce del log in cui i criterio di ricerca corrispondono all'ID richiesta client. Nel log del client sono presenti varie voci per ogni ID richiesta client, pertanto è consigliabile raggrupparle nel campo **ClientRequestId** per visualizzarle facilmente tutte insieme. L'immagine seguente mostra tutti i messaggi nel log del client per l'ID richiesta client specificato.

![Log del client con errori 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Mediante i dati presenti nei layout di visualizzazione in queste due schede è possibile analizzare i dati della richiesta per determinare le possibili cause dell'errore. È anche possibile esaminare le richieste precedenti per stabilire se l'errore 404 può essere riconducibile a un evento precedente. Ad esempio, è possibile esaminare le voci del log del client precedenti rispetto a questo ID richiesta client per determinare se il BLOB è stato eliminato o se l'errore è dovuto a un'applicazione client che chiama un'API CreateIfNotExists in un contenitore o BLOB. Nel log del client è possibile trovare l'indirizzo del BLOB nel campo **Descrizione**, mentre nel log del server e nel log della traccia di rete queste informazioni sono riportate nel campo **Riepilogo**.

Quando si conosce l'indirizzo del BLOB che ha restituito l'errore 404, è possibile effettuare ulteriori approfondimenti. Se esegue una ricerca nelle voci di log per individuare altri messaggi associati alle operazioni nello stesso BLOB, è possibile verificare se l'entità è stata eliminata in precedenza dal client.

## Analizzare altri tipi di errori di archiviazione

Dopo avere acquisito familiarità con l'uso di Message Analyzer per analizzare i dati di log, è possibile analizzare altri tipi di errori usando i layout di visualizzazione, le regole colore e le funzionalità di ricerca/filtro. Nelle tabelle seguenti sono elencati alcuni problemi che possono verificarsi e i criteri di filtro che è possibile usare per individuarli. Per altre informazioni sulla creazione di filtri e sul linguaggio di filtro di Message Analyzer, vedere [l'argomento relativo al filtraggio dei dati dei messaggi](http://technet.microsoft.com/library/jj819365.aspx).

| Per esaminare... | Usare l'espressione di filtro... | Log a cui è applicabile l'espressione (client, server, rete, tutti) |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| Ritardi imprevisti nel recapito dei messaggi in una coda | AzureStorageClientDotNetV4.Description contains "Retrying failed operation." | Client |
| Aumento di PercentThrottlingError HTTP | HTTP.Response.StatusCode == 500 &#124;&#124; HTTP.Response.StatusCode == 503 | Rete |
| Aumento di PercentTimeoutError | HTTP.Response.StatusCode == 500 | Rete |
| Aumento di PercentTimeoutError (tutti) |    **StatusCode == 500 | All |
| Increase in PercentNetworkError | AzureStorageClientDotNetV4.EventLogEntry.Level < 2 | Client |
| HTTP 403 (Forbidden) messages | HTTP.Response.StatusCode == 403 | Network |
| HTTP 404 (Not found) messages | HTTP.Response.StatusCode == 404 | Network |
| 404 (all) | *StatusCode == 404 | All |
| Shared Access Signature (SAS) authorization issue | AzureStorageLog.RequestStatus == "SASAuthorizationError" | Network |
| HTTP 409 (Conflict) messages | HTTP.Response.StatusCode == 409 | Network |
| 409 (all) | *StatusCode == 409 | All |
| Low PercentSuccess or analytics log entries have operations with transaction status of ClientOtherErrors | AzureStorageLog.RequestStatus == "ClientOtherError" | Server |
| Nagle Warning | ((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) and (AzureStorageLog.RequestPacketSize <1460) and (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS >= 200) | Server |
| Range of time in Server and Network logs | #Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 | Server, Network |
| Range of time in Server logs | AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 and AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 | Server |


## Passaggi successivi

Per altre informazioni sugli scenari end-to-end di risoluzione dei problemi di archiviazione di Azure, vedere le risorse seguenti:

- [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Analisi dell'archiviazione](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Monitorare un account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)
- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
- [Guida operativa di Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx)

<!---HONumber=AcomDC_0511_2016-->