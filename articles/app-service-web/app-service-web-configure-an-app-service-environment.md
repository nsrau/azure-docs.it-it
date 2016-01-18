<properties 
	pageTitle="Come configurare un ambiente del servizio app" 
	description="Configurazione, gestione e monitoraggio degli ambienti del servizio app" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Configurazione di un ambiente del servizio app #

## Panoramica ##

Ambienti del servizio app è una funzionalità del piano Premium inclusa nel servizio app di Azure che offre nuove funzionalità di scalabilità e accesso di rete. La nuova funzionalità di scalabilità consente di posizionare un'istanza del servizio app di Azure nella propria rete virtuale. Questa funzionalità può ospitare app Web, app per dispositivi mobili e app per le API. Le app per la logica non vengono ancora eseguite in un ambiente del servizio app.

Se non si ha familiarità con la funzionalità Ambiente del servizio app, leggere il documento [Informazioni sull'ambiente del servizio app](app-service-app-service-environment-intro.md) Per informazioni su come creare un ambiente del servizio app, leggere il documento [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

In generale, un ambiente del servizio app è costituito da vari componenti principali:

- Risorse di calcolo in esecuzione nel servizio ospitato Ambiente del servizio app
- Archiviazione
- Database
- Una rete virtuale classica “v1” con almeno una subnet
- Subnet con il servizio ospitato Ambiente del servizio app in esecuzione al suo interno

Le risorse di calcolo vengono usate per i 4 pool di risorse. Ogni ambiente del servizio app contiene un set di server front-end e 3 pool di lavoro. Non è necessario usare tutti e 3 i pool di lavoro. Se lo si desidera, è possibile usarne solo uno. I front-end sono gli endpoint HTTP per le app contenute nell'ambiente del servizio app. I ruoli di lavoro è dove le app vengono effettivamente eseguite. Per determinare quando è necessario aggiungere front-end o ruoli di lavoro è necessario conoscere le prestazioni delle app nell'ambiente del servizio app. Ad esempio, si supponga che nell'ambiente del servizio app sia presente un'app hello world che riceve un numero elevato di richieste. In questo caso, sarà necessario aumentare i front-end per gestire il carico HTTP ma non è necessario aumentare i ruoli di lavoro. Gestire tutto questo manualmente può risultare complesso, in particolare se si considera che è probabile che ogni ambiente del servizio app contenga una combinazione di applicazioni con criteri per le prestazioni eterogenei. Tuttavia, per semplificare le operazioni, agli ambienti del servizio app è stata aggiunta la funzionalità di scalabilità automatica. Per informazioni dettagliate sul ridimensionamento e sulla scalabilità degli ambienti del servizio app, vedere [Come configurare la scalabilità automatica in un ambiente del servizio app][ASEAutoscale]

Ogni ambiente del servizio app è configurato con 500 GB di spazio di archiviazione. Questo spazio viene usato da tutte le app incluse nell'ambiente. Questo spazio di archiviazione rappresenta una parte dell'ambiente del servizio app e non è possibile cambiare l'impostazione per usare lo spazio di archiviazione del cliente.

Il database contiene le informazioni che definiscono l'ambiente, nonché tutti i dettagli sulle app eseguite al suo interno. Anche questo fa parte della sottoscrizione di Azure e i clienti non hanno la possibilità di modificare queste informazioni direttamente.

La rete virtuale usata con l'ambiente del servizio app può essere una rete creata durante la creazione dell'ambiente o in precedenza. Se si vuole che la rete virtuale si trovi in un gruppo di risorse separato da quello usato dall'ambiente del servizio app, è necessario creare la rete in un flusso diverso da quello usato per la creazione dell'ambiente del servizio app. È consigliabile creare contemporaneamente la subnet che si vuole usare, perché se si crea la subnet durante la creazione dell'ambiente del servizio app, l'ambiente si troverà nello stesso gruppo di risorse della rete virtuale. Al momento esiste solo il supporto per le reti virtuali V1 "classiche".

L'interfaccia utente per gestire e monitorare l'ambiente del servizio app è accessibile tramite il portale di Azure. Se è stato creato un ambiente del servizio app, verrà visualizzato il simbolo del servizio app nell'intestazione laterale. Questo simbolo viene usato per rappresentare gli ambienti del servizio app nel portale di Azure.

![][1]

È possibile usare l'icona o selezionare il simbolo > (segno di maggiore di) nella parte inferiore dell'intestazione laterale e selezionare Ambienti del servizio app. Entrambi svolgono la stessa operazione, ovvero aprire l'interfaccia utente che elenca tutti gli ambienti del servizio app disponibili. Se si seleziona uno degli ambienti del servizio app elencati, verrà visualizzata l'interfaccia utente per monitorarlo e gestirlo.

![][2]

Il primo pannello mostra alcune proprietà dell'ambiente del servizio app e un grafico di metriche per ogni pool di risorse. Alcune delle proprietà mostrate nel blocco Informazioni di base sono anche collegamenti ipertestuali che consentono di aprire il pannello associato. Ad esempio, se si seleziona Nome rete virtuale, verrà aperta l'interfaccia utente associata alla rete virtuale in cui è in esecuzione l'ambiente del servizio app. I collegamenti relativi ai piani e alle app del servizio app consentono di aprire i pannelli che visualizzano gli elementi corrispondenti inclusi nell'ambiente del servizio app.

## Monitoraggio ##

I grafici consentono di visualizzare un'ampia gamma di metriche sulle prestazioni in ogni pool di risorse. Per il pool front-end è consigliabile monitorare l'utilizzo medio di CPU e di memoria. Il carico dei front-end è distribuito, quindi osservando la media è possibile determinare le prestazioni generali. I pool di lavoro invece non si comportano allo stesso modo. I ruoli di lavoro in un pool di lavoro possono essere usati da più piani di servizio app. In questo caso, l'utilizzo di CPU e memoria non fornisce informazioni particolarmente utili. È più importante monitorare il numero di ruoli di lavoro usati e di quelli disponibili, soprattutto se questo sistema viene gestito per essere usato da altri.

Tutte le metriche che possono essere monitorate nei grafici possono essere usate anche per impostare gli avvisi. L'impostazione degli avvisi funziona come per tutte le altre metriche nel servizio app. È possibile impostare gli avvisi dall'interfaccia utente per gli avvisi oppure direttamente dall'interfaccia utente delle singole metriche facendo clic su Aggiungi avviso.
 
![][3]

Le metriche illustrate sopra sono le metriche dell'ambiente del servizio app. Sono anche disponibili metriche a livello del piano di servizio app. In questo caso, il monitoraggio di CPU e memoria risulta particolarmente utile. In un ambiente del servizio app, tutti i piani di servizio app sono piani dedicati. Di conseguenza, le uniche app in esecuzione negli host allocati a tale piano di servizio app sono le app incluse in quello stesso piano. Per visualizzare i dettagli relativi al piano di servizio app, visualizzare il piano di servizio app da uno degli elenchi nell'interfaccia utente dell'ambiente del servizio app o dalla pagina di esplorazione dei piani di servizio app in cui sono elencati tutti i piani.

È probabile che l'utente conosca già le funzionalità di scalabilità automatica disponibili per i piani di servizio app all'esterno di un ambiente del servizio app e il modo in cui tali funzionalità usano le metriche disponibili per una risorsa. La scalabilità automatica di un ambiente del servizio app funziona allo stesso modo. Non solo è possibile ridimensionare automaticamente il piano di servizio app in base alle metriche di un ambiente del servizio app, ma è anche possibile impostare regole di scalabilità per l'ambiente stesso. Per informazioni su come impostare la scalabilità automatica, vedere la guida dettagliata [Scalabilità automatica in un ambiente del servizio app][ASEAutoscale]


## Proprietà ##

Il pannello Impostazioni verrà aperto automaticamente quando si visualizza il pannello dell'ambiente del servizio app. Nella parte superiore è disponibile la voce Proprietà. In questa sezione sono presenti numerose voci visualizzate anche nella sezione Informazioni di base, ma quelle più importanti sono Indirizzo VIP e Indirizzo IP in uscita. Per ora, queste due opzioni indicano lo stesso indirizzo, ma in futuro potrebbe essere possibile che l'indirizzo IP in uscita sia diverso dall'indirizzo VIP. Se quindi non si prevede di configurare SSL e aggiungere un indirizzo IP per una singola app nell'ambiente del servizio app, l'indirizzo IP impostato nel DNS per le app create nell'ambiente del servizio app corrisponderà all'indirizzo VIP visualizzato in Proprietà.

![][4]

## Indirizzi IP ##

In questa sezione è possibile aggiungere più indirizzi IP che verranno usati dalle app incluse nell'ambiente del servizio app. Quando nell'ambiente del servizio app si crea un'app da configurare con la connessione IP SSL, è necessario usare un indirizzo IP riservato solo per quella app. Di conseguenza, nell'ambiente del servizio app devono essere disponibili alcuni indirizzi IP da allocare in caso di necessità. Al momento della creazione dell'ambiente del servizio app, è incluso un indirizzo che serve a questo scopo. Se occorrono altri indirizzi, è possibile aggiungerli qui. Prima di aggiungere il numero massimo di indirizzi da usare in caso di necessità, ricordare che è previsto un addebito per gli indirizzi IP aggiuntivi. Per informazioni dettagliate sugli addebiti, vedere la pagina [Prezzi di Servizio app][AppServicePricing]. Scorrere verso il basso fino alle sezione relativa alle connessioni SSL. Il prezzo indicato è il prezzo aggiuntivo per la connessione IP SSL.

**NOTA:** se si aggiungono più indirizzi IP, tenere presente che si tratta di un'operazione di ridimensionamento. Si può eseguire una sola operazione di ridimensionamento alla volta. Le operazioni di ridimensionamento disponibili sono tre:

- modifica del numero di indirizzi IP dell'ambiente del servizio app disponibili per l'utilizzo con la connessione IP SSL
- modifica delle dimensioni della risorsa di calcolo usata in un pool di risorse
- modifica del numero di risorse di calcolo usate in un pool di risorse sia manualmente che tramite scalabilità automatica

## Pool di risorse ##

Dal pannello Impostazioni è possibile accedere all'interfaccia utente di Pool front-end o Pool di lavoro. Ognuno di questi pannelli del pool di risorse consente di visualizzare informazioni relative solo a tale pool, oltre ai controlli per ridimensionarlo completamente.

Il pannello di base per ogni pool di risorse mostra un grafico con le metriche relative al pool di risorse specifico. Esattamente come per i grafici del pannello dell'ambiente del servizio app, è possibile passare al grafico e impostare gli avvisi in base alle esigenze. La procedura di configurazione di un avviso dal pannello dell'ambiente del servizio app per un pool di risorse specifico equivale alla stessa operazione nel pool di risorse. Dal pannello Impostazioni del pool di lavoro è possibile accedere all'elenco di tutte le app o dei piani di servizio app in esecuzione nel pool di lavoro.

![][5]

### Ridimensionamento della quantità di risorse di calcolo ###

Per informazioni più approfondite sul ridimensionamento delle app in un ambiente del servizio app, vedere [Ridimensionamento di app in un ambiente del servizio app](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Per altre informazioni su come configurare la scalabilità automatica per i pool di risorse dell'ambiente del servizio app, vedere [Scalabilità automatica in un ambiente del servizio app][ASEAutoscale]. Questo articolo descrive in modo dettagliato le operazioni di ridimensionamento manuale nei pool di risorse.

I pool di risorse, i front-end e i ruoli di lavoro non sono direttamente accessibili ai tenant. In altre parole, non è possibile accedervi tramite Remote Desktop Protocol, modificarne il provisioning o eseguire attività amministrative, perché la gestione e la manutenzione vengono effettuate da Azure. Tuttavia, le decisioni relative alla quantità e alle dimensioni delle risorse di calcolo spettano invece all'utente.

Sono disponibili tre modi per controllare il numero di server presenti nei pool di risorse - Passare al pannello Piano dal pannello dal pannello Ambiente del servizio app nella parte superiore - Eseguire un'operazione di ridimensionamento manuale dal pannello Piano del singolo pool di risorse, disponibile in Impostazioni - Impostare la scalabilità automatica nel pannello Piano del pool di risorse

Per cambiare il piano dal pannello Ambiente del servizio app, è sufficiente fare clic su di esso, trascinare il dispositivo di scorrimento sulla quantità desiderata e salvare. Questa interfaccia utente supporta anche la modifica delle dimensioni.

![][6]

Per usare le funzionalità di ridimensionamento manuale o di scalabilità automatica nel pool di risorse specifico, dal pannello Ambiente del servizio app passare a Impostazioni. In Impostazioni aprire Pool front-end o Pool di lavoro, in base alle esigenze, quindi aprire il pool che si vuole modificare. In Impostazioni, accanto a Piano, è disponibile un segno di maggiore di (>). Fare clic su di esso e impostare il ridimensionamento manuale o la scalabilità automatica.

![][7]

Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end. Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per garantire la tolleranza di errore, è tuttavia necessario allocare una risorsa di calcolo aggiuntiva in base alle regole seguenti:

- per ogni pool di lavoro è necessaria almeno una risorsa di calcolo aggiuntiva a cui non è possibile assegnare un carico di lavoro
- quando la quantità di risorse di calcolo in un pool di lavoro supera un determinato valore, è necessaria un'altra risorsa di calcolo per la tolleranza di errore. Ciò non accade per il pool front-end.

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore di X risorse assegnate a un pool di lavoro:

- se X è compreso tra 2 e 20, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-1
- se X è compreso tra 21 e 40, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-2
- se X è compreso tra 41 e 53, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-3

Ricordare che il footprint minimo include 2 server front-end e 2 ruoli di lavoro. In base alle istruzioni precedenti, ecco alcuni esempi.

- Se sono presenti 30 ruoli di lavoro in un singolo pool, è possibile usarne 28 per ospitare i carichi di lavoro. 
- Se sono presenti 2 ruoli di lavoro in un singolo pool, è possibile usarne 1 per ospitare i carichi di lavoro.
- Se sono presenti 20 ruoli di lavoro in un singolo pool, è possibile usarne 19 per ospitare i carichi di lavoro.  
- Se sono presenti 21 ruoli di lavoro in un singolo pool, è possibile usarne solo 19 per ospitare i carichi di lavoro.  

L'aspetto relativo alla tolleranza di errore è importante ed è necessario tenerne conto se si aumentano le risorse o le istanze al di sopra di determinate soglie. Se si vuole aggiungere capacità a partire da 20 istanze, passare a 22 o più, in quanto con 21 non viene aggiunta altra capacità. Lo stesso vale per 40 istanze. Anche in questo caso, il valore successivo per aggiungere capacità è 42.

### Ridimensionamento delle risorse di calcolo ###

Oltre a poter gestire la quantità di risorse di calcolo che si possono assegnare a un determinato pool, è anche possibile gestire le dimensioni. Con gli ambienti del servizio app, è possibile scegliere tra 4 dimensioni diverse, denominate da P1 a P4. Per informazioni dettagliate sulle dimensioni e sui rispettivi prezzi, vedere [Dettagli prezzi del servizio app](../app-service/app-service-value-prop-what-is.md). Le dimensioni delle risorse di calcolo da P1 a P3 corrispondono a quelle disponibili normalmente. La risorsa di calcolo con dimensioni P4 offre 8 core con 14 GB di RAM ed è disponibile solo in un ambiente del servizio app.

Per modificare le dimensioni delle risorse di calcolo usate nei pool, è possibile procedere in due modi. È disponibile il comando Ridimensiona nel pannello Ambiente del servizio app e nel pannello Piano tariffario a cui si accede dalle impostazioni del singolo pool di risorse.

![][8]

Prima di apportare modifiche, tenere presente quanto segue:

- le modifiche apportate possono richiedere fino a due ore, a seconda dell'importanza della modifica
- se è già in corso una modifica di configurazione dell'ambiente del servizio app, non è possibile avviare un'altra modifica
- se si modificano le dimensioni delle risorse di calcolo usate in un pool di lavoro, potrebbero verificarsi interruzioni del servizio per le app in esecuzione in quel pool di lavoro

L'aggiunta di altre istanze a un pool di lavoro è un'operazione innocua che non influisce sull'operatività delle app per le risorse contenute nel pool. Ma la modifica delle dimensioni della risorsa di calcolo usata in un pool di lavoro deve essere gestita con cautela. Per evitare tempi di inattività delle app durante una modifica delle dimensioni per un pool di lavoro, è consigliabile:

- usare un pool di lavoro inutilizzato per aggiungere le istanze necessarie con le dimensioni desiderate
- ridimensionare i piani di servizio app in base al nuovo pool di lavoro.  
 
Rispetto alla modifica delle dimensioni della risorsa di calcolo mentre sono in esecuzione i carichi di lavoro, questa alternativa non genera alcun impatto sulle app in esecuzione. Per informazioni dettagliate sul ridimensionamento delle app in un ambiente del servizio app, vedere [Ridimensionamento di app in un ambiente del servizio app](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## Rete virtuale ##

Diversamente dal servizio ospitato che contiene l'ambiente del servizio app, il controllo di [rete virtuale][virtualnetwork] e subnet spetta all'utente. Gli ambienti del servizio app prevedono alcuni requisiti di rete, ma tutto il resto può essere controllato dall'utente. I requisiti degli ambienti del servizio app sono:

- rete virtuale "v1" classica 
- una subnet con almeno 8 indirizzi 
- la rete virtuale deve essere una rete virtuale regionale  
 
È possibile amministrare la rete virtuale tramite l'interfaccia utente di Rete virtuale o tramite PowerShell.

Poiché il servizio app di Azure è collocato all'interno della rete virtuale, le app ospitate nell'ambiente del servizio app possono accedere direttamente alle risorse rese disponibili tramite le reti private virtuali ExpressRoute o da sito a sito. Le app all'interno degli ambienti del servizio app non richiedono ulteriori funzionalità di rete per accedere alle risorse disponibili nella rete virtuale che ospita l'ambiente del servizio app. Questo significa che non è necessario usare le funzionalità Integrazione rete virtuale o Connessioni ibride per accedere alle risorse incluse o connesse alla rete virtuale. È comunque possibile usare entrambe queste funzionalità per accedere a risorse in reti non connesse alla propria rete virtuale. Si può ad esempio usare la funzionalità Integrazione rete virtuale per eseguire l'integrazione con una rete virtuale inclusa nella propria sottoscrizione ma non connessa alla rete virtuale in cui è ospitato l'ambiente del servizio app oppure usare la funzionalità Connessioni ibride per accedere a risorse in altre reti, come normalmente.

Se la rete virtuale è configurata con una VPN ExpressRoute, è necessario tenere conto delle eventuali esigenze di routing specifiche dell'ambiente del servizio app. Esistono alcune configurazioni di route definite dall'utente che non sono compatibili con un ambiente del servizio app. Per altre informazioni sull'esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute, vedere l'articolo relativo all'[esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute][ExpressRoute]

È anche possibile controllare gli accessi alle app tramite i gruppi di sicurezza di rete. Questa funzionalità consente di bloccare l'ambiente del servizio app e consentire l'accesso solo agli indirizzi IP desiderati. Per altre informazioni su come limitare gli accessi, vedere [Come controllare il traffico in ingresso in un ambiente del servizio app](app-service-app-service-environment-control-inbound-traffic.md).

## Eliminazione di un ambiente del servizio app ##

Per eliminare un ambiente del servizio app, usare l'opzione Elimina nella parte superiore del pannello Ambiente del servizio app. In questo caso, verrà richiesto di immettere il nome dell'ambiente del servizio app per confermare che si vuole procedere con l'operazione. NOTA: quando si elimina un ambiente del servizio app, viene eliminato anche tutto il contenuto al suo interno.

![][9]

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0107_2016-->