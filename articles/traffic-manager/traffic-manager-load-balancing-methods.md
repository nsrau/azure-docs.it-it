<properties 
   pageTitle="Gestione traffico- Metodi di routing del traffico | Microsoft Azure"
   description="Questo articolo aiuterà a comprendere i diversi metodi di routing del traffico usati da Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Metodi di routing di Gestione traffico

Sono disponibili tre metodi di routing del traffico in Gestione traffico. In ogni profilo di Gestione traffico può essere usato un solo metodo di routing del traffico alla volta, sebbene in qualsiasi momento sia possibile selezionarne uno diverso.

È importante notare che tutti i metodi di routing del traffico prevedono il monitoraggio degli endpoint. Dopo aver configurato il profilo di Gestione traffico per specificare il metodo di routing che meglio soddisfa le proprie esigenze, configurare le impostazioni di monitoraggio. Quando il monitoraggio è configurato correttamente, Gestione traffico monitora lo stato degli endpoint, costituiti da servizi cloud e siti Web, e non invia il traffico agli endpoint ritenuti non disponibili. Per informazioni su Gestione traffico, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).

I tre metodi di routing di Gestione traffico sono:

- **Failover**: selezionare Failover quando gli endpoint si trovano nello stesso data center o in data center diversi di Azure, noti come regioni nel portale di gestione, e si desidera usare un endpoint primario per tutto il traffico, ma fornire backup da usare nel caso in cui l'endpoint primario o di backup non sia disponibile. Per ulteriori informazioni, vedere[Metodo di routing del traffico Failover](#failover-traffic-routing-method).

