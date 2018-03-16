---
title: "Funzionalità del sistema operativo in Servizio app di Azure"
description: "Informazioni sulle funzionalità del sistema operativo disponibili per app Web, back-end per app per dispositivi mobili e app per le API in Servizio app di Azure"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: 6b5939341ad05fb8f80415c5335c24d216fc2555
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funzionalità del sistema operativo in Servizio app di Azure
Questo articolo descrive le funzionalità di base del sistema operativo disponibili in tutte le app in esecuzione in [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Queste funzionalità includono l'accesso a file, rete e registro, nonché log ed eventi di diagnostica. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Livelli del piano del servizio app di Azure
Il servizio app esegue app di clienti in un ambiente host multi-tenant. Le app distribuite nei livelli **Gratuito** e **Condiviso** vengono eseguite in processi di lavoro su macchine virtuali condivise, mentre le app distribuite nei livelli **Standard** e **Premium** vengono eseguite su macchine virtuali dedicate in modo specifico per le app associate a un singolo cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Poiché il servizio app supporta una perfetta scalabilità tra i diversi livelli, la configurazione della sicurezza applicata per le app del servizio app è la stessa. Questo garantisce un comportamento sempre coerente delle app, senza eventi imprevisti, quando il piano del servizio app passa da un livello all'altro.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Framework di sviluppo
I piani tariffari del servizio app controllano la quantità di risorse di calcolo (CPU, archiviazione su disco, memoria e uscita di rete) disponibili per le app. Tuttavia, la gamma di funzionalità del framework disponibili per le app resta la stessa indipendentemente dai livelli di scalabilità.

Il servizio app supporta diversi framework di sviluppo, tra cui ASP.NET, ASP classico, Node.js, PHP e Python, tutti eseguibili come estensioni in IIS. Per semplificare e normalizzare la configurazione della sicurezza, le app del servizio app eseguono i vari framework di sviluppo con le relative impostazioni predefinite. Un approccio alla configurazione delle app avrebbe potuto comportare la personalizzazione delle funzionalità e della superficie di attacco delle API per ogni framework di sviluppo. Il servizio app invece usa un approccio più generico, abilitando una linea di base comune delle funzionalità del sistema operativo indipendentemente dal framework di sviluppo di un'app.

Le sezioni seguenti riepilogano i tipi generali di funzionalità del sistema operativo disponibili per le app del servizio app.

<a id="FileAccess"></a>

## <a name="file-access"></a>Accesso ai file
Nel servizio app sono presenti varie unità, tra cui unità locali e unità di rete.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unità locali
Il servizio app è essenzialmente un servizio in esecuzione sull'infrastruttura PaaS (piattaforma distribuita come servizio) di Azure. Di conseguenza, le unità locali collegate a una macchina virtuale sono dello stesso tipo di quelle disponibili in qualsiasi ruolo di lavoro in esecuzione in Azure. Sono incluse un'unità del sistema operativo (l'unità D:\), un'unità delle applicazioni contenente i file pacchetto di Azure (cspkg) usati esclusivamente dal servizio app (e non accessibili ai clienti) e un'unità "utente" (l'unità C:\), la cui dimensione varia in base alla dimensione della VM. È importante monitorare l'utilizzo del disco man mano che aumentano le dimensioni dell'applicazione. Il raggiungimento della quota disco può avere effetti negativi sull'applicazione.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unità di rete (note anche come condivisioni UNC)
Uno degli aspetti esclusivi del servizio app che rende immediata la distribuzione e la manutenzione delle app è rappresentato dal fatto che l'intero contenuto utente è archiviato in un set di condivisioni UNC. Questo modello corrisponde perfettamente al modello comune di archiviazione di contenuto usato dagli ambienti host Web locali che dispongono di più server con carico bilanciato. 

Nel servizio app sono presenti numerose condivisioni UNC create in ogni data center. A ciascuna condivisione UNC è allocata una percentuale del contenuto utente per tutti i clienti di ciascun data center. Inoltre, l'intero contenuto dei file per una sottoscrizione di un singolo cliente è sempre posizionato nella stessa condivisione UNC. 

Tenere presente che per effetto della modalità di funzionamento dei servizi cloud, la macchina virtuale specifica che è responsabile dell'hosting di una condivisione UNC cambierà nel corso del tempo. Sicuramente le condivisioni UNC verranno montate da macchine virtuali diverse in quanto vengono avviate e arrestate durante la normale esecuzione delle operazioni cloud. Per questo motivo le app non devono mai essere hardcoded in base al presupposto che le informazioni sul computer in un percorso file UNC rimarranno stabili nel corso del tempo. Devono invece usare il pratico percorso assoluto *faux* **D:\home\site** offerto dal servizio app, che offre un metodo portatile, indipendente dall'app e dall'utente per fare riferimento alla propria app. Usando **D:\home\site** è possibile trasferire file condivisi da un'app all'altra senza dover configurare un nuovo percorso assoluto per ogni trasferimento.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipi di accesso ai file concessi a un'app
La sottoscrizione di ciascun cliente presenta una struttura di directory riservata su una condivisione UNC specifica all'interno di un data center. Un cliente può disporre di più app create all'interno di un data center specifico, pertanto tutte le directory che appartengono alla sottoscrizione di un singolo cliente vengono create nella stessa condivisione UNC. La condivisione può includere directory come quelle relative a log di contenuto, di errori e di diagnostica e versioni precedenti dell'app create dal controllo del codice sorgente. Come previsto, le directory dell'app di un cliente sono disponibili per l'accesso in lettura e in scrittura in fase di esecuzione dal codice dell'applicazione di un'app.

Il servizio app riserva una porzione di spazio dell'unità C:\ per l'archiviazione locale temporanea specifica delle app nelle unità locali collegate alla macchina virtuale in cui è in esecuzione un'app. Sebbene un'app disponga di accesso completo in lettura/scrittura alla propria risorsa di archiviazione locale temporanea, tale risorsa non è in realtà destinata all'uso diretto da parte del codice dell'applicazione. L'obiettivo è invece quello di fornire una risorsa di archiviazione di file temporanea per IIS e per i framework applicazione Web. Il servizio app limita anche la quantità di risorse di archiviazione locale temporanea disponibili per ogni app, allo scopo di impedire un utilizzo eccessivo di tali risorse da parte delle singole app.

Due esempi del modo in cui il servizio app usa le risorse di archiviazione locale temporanea sono costituiti dalla directory per i file ASP.NET temporanei e dalla directory per i file IIS compressi. Il sistema di compilazione di ASP.NET usa la directory "Temporary ASP.NET Files" come posizione cache temporanea per la compilazione. IIS usa la directory "IIS Temporary Compressed Files" per archiviare l'output della risposta in formato compresso. Entrambi i tipi di utilizzo di file (nonché altri) sono rimappati nel servizio app a una risorsa di archiviazione locale temporanea per singola app. Il remapping garantisce una funzionalità conforme alle aspettative.

Ogni app nel servizio app viene eseguita come identità esclusiva e casuale del processo di lavoro con privilegi limitati, definita come "identità del pool di applicazioni" e descritta più dettagliatamente all'indirizzo seguente: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Il codice dell'applicazione usa questa identità per l'accesso di base in sola lettura all'unità del sistema operativo (l'unità D:\). Questo significa che il codice dell'applicazione può elencare strutture di directory comuni e file comuni in lettura nell'unità del sistema operativo. Sebbene questo livello di accesso possa sembrare piuttosto esteso, è possibile accedere alle stesse directory e agli stessi file quando si esegue il provisioning di un ruolo di lavoro in un servizio ospitato Azure e la lettura dei contenuti dell'unità. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Accesso ai file tra più istanze
La home directory include il contenuto di un'app e il codice dell'applicazione può accedervi per la scrittura. Se un'app esegue più istanze, la home directory viene condivisa tra tutte le istanze e quindi tutte le istanze visualizzano la stessa directory. Se quindi un'app salva ad esempio i file caricati nella home directory, tali file sono immediatamente disponibili per tutte le istanze. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Accesso alla rete
Il codice dell'applicazione può usare i protocolli basati su TCP/IP e UDP per effettuare connessioni di rete in uscita a endpoint accessibili tramite Internet che rendono visibili i servizi esterni. Le app possono usare gli stessi protocolli per connettersi ai servizi di Azure&#151; ad esempio stabilendo connessioni HTTPS al database SQL.

È anche presente una capacità limitata per le app di stabilire una connessione loopback locale e di disporre di un'app in ascolto su tale socket loopback locale. Questa funzionalità esiste principalmente per abilitare le app che includono tra le proprie funzionalità l'ascolto su socket di loopback locali. Si noti che ogni app visualizza una connessione di loopback "privata". L'app "A" non può essere in ascolto su un socket di loopback locale stabilito dall'app "B".

Sono supportate anche le named pipe come meccanismo di comunicazione interprocesso (IPC) tra processi diversi che eseguono collettivamente un'app. Ad esempio, il modulo IIS FastCGI si basa su named pipe per coordinare i singoli processi che eseguono le pagine PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Esecuzione del codice, processi e memoria
Come indicato in precedenza, le app vengono eseguite all'interno di processi di lavoro con privilegi limitati che usano un'identità del pool di applicazioni casuale. Il codice dell'applicazione ha accesso allo spazio di memoria associato al processo di lavoro, nonché a eventuali processi figlio che possono essere generati da processi CGI o da altre applicazioni. Tuttavia, un'app non può accedere alla memoria o ai dati di un'altra app anche se si trova nella stessa macchina virtuale.

Le app possono eseguire script o pagine scritte con framework di sviluppo Web supportati. Il servizio app non configura le impostazioni dei framework Web su modalità con maggiori restrizioni. Ad esempio, le app ASP.NET in esecuzione nel servizio app vengono eseguite in una modalità di attendibilità "completa" e non in una modalità di attendibilità con maggiori restrizioni. I framework Web, tra cui ASP classico e ASP.NET, possono chiamare componenti COM in-process (ma non componenti COM out-of-process) come gli oggetti ADO (ActiveX Data Objects) registrati per impostazione predefinita nel sistema operativo Windows.

Le app possono generare ed eseguire codice arbitrario. Un'app può eseguire attività come la generazione di una shell dei comandi o l'esecuzione di uno script PowerShell. Tuttavia, mentre il codice arbitrario e i processi possono essere generati da un'app, i programmi e gli script eseguibili sono ancora limitati ai privilegi concessi al pool di applicazioni padre. Ad esempio, un'app può generare un eseguibile che effettua una chiamata HTTP in uscita, ma lo stesso eseguibile non può tentare di dissociare l'indirizzo IP di una macchina virtuale dalla relativa NIC. L'esecuzione di una chiamata di rete in uscita è consentita al codice con privilegi limitati, ma il tentativo di riconfigurare le impostazioni di rete su una macchina virtuale richiede privilegi amministrativi.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Log ed eventi di diagnostica
Le informazioni dei log costituiscono un altro set di dati al quale alcune app tentano di accedere. Le informazioni dei log disponibili per il codice in esecuzione nel servizio app includono informazioni di diagnostica e dei log generate da un'app che sono anche facilmente accessibili all'app. 

Ad esempio, i log HTTP W3C generati da un'app attiva sono disponibili in una directory log nel percorso di condivisione di rete creato per l'app oppure in una risorsa di archiviazione BLOB nel caso in cui un cliente abbia impostato la registrazione W3C nella risorsa di archiviazione. Quest'ultima opzione consente di raccogliere elevate quantità di log senza il rischio di superare i limiti di archiviazione dei file associati a una condivisione di rete.

Analogamente, le informazioni di diagnostica in tempo reale delle app .NET possono essere registrate usando l'infrastruttura delle funzionalità di traccia e diagnostica di .NET, con la possibilità di scrivere le informazioni di traccia nella condivisione di rete dell'app o, in alternativa, in un percorso di archiviazione BLOB.

Le aree delle funzionalità di registrazione diagnostica e traccia che non sono disponibili per le app sono eventi Windows ETW e log eventi Windows comuni, ad esempio log eventi di sistema, di applicazioni e di sicurezza. Poiché le informazioni di traccia ETW sono potenzialmente visualizzabili a livello di intera macchina (con gli ACL appropriati), l'accesso in lettura e scrittura agli eventi ETW sono bloccati. Gli sviluppatori potrebbero notare chiamate API per la lettura e la scrittura di eventi ETW e di log eventi Windows comuni apparentemente funzionanti. Tuttavia, ciò accade perché il servizio app "simula" le chiamate in modo che risultino eseguite correttamente. In realtà, il codice dell'applicazione non è autorizzato ad accedere a questi dati di eventi.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Accesso al Registro di sistema
Le app hanno accesso in sola lettura alla maggior parte del Registro di sistema della macchina virtuale sulla quale sono in esecuzione. In pratica, questo significa che le chiavi del Registro di sistema che consentono l'accesso in sola lettura al gruppo di utenti locali sono accessibili tramite app. Un'area del Registro di sistema che non è attualmente supportata per l'accesso in lettura o scrittura è l'hive HKEY\_CURRENT\_USER.

L'accesso in scrittura al registro è bloccato, incluso l'accesso a qualsiasi chiave di registro per utente. Dal punto di vista dell'app, l'accesso in scrittura al Registro di sistema nell'ambiente Azure non deve mai essere considerato affidabile poiché le app sono soggette a migrazione tra macchine virtuali diverse. L'unica risorsa di archiviazione scrivibile permanente che può essere considerata affidabile da un'app è la struttura della directory dei contenuti per app archiviata nelle condivisioni UNC del servizio app. 

## <a name="more-information"></a>Altre informazioni

[App Web Sandbox di Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox): informazioni aggiornate sull'ambiente di esecuzione del servizio App. Questa pagina è gestita direttamente dal team di sviluppo del servizio App.

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 


