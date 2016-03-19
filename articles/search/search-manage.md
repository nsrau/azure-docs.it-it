<properties 
	pageTitle="Gestire il servizio di ricerca in Microsoft Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Gestire la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="heidist"/>

# Gestire il servizio di ricerca in Microsoft Azure
> [AZURE.SELECTOR]
- [Portale](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Ricerca di Azure è un servizio basato sul cloud con un'API basata su HTTP che è possibile usare in applicazioni di ricerca personalizzate. Il servizio di ricerca fornisce il motore per l'analisi del testo tramite ricerca full-text, funzionalità avanzate di ricerca, archiviazione di dati di ricerca e una sintassi dei comandi di query.

Questo articolo spiega come amministrare un servizio di ricerca nel [Portale di Azure](https://portal.azure.com). È anche possibile usare la nuova funzionalità di analisi del traffico di ricerca per informazioni dettagliate sulle attività a livello di indice. Per iniziare, visitare [Analisi del traffico di ricerca per Ricerca di Azure](search-traffic-analytics.md).

In alternativa, è possibile usare l'API REST di gestione. Per informazioni dettagliate, vedere [Introduzione all'API REST di gestione di Ricerca di Azure](search-get-started-management-api.md) e [API REST di gestione di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn832684.aspx).

<a id="sub-1"></a>
## Aggiungere un servizio di ricerca alla sottoscrizione

Un amministratore che configura un servizio di ricerca deve prima di tutto definire un livello di prezzo. Le opzioni disponibili includono i livelli di prezzo Gratuito e Standard.

Per gli abbonati esistenti è disponibile un servizio condiviso senza costi aggiuntivi, consigliato per scopi di apprendimento, test dei modelli di prova e piccoli progetti di sviluppo. Il servizio condiviso è limitato a 50 MB di spazio di archiviazione, tre indici e un numero massimo 10.000 documenti, anche qualora lo spazio di archiviazione usato da questi ultimi sia inferiore ai 50 MB consentiti. Non vi è garanzia alcuna rispetto alle prestazioni del servizio condiviso, pertanto, se si prevede di sviluppare un'applicazione di ricerca di produzione, è consigliabile valutare l'acquisto del servizio di ricerca standard.

Il servizio di ricerca, sia Basic che Standard, è fatturabile perché fornisce accesso a risorse dedicate e a un'infrastruttura usata unicamente dalla propria sottoscrizione. I servizi di ricerca Basic e Standard vengono allocati in bundle di partizioni definiti dall'utente (archiviazione) e repliche (carichi di lavoro del servizio). Le tariffe sono definite per unità di ricerca. È possibile aumentare partizioni o repliche indipendentemente, aggiungendo le risorse necessarie.

Per pianificare la capacità e comprendere l'impatto di fatturazione, è consigliabile consultare le informazioni disponibili seguendo i collegamenti seguenti:

+	[Limiti e vincoli](search-limits-quotas-capacity.md)
+	[Dettagli sui prezzi](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Quando si è pronti a effettuare l'iscrizione, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

##Analisi di ricerca

È possibile abilitare la raccolta dei dati nelle attività di ricerca dell'utente per comprendere il comportamento del servizio di ricerca, quali termini vengono usati e se tali termini abbiano restituito occorrenze. Il modo migliore per analizzare e visualizzare i dati è tramite un pacchetto di contenuti di Power BI. Il primo passaggio consiste nell'abilitare l'analisi del traffico di ricerca. Per informazioni su come procedere, vedere la pagina relativa all'[analisi del traffico di Ricerca di Azure](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/).

<a id="sub-2"></a>
## Attività amministrative

Anche se per alcuni servizi sono ammessi i coamministratori, per un servizio di ricerca di Azure è ammesso un solo amministratore per sottoscrizione. Per eseguire le attività descritte in questa sezione è necessario essere un amministratore. Oltre che per l'aggiunta di un servizio di ricerca alla sottoscrizione, un amministratore è responsabile per l'esecuzione delle attività seguenti:

+	Distribuzione dell'URL del servizio (definito durante il provisioning del servizio).
+	Gestione e distribuzione delle chiavi API.
+	Monitorare l'uso delle risorse
+	Aumentare o ridurre la quantità di risorse (applicabile solo al servizio di ricerca standard)
+	Avviare o arrestare il servizio
+	Impostare i ruoli per il controllo dell'accesso amministrativo

<a id="sub-3"></a>
## URL del servizio

L'URL del servizio è definito come proprietà fissa quando si crea il servizio. Non sarà possibile modificarlo in seguito.

Gli sviluppatori che devono creare applicazioni di ricerca dovranno conoscere l'URL del servizio per le richieste HTTP. È possibile individuare rapidamente l'URL del servizio tramite il Dashboard servizi.

Per ottenere l'URL del servizio dal Dashboard servizi:

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **Sfoglia** | **Tutto** | **Servizi di ricerca**.
3.	Fare clic sul nome del servizio di ricerca per aprire il dashboard.
4.	Fare clic su **PROPRIETÀ** per aprire una pagina delle proprietà con effetto scorrimento. L'URL del servizio è visualizzato nella parte superiore della pagina. È possibile bloccare questa pagina per accedervi facilmente in seguito.

    ![][8]

Gli sviluppatori potrebbero richiedere anche la versione dell'API. Un requisito per la codifica di API del servizio di ricerca di Azure è la specifica della versione dell'API nella richiesta. Tale requisito esiste per consentire agli sviluppatori di continuare a usare una versione precedente e quindi passare a una versione successiva al momento opportuno.

La versione dell'API non è visualizzata nelle pagine del portale, pertanto è un'informazione che l'utente non può fornire. Per informazioni sulle versioni API attuali e precedenti, vedere [API REST del servizio di ricerca di Azure](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<a id="sub-4"></a>
## Gestire le chiavi API

Gli sviluppatori che devono creare applicazioni di ricerca avranno bisogno di una chiave API per accedere al servizio di ricerca. Ogni richiesta HTTP indirizzata al servizio di ricerca necessiterà di una chiave API generata specificamente per il proprio servizio. Questa chiave API è l'unico meccanismo di autenticazione per l'URL del servizio di ricerca.

Per accedere al servizio di ricerca vengono usati due tipi di chiavi:

+	Amministrativa (valida per qualsiasi operazione)
+	Di query (valida solo per richieste di query)

Una chiave API amministrativa viene creata con il servizio. Vi sono una chiave primaria e una secondaria. Queste possono essere usate indifferentemente. Nessuna delle due prevede un livello di accesso maggiore o minore rispetto all'altra, il che può essere utile nel caso in cui si voglia eseguire il rollover delle chiavi. È possibile rigenerare qualsiasi delle due chiavi amministrative ma non è possibile aggiungere chiavi al totale delle chiavi amministrative disponibili. È consentito un massimo di due chiavi amministrative per ogni servizio di ricerca.

Le chiavi di query sono progettate per l'uso in applicazioni client che chiamano il servizio di ricerca direttamente. È possibile creare fino a 50 chiavi di query.

Per ottenere o rigenerare le chiavi API, aprire il Dashboard servizi. Fare clic su **CHIAVI** per aprire la pagina di gestione delle chiavi con effetto scorrimento. I comandi per rigenerare o creare le chiavi sono visualizzati nella parte superiore della pagina.

 ![][9]


<a id="sub-5"></a>
## Monitorare l'uso delle risorse

In questa anteprima pubblica, il monitoraggio delle risorse è limitato alle informazioni visualizzate nel dashboard del servizio e ad alcune metriche che è possibile ottenere effettuando query sul servizio.

Nella sezione Utilizzo del Dashboard servizi è possibile determinare rapidamente se i livelli delle risorse di partizione sono adeguati per l'applicazione.

Usando l'API del servizio di ricerca, è possibile ottenere un conteggio dei documenti e degli indici. Esistono limiti rigidi associati a questi conteggi in base al livello di prezzo. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti del servizio di ricerca](search-limits-quotas-capacity.md).

+	[Ottenere le statistiche di un indice](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Conteggio documenti](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Il comportamento della cache può determinare la dichiarazione di un limite più alto. Ad esempio, quando si usa il servizio condiviso, è possibile che venga visualizzato un numero documenti superiore al limite rigido di 10.000. Questa stima in eccesso è temporanea e verrà rilevata al successivo controllo di imposizione del limite.


<a id="sub-6"></a>
## Aumentare o ridurre la quantità di risorse

Ogni servizio di ricerca viene creato con un minimo di una replica e una partizione. Se si è effettuata l'iscrizione alle risorse dedicate usando il [piano tariffario Basic o Standard](search-limits-quotas-capacity.md), fare clic sul riquadro **RIDIMENSIONA** nel dashboard del servizio per regolare il numero di partizioni e di repliche usate dal servizio stesso.

Quando si aggiunge capacità tramite l'una o l'altra risorsa, la risorsa aggiunta viene usata dal servizio in modo automatico. Non sono necessarie ulteriori azioni da parte dell'utente, ma vi sarà un lieve ritardo prima che l'impatto delle nuove risorse sia apprezzabile. Possono essere necessari 15 o più minuti per il provisioning delle risorse aggiuntive.

 ![][10]

### Aggiungere repliche

L'aumento delle query al secondo o il raggiungimento della disponibilità elevata si ottengono mediante l'aggiunta di repliche. Ogni replica presenta una copia di un indice, pertanto l'aggiunta di un'ulteriore replica si traduce in un indice aggiuntivo che può essere usato per soddisfare le richieste di query. In linea generale, attualmente sono necessarie almeno 3 repliche per la disponibilità elevata. Per informazioni dettagliate vedere l'articolo relativo alla [pianificazione della capacità](search-capacity-planning.md).

Un servizio di ricerca con un numero superiore di repliche può eseguire il bilanciamento del carico delle richieste di query su un numero maggiore di indici. Per un determinato volume di query, la velocità effettiva delle query sarà maggiore in presenza di più copie dell'indice disponibili per soddisfare la richiesta. Se si verificano latenze delle query, ci si può aspettare un netto miglioramento delle prestazioni una volta portate online le repliche aggiuntive.

Sebbene la velocità effettiva delle query aumenti quando si aggiungono repliche, non viene effettivamente raddoppiata o triplicata in rapporto al numero di repliche aggiunte. Tutte le applicazioni di ricerca sono influenzate da fattori esterni che possono influire sulle prestazioni di query. Query complesse e latenza di rete sono due fattori che contribuiscono alle variazioni nei tempi di risposta alle query.

### Aggiungere partizioni

La maggior parte delle applicazioni di servizio mostrano una necessità insita di disporre di più repliche, più che di un numero maggiore di partizioni, poiché la maggior parte delle applicazioni che usano la ricerca può essere contenuta facilmente in una singola partizione che può supportare fino a 15 milioni di documenti.

Per i casi in cui è necessario un numero maggiore di documenti, l'iscrizione al servizio Standard permette di aggiungere partizioni. Il livello Basic non offre partizioni aggiuntive.

Al livello Standard le partizioni vengono aggiunte in multipli di 12 (ossia 1, 2, 3, 4, 6 o 12). Si tratta di un artefatto del partizionamento orizzontale: un indice viene creato in 12 sottopartizioni, tutte archiviabili a propria volta in un'unica partizione o equamente distribuibili in 2, 3, 4, 6 o 12 partizioni (in quest'ultimo caso, una per partizione).

### Rimuovere repliche

Dopo periodi di alti volumi di query è possibile che si vogliano rimuovere repliche una volta che i carichi di query di ricerca si sono normalizzati (ad esempio al termine di un periodo di vendite prefestive).

Per fare ciò è sufficiente spostare il dispositivo di scorrimento relativo alle repliche su un numero inferiore. Non vi sono altre azioni richieste da parte dell'utente. La riduzione del numero di repliche comporta il rilascio di macchine virtuali nel data center. Le operazioni di query e di inserimento dati verranno ora elaborate su un numero minore di VM rispetto a prima. Il limite minimo è di una replica.

### Rimuovere partizioni

A differenza della rimozione di repliche, che non richiede operazioni aggiuntive da parte dell'utente, potrebbe essere richiesto lavoro aggiuntivo se si occupa più archiviazione di quanta se ne vuole ottenere dopo una riduzione. Ad esempio, se la soluzione usa tre partizioni, il tentativo di ridurle a una o due partizioni genererà un errore se si sta usando più spazio di archiviazione di quanto ne è disponibile nel numero di partizioni a cui si vuole arrivare. In questo caso la scelta è di eliminare indici o documenti all'interno di un indice associato per liberare spazio oppure di mantenere la configurazione attuale.

Non è disponibile un metodo di rilevamento che indichi quante sottopartizioni di indice sono archiviate su una partizione specifica. Ogni partizione fornisce approssimativamente 25 GB di spazio di archiviazione, pertanto sarà necessario ridurre l'archiviazione a una dimensione che possa essere contenuta nel numero disponibile di partizioni. Se si vuole tornare a una partizione, questa dovrà contenere tutte e 12 le sottopartizioni.

Per pianificare in anticipo le esigenze future, è possibile verificare l'archiviazione (usando [Ottieni statistiche indice](http://msdn.microsoft.com/library/dn798942.aspx)) per verificare quanta archiviazione è stata effettivamente usata.

### Procedure consigliate per la scalabilità e la distribuzione del servizio tra più data center (video)

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="sub-7"></a>
## Avviare o arrestare il servizio

È possibile avviare, arrestare o eliminare il servizio usando i comandi disponibili nel Dashboard servizi.

 ![][11]


L'arresto o l'avvio del servizio non determinano la sospensione della fatturazione. Per evitare qualsiasi ulteriore addebito relativo al servizio, è necessario che quest'ultimo venga eliminato. Quando il servizio viene rimosso, tutti i dati ad esso associati verranno eliminati.

<a id="sub-8"></a>
## Impostare i ruoli per il controllo dell'accesso amministrativo

Azure offre un modello di autorizzazione basata sui ruoli globali per tutti i servizi gestiti tramite il portale oppure nell'API di Gestione risorse di Azure, se si usa uno strumento di amministrazione personalizzato. I ruoli Proprietario, Collaboratore e Lettore configurano il livello di amministrazione del servizio per gli utenti, i gruppo e le entità di sicurezza di Active Directory assegnati a ogni ruolo. Per informazioni dettagliate sull'appartenenza ai ruoli, vedere [Controllo di accesso in base al ruolo nel portale di Azure classico](../active-directory/role-based-access-control-configure.md).

In Ricerca di Azure i controlli degli accessi in base al ruolo determinano le attività amministrative seguenti:


Ruolo|Attività
---|---
Proprietario|Avviare, arrestare o eliminare il servizio.<p>Generare e visualizzare le chiavi di amministratore ed eseguire le chiavi di query.<p>Visualizzare lo stato di servizio, incluso il numero di indici, i nomi degli indici, il numero di documenti e le dimensioni di archiviazione.<p>Aggiungere o eliminare l'appartenenza al ruolo (solo un proprietario può gestire l'appartenenza al ruolo).<p>Gli amministratori della sottoscrizione e del servizio dispongono dell’appartenenza automatica al ruolo di proprietario.
Collaboratore|Ha lo stesso livello di accesso del Proprietario, ad eccezione della gestione dei ruoli. Ad esempio, un Collaboratore può visualizzare e rigenerare `api-key`, ma non può modificare le appartenenze ai ruoli.
Lettore|Può visualizzare lo stato e le chiavi di query. I membri di questo ruolo non possono avviare o arrestare un servizio o visualizzare le chiavi di amministrazione.

Si noti che i ruoli non concedono diritti di accesso all'endpoint del servizio. Le operazioni del servizio di ricerca, ad esempio la gestione e il popolamento degli indici e le query sui dati di ricerca, sono controllate tramite le chiavi API, non tramite i ruoli. Per altre informazioni, vedere "Autorizzazioni per le operazioni di gestione e per le operazioni di dati" in [Controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-configure.md).

I ruoli forniscono il controllo degli accessi dopo la creazione del servizio. Solo i gestori delle sottoscrizioni possono aggiungere un servizio di ricerca a una sottoscrizione.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7
[Set roles to control administrative access]: #sub-8

<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


 

<!---HONumber=AcomDC_0302_2016-->