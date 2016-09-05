<properties 
   pageTitle="Gestione traffico- Metodi di routing del traffico | Microsoft Azure"
   description="Questo articolo aiuterà a comprendere i diversi metodi di routing del traffico usati da Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="sewhee" />

# Metodi di routing del traffico di Gestione traffico

Questa pagina descrive i metodi di routing del traffico supportati da Gestione traffico di Azure. Questi metodi vengono usati per indirizzare gli utenti finali all'endpoint di servizio corretto.

> [AZURE.NOTE] L'API di Azure Resource Manager (ARM) per Gestione traffico usa una terminologia diversa dall'API di Azure Service Management (ASM). Questa modifica è stata introdotta in base ai commenti degli utenti per migliorare la chiarezza e ridurre le incomprensioni comuni. In questa pagina si userà la terminologia ARM. Le differenze sono le seguenti:

>- In ARM si usa il "metodo di routing del traffico" per descrivere l'uso dell'algoritmo per determinare a quale endpoint deve essere indirizzato un utente finale specifico in un determinato momento. In ASM questo metodo viene chiamato "metodo di bilanciamento del carico".

>- In ARM si usa il termine "ponderato" per indicare il metodo di routing del traffico che distribuisce il traffico tra tutti gli endpoint disponibili, in base al peso definito per ogni endpoint. In ASM questo metodo viene chiamato "round robin".
>- In ARM si usa il termine "priorità" per indicare il metodo di routing del traffico che indirizza tutto il traffico al primo endpoint disponibile in un elenco ordinato. In ASM questo metodo viene chiamato "failover".

> In ogni caso le uniche differenze riguardano la denominazione. Non esistono differenze in termini di funzionalità.


Gestione traffico di Azure supporta diversi algoritmi per determinare la modalità di indirizzamento degli utenti finali ai vari endpoint di servizio. Questi sono denominati metodi di routing del traffico. Il metodo di routing del traffico viene applicato a ogni query DNS ricevuta, per determinare l'endpoint da restituire nella risposta DNS.

In Gestione traffico sono disponibili tre metodi di routing del traffico:

- **Priorità:** selezionare "Priorità" quando si vuole usare un endpoint di servizio primario per tutto il traffico e prevedere endpoint di backup nel caso in cui l'endpoint primario o gli endpoint di backup non siano disponibili. Per altre informazioni, vedere [Metodo di routing del traffico Priorità](#priority-traffic-routing-method).