- **Round robin**: selezionare Round robin quando si desidera distribuire il carico in un set di endpoint nello stesso data center o in data center diversi. Per ulteriori informazioni, vedere[Metodo di routing del traffico Round Robin](#round-robin-traffic-routing-method).

- **Prestazioni**: selezionare Prestazioni quando gli endpoint si trovano in aree geografiche diverse e si desidera richiedere ai client di usare l'endpoint "più vicino" in termini di latenza più bassa. Per ulteriori informazioni, vedere[Metodo di routing del traffico delle prestazioni](#performance-traffic-routing-method).

Tenere presente che Siti Web di Azure offre già funzionalità di routing del traffico round-robin per i siti web che si trovano all'interno di un data center, indipendentemente dalla modalità del sito Web. Gestione traffico consente di specificare il failover e il routing del traffico round-robin per i siti web che si trovano in data center diversi.

>[AZURE.NOTE]La durata TTL del DNS indica ai client e ai resolver DNS sui server DNS per quanto tempo i nomi risolti rimarranno memorizzati nella cache. I client continueranno a usare un endpoint specificato durante la risoluzione del nome di dominio fino alla scadenza della voce cache DNS locale per il nome.

## Metodo di routing del traffico failover

In genere le organizzazioni desiderano offrire la massima affidabilità per i servizi erogati. A questo scopo, è necessario fornire servizi di backup in caso di inattività del servizio primario. Un modello comune di failover del servizio consiste nel fornire un set di endpoint identici e inviare il traffico a un servizio primario, con un elenco di uno o più backup. Se il servizio primario non è disponibile, i client richiedenti vengono indirizzati a quello successivo nell'ordine. Se il primo e il secondo servizio nell'elenco non sono disponibili, il traffico viene indirizzato al terzo e così via.

Quando si configura il metodo di routing del traffico Failover, l'ordine degli endpoint selezionati è importante. Con il portale di gestione è possibile configurare l'ordine di failover nella pagina Configurazione del profilo.

Nella Figura 1 viene illustrato un esempio di metodo di routing del traffico Failover per un set di endpoint.

![Bilanciamento del carico di failover di Gestione traffico](./media/traffic-manager-load-balancing-methods/IC750592.jpg)

**Figura 1**

I passaggi numerati seguenti corrispondono ai numeri nella Figura 1.

1. Gestione traffico riceve una richiesta in ingresso da un client tramite DNS e individua il profilo.
2. Il profilo contiene un elenco ordinato di endpoint. Gestione traffico individua il primo endpoint nell'elenco. Se l'endpoint è online, in base al monitoraggio dell'endpoint in corso, viene specificato questo nome DNS dell'endpoint nella risposta DNS al client. Se l'endpoint è offline, Gestione traffico determina il successivo endpoint online nell'elenco. In questo esempio CS-A è offline (non disponibile), mentre CS-B è online (disponibile).
3. Gestione traffico restituisce il nome di dominio di CS-B al server DNS del client, che risolve il nome di dominio in un indirizzo IP e lo invia al client.
4. Il client avvia il traffico verso CS-B.

## Metodo di routing del traffico round robin

Un modello comune di routing del traffico consiste nel fornire un set di endpoint identici e nell'inviare traffico a ciascuno di essi in base a uno schema round robin. Il metodo Round robin divide il traffico tra vari endpoint. Seleziona un endpoint integro casuale e non invia il traffico ai servizi ritenuti inattivi. Per altre informazioni, vedere [Monitoraggio di Gestione traffico](../traffic-manager-onitoring.md).

La Figura 2 mostra un esempio di routing del traffico Round robin per un set di endpoint.

![Bilanciamento del carico round robin di Gestione traffico](./media/traffic-manager-load-balancing-methods/IC750593.jpg)

**Figura 2**

I passaggi numerati seguenti corrispondono ai numeri nella Figura 2.

1. Gestione traffico riceve una richiesta in ingresso da un client e individua il profilo.
2. Il profilo contiene un elenco di endpoint. Gestione traffico seleziona un endpoint a caso dall'elenco, escludendo gli endpoint offline (non disponibili) determinati mediante il monitoraggio degli endpoint di Gestione traffico. In questo esempio si tratta dell'endpoint CS-B.
3. Gestione traffico restituisce il nome di dominio di CS-B al server DNS del client. Il server DNS del client risolve il nome di dominio in un indirizzo IP e lo invia al client.
4. Il client avvia il traffico verso CS-B.

Il routing del traffico Round robin supporta anche la distribuzione ponderata del traffico di rete. La Figura 3 mostra un esempio di routing del traffico Round robin ponderato per un set di endpoint.

![Bilanciamento del carico ponderato round robin](./media/traffic-manager-load-balancing-methods/IC750594.png)

**Figura 3**

Il routing del traffico con metodo Round robin ponderato consente di distribuire il carico a vari endpoint in base a un valore di peso assegnato a ciascuno. Maggiore il peso, maggiore sarà la frequenza di restituzione di un endpoint. Questo metodo può essere utile negli scenari seguenti:

- Aggiornamento graduale dell'applicazione: allocare una percentuale di traffico da indirizzare a un nuovo endpoint, quindi incrementare gradualmente il traffico nel tempo fino al 100%.
- Migrazione dell'applicazione in Azure: creare un profilo con endpoint di Azure ed esterni, quindi specificare il peso del traffico indirizzato a ogni endpoint.
- Espansione del cloud per capacità aggiuntiva: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro a un profilo di Gestione traffico. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più endpoint e si può specificare la quantità di traffico da indirizzare a ogni endpoint.

Attualmente, non si può usare il portale di gestione per configurare il routing del traffico ponderato. Azure offre accesso a livello di codice a questo metodo mediante l'API REST di gestione dei servizi associata e i cmdlet di Azure PowerShell.

Per informazioni sull'uso delle API REST, vedere [Operazioni per Gestione traffico (documentazione di riferimento sulle API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Per informazioni sull'uso dei cmdlet di Azure PowerShell, vedere [Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769). Per una configurazione di esempio, vedere [Endpoint esterni di Gestione traffico di Azure e metodo Round robin ponderato tramite PowerShell nel blog di Azure](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

Per testare il profilo da un singolo client e osservare il comportamento del metodo Round robin uniforme o ponderato, assicurarsi che il nome DNS venga risolto in un indirizzo IP diverso per ogni endpoint in base ai valori uniformi o ponderati definiti nel profilo. Durante il test è necessario disabilitare la memorizzazione nella cache DNS sul lato client oppure cancellare la cache DNS tra un tentativo e l'altro per assicurarsi che venga inviata una nuova query relativa al nome DNS.

## Metodo di routing del traffico delle prestazioni

Per effettuare il routing del traffico degli endpoint situati in diversi data center a livello globale, è possibile indirizzare il traffico in ingresso all'endpoint più vicino in termini di latenza più bassa fra il client richiedente e l'endpoint. In genere, l'endpoint più vicino corrisponde direttamente alla distanza geografica più breve. Il metodo di routing del traffico basato sulle prestazioni consente di distribuire il carico in base alla posizione e alla latenza, ma non può tenere conto delle modifiche in tempo reale della configurazione della rete o del carico.

Il metodo di routing del traffico Prestazioni individua il client richiedente e lo indirizza all'endpoint più vicino. La vicinanza viene determinata in base a una tabella della latenza di Internet in cui è indicato il tempo di round trip tra i diversi indirizzi IP e ciascun data center di Azure. Questa tabella viene aggiornata periodicamente e non deve essere intesa con un indicatore di prestazioni in tempo reale su Internet. Con questo metodo il carico su un servizio specificato non viene preso in considerazione, anche se tramite Gestione traffico vengono monitorati gli endpoint in base al metodo scelto e non verranno inclusi nelle risposte alle query DNS se non sono disponibili. In altre parole, il routing del traffico delle prestazioni incorpora inoltre il metodo di routing del traffico di Failover.

La Figura 4 illustra un esempio di metodo di routing del traffico Prestazioni per un set di endpoint.

![Bilanciamento del carico delle prestazioni di Gestione traffico](./media/traffic-manager-load-balancing-methods/IC753237.jpg)

**Figura 4**

I passaggi numerati seguenti corrispondono ai numeri nella Figura 4.

1. Gestione traffico compila periodicamente la tabella della latenza di Internet. Sull'infrastruttura di Gestione traffico vengono eseguiti test per determinare i tempi di round trip tra diversi punti nel mondo e i data center di Azure in cui sono ospitati gli endpoint.
2. Gestione traffico riceve una richiesta in ingresso da un client tramite il server DNS locale e individua il profilo.
3. Gestione traffico individua la riga nella tabella della latenza di Internet relativa all'indirizzo IP della richiesta DNS in ingresso. Poiché il server DNS locale dell'utente esegue una query DNS iterativa per trovare il server DNS autorevole per il nome del profilo di Gestione traffico, la query DNS viene inviata dall'indirizzo IP del server DNS locale del client.
4. Gestione traffico individua il data center con il tempo più breve tra i data center che ospitano gli endpoint definiti nel profilo. In questo esempio si tratta di CS-B.
5. Gestione traffico restituisce il nome di dominio di CS-B al server DNS locale del client, che risolve il nome di dominio in un indirizzo IP e lo invia al client.
6. Il client avvia il traffico verso CS-B.

**Punti da notare:**

- Se nel profilo sono contenuti più endpoint nello stesso data center, il traffico indirizzato al data center in questione viene distribuito in modo uniforme tra gli endpoint disponibili e integri in base al monitoraggio dell'endpoint.
- Se tutti gli endpoint in un data center specificato non sono disponibili, in base al monitoraggio dell'endpoint, il relativo traffico verrà distribuito tra tutti gli altri endpoint disponibili specificati nel profilo, anziché negli endpoint successivi più vicini. In questo modo è possibile evitare un errore a catena che potrebbe verificarsi in caso di overload del successivo endpoint più vicino.
- Quando viene aggiornata la tabella della latenza di Internet, è possibile riscontrare una differenza nel carico e nei modelli di traffico sugli endpoint. Queste differenze dovrebbero essere minime.
- Quando si usa il metodo di routing del traffico Prestazioni con endpoint esterni, sarà necessario specificare la posizione di tali endpoint. Scegliere l'area di Azure più vicina alla distribuzione specifica. Per altre informazioni, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).

## Figure di Gestione traffico

Se si vogliono scaricare le figure presenti in questo argomento come diapositive di PowerPoint da usare per una presentazione su Gestione traffico o da modificare per altri scopi, vedere [Figure di Gestione traffico nella documentazione MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Passaggi successivi

[Gestione traffico di Azure](traffic-manager-overview.md)

[Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=Oct15_HO3-->