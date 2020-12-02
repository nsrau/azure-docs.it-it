---
title: Monitorare il Glossario di anteprima di desktop virtuale Windows-Azure
description: Un glossario di termini e concetti correlati a monitoraggio di Azure per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f13909d3835bdbd2931277a88244abfae3f80759
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467732"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Glossario di monitoraggio di Azure per desktop virtuale Windows (anteprima)

>[!IMPORTANT]
>Monitoraggio di Azure per desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo elenca e descrive brevemente i termini e i concetti chiave relativi a monitoraggio di Azure per desktop virtuale Windows (anteprima).

## <a name="alerts"></a>Avvisi

Qualsiasi avviso di monitoraggio di Azure attivo configurato nella sottoscrizione e classificato come [gravità 1](#severity-1-alerts) verrà visualizzato nella pagina panoramica. Per informazioni su come configurare gli avvisi, vedere [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../azure-monitor/learn/tutorial-response.md).

## <a name="available-sessions"></a>Sessioni disponibili

Sessioni disponibili indica il numero di sessioni disponibili nel pool di host. Il servizio calcola questo numero moltiplicando il numero di macchine virtuali (VM) per il numero massimo di sessioni consentito per macchina virtuale, quindi sottraendo le sessioni totali.

## <a name="connection-success"></a>Connessione riuscita

Questo elemento Mostra l'integrità della connessione. "Connessione riuscita" indica che la connessione potrebbe raggiungere l'host, come confermato dallo stack della macchina virtuale. Una connessione non riuscita significa che la connessione non è riuscita A raggiungere l'host.

## <a name="daily-active-users-dau"></a>Utenti attivi giornalieri (DAU)

Numero totale di utenti che hanno avviato una sessione nelle ultime 24 ore.

## <a name="daily-alerts"></a>Avvisi giornalieri

Numero totale di [avvisi di gravità 1](#severity-1-alerts) attivati nelle ultime 24 ore.

## <a name="daily-connections-and-reconnections"></a>Connessioni e riconnessioni giornaliere

Numero totale di connessioni e riconnessioni avviate o completate nelle ultime 24 ore.

## <a name="daily-connected-hours"></a>Ore di connessione giornaliere

Il numero totale di ore di connessione a una sessione tra gli utenti nelle ultime 24 ore.

## <a name="diagnostics-and-errors"></a>Diagnostica ed errori

Quando viene visualizzato un errore o un avviso in monitoraggio di Azure per desktop virtuale Windows, questo viene categorizzato in base a tre elementi:

- Tipo di attività: questa categoria indica il modo in cui l'errore viene categorizzato da diagnostica desktop virtuali di Windows. Le categorie sono attività di gestione, feed, connessioni, registrazioni host, errori e Checkpoint. Per altre informazioni su queste categorie, vedere [usare log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

- Kind: questa categoria Mostra la posizione dell'errore. 

     - Errori contrassegnati come "Service" o "ServiceError = TRUE" nel servizio desktop virtuale di Windows.
     - Gli errori contrassegnati come "Deployment" o con tag "ServiceError = FALSE" si sono verificati all'esterno del servizio desktop virtuale di Windows.
     - Per altre informazioni sul tag ServiceError, vedere [scenari di errore comuni](diagnostics-role-service.md#common-error-scenarios).

- Origine: questa categoria fornisce una descrizione più specifica del punto in cui si è verificato l'errore.

     - Diagnostica: ruolo del servizio responsabile del monitoraggio e dell'attività di Reporting Services per consentire agli utenti di osservare e diagnosticare i problemi di distribuzione.

     - RDBroker: ruolo del servizio responsabile dell'orchestrazione delle attività di distribuzione, gestione dello stato degli oggetti, convalida dell'autenticazione e altro ancora.

     - RDGateway: ruolo del servizio responsabile della gestione della connettività di rete tra gli utenti finali e le macchine virtuali.

     - RDStack: componente software installato nelle macchine virtuali per consentire la comunicazione con il servizio desktop virtuale di Windows.

     - Client: il software in esecuzione nel computer dell'utente finale che fornisce l'interfaccia al servizio desktop virtuale di Windows. Visualizza l'elenco delle risorse pubblicate e ospita la connessione Desktop remoto dopo aver effettuato una selezione.

Ogni problema di diagnostica o errore include un messaggio che spiega cosa è successo. Per ulteriori informazioni sulla risoluzione degli errori, vedere [identificare e diagnosticare i problemi relativi al desktop virtuale di Windows](diagnostics-role-service.md).

## <a name="input-delay"></a>Ritardo di input

"Ritardo di input" in monitoraggio di Azure per desktop virtuale Windows indica il contatore delle prestazioni di ritardo input per processo per ogni sessione. Nella pagina prestazioni host in <> aka.ms/azmonwvdi, questo contatore delle prestazioni è configurato per l'invio di un report al servizio una volta ogni 30 secondi. Questi intervalli di 30 secondi sono denominati "Samples" e segnalano il caso peggiore in tale finestra. I valori mediano e P95 riflettono la mediana e il 95 ° percentile in tutti gli esempi.

In **input Delay by host** è possibile selezionare una riga Host sessione per filtrare tutti gli altri oggetti visivi nella pagina in tale host. È anche possibile selezionare un nome di processo per filtrare il ritardo di input medio nel grafico del tempo.

Si inseriscono ritardi nelle categorie seguenti:

- Valido: inferiore a 150 millisecondi.
- Accettabile: 150-500 millisecondi.
- Scarso: 500-2000 millisecondi (inferiori a 2 secondi).
- Non valido: oltre 2.000 millisecondi (2 secondi e superiore).

Per altre informazioni sul funzionamento del contatore dei ritardi di input, vedere [contatori delle prestazioni di ritardo input utente](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Utenti attivi mensili

Il numero totale di utenti che hanno avviato una sessione negli ultimi 28 giorni. Se si archiviano i dati per 30 giorni o meno, è possibile visualizzare i valori di MAU e di connessione inferiori a quelli previsti durante i periodi in cui sono disponibili meno di 28 giorni di dati.

## <a name="performance-counters"></a>Contatori delle prestazioni

I contatori delle prestazioni mostrano le prestazioni di componenti hardware, sistemi operativi e applicazioni.

La tabella seguente elenca i contatori delle prestazioni e gli intervalli di tempo consigliati usati da monitoraggio di Azure per desktop virtuale Windows:

|Nome del contatore delle prestazioni.|Intervallo di tempo|
|---|---|
|Disco logico (C:) \\ lunghezza media coda del disco|30 secondi|
|Disco logico (C:) \\ media sec/trasferimento disco|60 secondi|
|Disco logico (C:) \\ lunghezza corrente coda del disco|30 secondi|
|Memoria ( \* ) \\ MByte disponibili|30 secondi|
|Memoria ( \* ) \\ errori di pagina/sec|30 secondi|
|Memoria ( \* ) \\ pagine/sec|30 secondi|
|Memoria ( \* ) \\ % byte vincolati in uso|30 secondi|
|PhysicalDisk ( \* ) \\ lunghezza media coda del disco|30 secondi|
|PhysicalDisk ( \* ) \\ Media letture disco/sec|30 secondi|
|PhysicalDisk ( \* ) \\ media sec/trasferimento disco|30 secondi|
|PhysicalDisk ( \* ) \\ Media scritture disco/sec|30 secondi|
|Elabora ( \* ) \\ % tempo processore|20 secondi|
|Elabora ( \* ) \\ % tempo utente|30 secondi|
|Process ( \* ) \\ conteggio thread|30 secondi|
|Operazioni di \* scrittura i/o processo () \\ /sec|30 secondi|
|Operazioni di \* lettura i/o processo () \\ /sec|30 secondi|
|Informazioni processore (_Total) \\ % tempo processore|30 secondi|
|Sessioni attive di Servizi terminal ( \* ) \\|60 secondi|
|\*Sessioni inattive di Servizi terminal () \\|60 secondi|
|Totale sessioni Servizi terminal ( \* ) \\|60 secondi|
|\*Ritardo input utente per processo ( \* ) \\ numero massimo input dela|30 secondi|
|\*Ritardo input utente per sessione ( \* )- \\ ritardo input massimo|30 secondi|
|\*RTT TCP corrente rete () RemoteFX \\|30 secondi|
|RemoteFX rete ( \* ) \\ larghezza di banda UDP corrente|30 secondi|

Per altre informazioni su come leggere i contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/platform/data-sources-performance-counters.md).

Per ulteriori informazioni sui contatori delle prestazioni di ritardo di input, vedere [contatori delle prestazioni di ritardo input utente](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Problemi di connettività potenziali

Potenziali problemi di connettività mostrano gli host, gli utenti, le risorse pubblicate e i client con una percentuale di errori di connessione elevata. Una volta scelto un filtro "report per", è possibile valutare la gravità del problema controllando i valori nelle colonne seguenti:

- Tentativi (numero di tentativi di connessione)
- Risorse (numero di app pubblicate o desktop)
- Host (numero di macchine virtuali)
- Client

Se ad esempio si seleziona il filtro per **utente** , è possibile verificare che i tentativi di connessione di ogni utente siano visualizzati nella colonna **tentativi** .

Se si nota che un problema di connessione si estende su più host, utenti, risorse o client, è probabile che il problema influisca sull'intero sistema. In caso contrario, si tratta di un problema più piccolo che ha una priorità più bassa.

È anche possibile selezionare le voci per visualizzare informazioni aggiuntive. È possibile visualizzare quali host, risorse e versioni client sono stati interessati al problema. Nella visualizzazione vengono visualizzati anche tutti gli errori segnalati durante i tentativi di connessione.

## <a name="round-trip-time-rtt"></a>Tempo di round trip (RTT)

Il tempo di round trip (RTT) è una stima del tempo di round trip della connessione tra la posizione dell'utente finale e l'area di Azure della macchina virtuale. Per vedere quali percorsi hanno la massima latenza, cercare la posizione desiderata nello [strumento di stima dell'esperienza desktop virtuale di Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Cronologia delle sessioni

L'elemento **Sessions** Mostra lo stato di tutte le sessioni connesse e disconnesse. Le **sessioni inattive** mostrano solo le sessioni disconnesse.

## <a name="severity-1-alerts"></a>Avvisi gravità 1

Gli elementi più urgenti che è necessario gestire immediatamente. Se non si affrontano questi problemi, è possibile che la distribuzione del desktop virtuale di Windows smetta di funzionare.

## <a name="time-to-connect"></a>Tempo richiesto per la connessione

Il tempo necessario per la connessione è il tempo che intercorre tra il momento in cui un utente avvia la sessione e quando viene conteggiato come connesso al servizio. La creazione di nuove connessioni tende a richiedere più tempo rispetto alla ridefinizione delle connessioni esistenti.

## <a name="user-report"></a>Report utente

Nella pagina report utente è possibile visualizzare la cronologia delle connessioni e le informazioni di diagnostica di un utente specifico. Ogni report utente Mostra i modelli di utilizzo, il feedback degli utenti e gli eventuali errori riscontrati dagli utenti durante le sessioni. È possibile risolvere i problemi più piccoli con i commenti degli utenti. Se è necessario eseguire un'analisi più approfondita, è anche possibile filtrare le informazioni su un ID connessione specifico o un periodo di tempo.

## <a name="users-per-core"></a>Utenti per core

Questo è il numero di utenti in ogni core della macchina virtuale. Il monitoraggio del numero massimo di utenti per core nel tempo può aiutare a identificare se l'ambiente viene eseguito in modo coerente a un numero elevato, minimo o fluttuante di utenti per core. Conoscere il numero di utenti attivi consentirà di eseguire in modo efficiente le risorse e la scalabilità dell'ambiente.

## <a name="windows-events"></a>Eventi Windows

I registri eventi di Windows sono origini dati raccolte da agenti Log Analytics in macchine virtuali Windows. È possibile raccogliere eventi da log standard come il sistema e l'applicazione, nonché i log personalizzati creati dalle applicazioni che è necessario monitorare.

La tabella seguente elenca gli eventi di Windows necessari per il desktop virtuale Windows per il monitoraggio di Azure:

|Nome evento|Tipo di evento|
|---|---|
|Applicazione|Errore e avviso|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Errore, avviso e informazioni|
|Microsoft-Windows-TerminalServices-LocalSessionManager/operativo|Errore, avviso e informazioni|
|Sistema|Errore e avviso|
| Microsoft-FSLogix-Apps/operativo|Errore, avviso e informazioni|
|Microsoft-FSLogix-app/amministratore|Errore, avviso e informazioni|

Per ulteriori informazioni sugli eventi di Windows, vedere [proprietà di record eventi di Windows](../azure-monitor/platform/data-sources-windows-events.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare monitoraggio di Azure per desktop virtuale di Windows, vedere questi articoli:

- [Usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md)
- [Monitoraggio di Azure per la risoluzione dei problemi relativi al desktop virtuale Windows](troubleshoot-azure-monitor.md)

È anche possibile configurare Azure Advisor per capire come risolvere o impedire problemi comuni. Per altre informazioni, vedere [usare Azure Advisor con desktop virtuale di Windows](azure-advisor.md).

Per assistenza o domande, consultare le risorse della community:

- È possibile porre domande o inviare suggerimenti alla community sul [desktop virtuale Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Per informazioni su come lasciare commenti, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto per desktop virtuale di Windows](troubleshoot-set-up-overview.md#report-issues).

- È anche possibile lasciare commenti e suggerimenti per desktop virtuale Windows nell' [Hub di feedback per desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) o nel [Forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).