- **Ponderato:** selezionare "Ponderato" quando si vuole distribuire il traffico in un set di endpoint in modo uniforme o in base ai pesi definiti. Per altre informazioni, vedere [Metodo di routing del traffico Ponderato](#weighted-traffic-routing-method).

- **Prestazioni**: selezionare "Prestazioni" quando gli endpoint si trovano in aree geografiche diverse e si vuole che gli utenti finali usino l'endpoint "più vicino" in termini di latenza di rete più bassa. Per altre informazioni, vedere [Metodo di routing del traffico Prestazioni](#performance-traffic-routing-method).

> [AZURE.NOTE] Tutti i profili di Gestione traffico includono il monitoraggio continuo dell'integrità degli endpoint e il failover automatico degli endpoint. Queste funzionalità sono supportate per tutti i metodi di routing del traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).

Un singolo profilo di Gestione traffico può usare un solo metodo di routing del traffico. È possibile selezionare in qualsiasi momento un metodo di routing del traffico diverso per il profilo. Le modifiche vengono applicate entro 1 minuto e non si verificano periodi di inattività. I metodi di routing del traffico possono essere combinati usando profili nidificati di Gestione traffico. In questo modo, è possibile creare configurazioni di routing del traffico sofisticate e flessibili per soddisfare le esigenze di applicazioni più grandi e più complesse. Per altre informazioni, vedere [nested Traffic Manager profiles](traffic-manager-nested-profiles.md) (Profili nidificati di Gestione traffico).

## Metodo di routing del traffico Priorità

Un'organizzazione vuole spesso offrire la massima affidabilità per i propri servizi e a tale scopo dispone di uno o più servizi di backup in caso di inattività del servizio primario. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure][1]

Il profilo di Gestione traffico viene configurato con un elenco con priorità di endpoint di servizio. Per impostazione predefinita, tutto il traffico dell'utente finale viene inviato all'endpoint primario (o con priorità più elevata). Se l'endpoint primario non è disponibile, in base allo stato abilitato/disabilitato dell'endpoint configurato e al monitoraggio degli endpoint in corso, gli utenti vengono indirizzati al secondo endpoint. Se l'endpoint primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via.

La configurazione delle priorità degli endpoint viene eseguita in modo diverso nelle API di ARM (nel nuovo portale di Azure) rispetto alle API di ASM (nel portale classico):

- Nelle API di ARM la priorità degli endpoint viene configurata in modo esplicito, mediante la proprietà "Priorità" definita per ogni endpoint. Questa proprietà accetta un valore compreso tra 1 e 1000, dove i valori più bassi rappresentano una priorità più elevata. Due endpoint non possono avere lo stesso valore di priorità. La proprietà è facoltativa e, se viene omessa, viene usata una priorità predefinita in base all'ordine degli endpoint.

- Nelle API di ASM la priorità degli endpoint viene configurata in modo implicito, in base all'ordine in cui sono elencati gli endpoint nella definizione del profilo. È possibile configurare l'ordine di failover anche nel portale di Azure "classico", nella pagina di configurazione del profilo.

## Metodo di routing del traffico Ponderato

Un approccio comune per garantire disponibilità elevata e ottimizzare l'utilizzo del servizio consiste nel fornire un set di endpoint e distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito. Questa funzionalità è supportata dal metodo di routing del traffico "Ponderato".

![Metodo di routing del traffico "Ponderato" di Gestione traffico di Azure][2]

Nel metodo di routing del traffico Ponderato viene assegnato un peso a ogni endpoint come parte della configurazione del profilo di Gestione traffico. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro è facoltativo e, se viene omesso, viene usato il valore di peso predefinito "1".
  
Il traffico dell'utente finale viene distribuito tra tutti gli endpoint di servizio disponibili, in base allo stato abilitato/disabilitato dell'endpoint configurato e al monitoraggio degli endpoint in corso. Per ogni query DNS ricevuta, viene scelto un endpoint disponibile in modo casuale, con una probabilità basata sul peso assegnato a tale endpoint e agli altri endpoint disponibili.

L'uso dello stesso peso in tutti gli endpoint comporta una distribuzione uniforme del traffico, ideale per un utilizzo coerente in un set di endpoint identici. Se vengono usati pesi superiori (o inferiori) in determinati endpoint, questi verranno restituiti più (o meno) frequentemente nelle risposte DNS, pertanto riceveranno più traffico. In questo modo, vengono abilitati diversi scenari utili:

- Aggiornamento graduale dell'applicazione: allocare una percentuale di traffico da indirizzare a un nuovo endpoint, quindi incrementare gradualmente il traffico nel tempo fino al 100%.

- Migrazione dell'applicazione in Azure: creare un profilo con endpoint di Azure ed esterni, quindi specificare il peso del traffico indirizzato a ogni endpoint.

- Espansione del cloud per capacità aggiuntiva: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro a un profilo di Gestione traffico. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più endpoint e si può specificare la quantità di traffico da indirizzare a ogni endpoint.

Il routing del traffico Ponderato può essere configurato tramite il nuovo portale di Azure, ma non è possibile configurare i pesi tramite il portale "classico". Può essere configurato anche tramite ARM e ASM usando Azure PowerShell, l'interfaccia della riga di comando di Azure e le API REST di Azure.

Nota: le risposte DNS vengono memorizzate nella cache dai client e dai server DNS ricorsivi usati da tali client per creare le query DNS. È importante comprendere il potenziale impatto della memorizzazione nella cache delle distribuzioni del traffico ponderate. Se il numero di client e di server DNS ricorsivi è elevato, come avviene per le tipiche applicazioni con connessione Internet, la distribuzione del traffico funziona come previsto. Se il numero di client o di server DNS ricorsivi è limitato, la memorizzazione nella cache può tuttavia modificare significativamente la distribuzione del traffico. I casi d'uso comuni in cui può verificarsi questa situazione includono:

- Ambienti di sviluppo e test
- Comunicazioni tra applicazioni
- Applicazioni destinate a una base utenti ristretta che condivide un'infrastruttura DNS ricorsiva comune, ad esempio i dipendenti di un'organizzazione.

Questi effetti della memorizzazione nella cache DNS non riguardano solo Gestione traffico di Azure, ma sono comuni a tutti i sistemi di routing del traffico basati su DNS. In alcuni casi, la cancellazione della cache DNS in modo esplicito può risolvere il problema. In altri casi, può essere più appropriato un metodo di routing del traffico alternativo.

## Metodo di routing del traffico Prestazioni

La velocità di risposta di molte applicazioni può essere migliorata distribuendo gli endpoint in due o più posizioni a livello globale e indirizzando gli utenti finali alla posizione a loro "più vicina". Questo è lo scopo del metodo di routing del traffico "Prestazioni".

![Metodo di routing del traffico "Prestazioni" di Gestione traffico di Azure][3]

Per ottimizzare la velocità di risposta, l'endpoint "più vicino" non è necessariamente il più vicino in termini di distanza geografica. Il metodo di routing del traffico "Prestazioni" determina invece l'endpoint più vicino all'utente finale in termini di latenza di rete. La vicinanza viene determinata in base a una tabella della latenza di Internet in cui è indicato il tempo di round trip tra intervalli di indirizzi IP e ciascun data center di Azure.

Gestione traffico esamina ogni richiesta DNS in ingresso e cerca l'indirizzo IP di origine della richiesta nella tabella della latenza di Internet. In questo modo, viene determinata la latenza dall'indirizzo IP a ciascun data center di Azure. Gestione traffico sceglie poi gli endpoint disponibili con la latenza più bassa, in base allo stato abilitato/disabilitato dell'endpoint configurato e al monitoraggio degli endpoint in corso, e restituisce l'endpoint nella risposta DNS. L'utente finale viene pertanto indirizzato all'endpoint che garantisce la latenza più bassa e quindi le prestazioni migliori.

Come spiegato in [How Traffic Manager Works](traffic-manager-how-traffic-manager-works.md) (Funzionamento di Gestione traffico), Gestione traffico non riceve le query DNS direttamente dagli utenti finali, ma dal servizio DNS ricorsivo per il quale sono configurati. In questo modo l'indirizzo IP usato per determinare l'endpoint "più vicino" non è l'indirizzo IP dell'utente finale, ma l'indirizzo IP del relativo servizio DNS ricorsivo. In pratica, questo indirizzo IP è un proxy valido a questo scopo per l'utente finale.

Per adeguarsi alle modifiche nella rete Internet globale e all'aggiunta di nuove aree di Azure, Gestione traffico aggiorna regolarmente la tabella della latenza di Internet utilizzata. Non possono tuttavia essere prese in considerazione le variazioni in tempo reale delle prestazioni o del carico su Internet.

Con il routing del traffico Prestazioni non viene preso in considerazione il carico su un determinato endpoint di servizio, anche se Gestione traffico esegue il monitoraggio degli endpoint e non li include nelle risposte alle query DNS se non sono disponibili.

Punti da notare:

- Se il profilo contiene più endpoint nella stessa area di Azure, il traffico indirizzato all'area in questione viene distribuito in modo uniforme tra gli endpoint disponibili, in base allo stato abilitato/disabilitato dell'endpoint configurato e al monitoraggio degli endpoint in corso. Se si preferisce una distribuzione del traffico diversa all'interno di un'area, usare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

- Se tutti gli endpoint abilitati in una determinata area di Azure sono danneggiati, in base al monitoraggio degli endpoint in corso, il relativo traffico verrà distribuito tra tutti gli altri endpoint disponibili specificati nel profilo, anziché negli endpoint successivi più vicini. In questo modo è possibile evitare un errore a catena che potrebbe verificarsi in caso di overload del successivo endpoint più vicino. Se si preferisce definire la sequenza di failover degli endpoint, usare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

- Quando si usa il metodo di routing del traffico Prestazioni con endpoint esterni o endpoint nidificati, è necessario specificare la posizione di tali endpoint. Scegliere l'area di Azure più vicina alla distribuzione. Le opzioni disponibili sono le aree di Azure, ovvero le posizioni supportate dalla tabella della latenza di Internet.

- L'algoritmo che sceglie quale endpoint restituire a un determinato utente finale è deterministico, ovvero non implica alcun casualità. Le query DNS ripetute dallo stesso client verranno indirizzate allo stesso endpoint. È consigliabile tuttavia non fare affidamento sul fatto che il metodo di routing del traffico Prestazioni indirizzi sempre un determinato utente a una distribuzione specifica, operazione che potrebbe essere necessaria ad esempio se i dati per tale utente sono archiviati in una sola posizione. Quando viaggiano, gli utenti finali solitamente usano server DNS ricorsivi diversi, pertanto potrebbero essere indirizzati a un altro endpoint. Potrebbero inoltre risentire degli aggiornamenti della tabella della latenza di Internet.

- Quando viene aggiornata la tabella della latenza di Internet, è possibile notare che alcuni client vengono indirizzati a un endpoint diverso. Il numero di utenti interessati dovrebbe essere minimo e riflette un routing più accurato in base ai dati di latenza correnti. Questi aggiornamenti sono essenziali per garantire la precisione del routing del traffico Prestazioni man mano che Internet si evolve.


## Passaggi successivi

Informazioni su come sviluppare applicazioni a disponibilità elevata tramite [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)


<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

<!---HONumber=AcomDC_0824_2016-->