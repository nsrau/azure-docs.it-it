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
	ms.date="04/04/2016" 
	ms.author="ccompy"/>


# Configurazione di un ambiente del servizio app #

## Panoramica ##

In generale, un ambiente del servizio app è costituito da vari componenti principali:

- Risorse di calcolo in esecuzione nel servizio ospitato Ambiente del servizio app
- Archiviazione
- Database
- Una rete virtuale classica “v1” con almeno una subnet
- Subnet con il servizio ospitato Ambiente del servizio app in esecuzione al suo interno

### Risorse di calcolo

Le risorse di calcolo vengono usate per i 4 pool di risorse. Ogni ambiente del servizio app contiene un set di server front-end e 3 pool di lavoro possibili. Non è necessario usare tutti e tre i pool di lavoro. Se si vuole, è possibile usarne solo uno o due. I pool di risorse, i front-end e i ruoli di lavoro non sono direttamente accessibili ai tenant. Non è possibile accedervi tramite Remote Desktop Protocol (RDP), modificarne il provisioning o eseguire attività amministrative, ma è possibile impostarne la quantità e le dimensioni In un ambiente del servizio app sono disponibili 4 opzioni di dimensioni denominate da P1 a P4. Per informazioni sulle dimensioni e sui relativi prezzi, vedere [Dettagli prezzi del servizio app](../app-service/app-service-value-prop-what-is.md). È possibile eseguire una sola operazione di ridimensionamento alla volta.

**Front-end**: i front-end sono gli endpoint HTTP per le app contenute nell'ambiente del servizio app. Non è possibile eseguire carichi di lavoro nei front-end.

- Un ambiente inizia con (2) P2 sufficiente per carichi di lavoro di sviluppo e test e carichi di lavoro di produzione di livello basso. I P3 sono fortemente consigliati per carichi di lavoro di produzione da moderati a elevati.
- Per carichi di lavoro da moderati a elevati, è consigliabile avere almeno 4 x P3 per fare in modo che sia presente un numero sufficiente di front-end in esecuzione quando viene eseguita la manutenzione pianificata. Le attività di manutenzione pianificate arresteranno (1) front-end alla volta, riducendo in questo modo la capacità complessiva di front-end disponibili durante le attività di manutenzione.
- Non è possibile aggiungere immediatamente una nuova istanza di front-end. Il provisioning richiede da 2 a 3 ore.
- Per un'ulteriore ottimizzazione della scalabilità, i clienti devono monitorare la percentuale di CPU, la percentuale di memoria e le metriche di richieste attive per il pool di front-end. Se la percentuale di CPU o memoria è superiore al 70% durante l'esecuzione dei P3, aggiungere ulteriori front-end. Anche nel caso in cui la media delle richieste attive è compresa tra 15 K e 20 K per ogni front-end, è necessario aggiungere ulteriori front-end. L'obiettivo generale è quello di mantenere le percentuali di CPU e memoria inferiori al 70% e la media delle richieste attive al di sotto dei 15 K per ogni front-end durante l'esecuzione dei P3.  

**Ruoli di lavoro** I ruoli di lavoro rappresentano la posizione in cui le app vengono effettivamente eseguite. Quando si aumenta il livello dei piani di servizio app, vengono usati ruoli di lavoro elevati del pool di lavoro associato.

- Non è possibile aggiungere immediatamente ruoli di lavoro. Il provisioning può richiedere da 2 a 3 ore, indipendentemente dal numero di ruoli aggiunti.
- Il ridimensionamento di una risorsa di calcolo di qualsiasi pool richiederà 2-3 ore per ogni dominio di aggiornamento. Sono disponibili 20 domini di aggiornamento in un ambiente del servizio app. Il ridimensionamento del calcolo di un pool di ruoli di lavoro con 10 istanze può richiedere da 20 a 30 ore. 
- Se si modificano le dimensioni delle risorse di calcolo usate in un pool di lavoro, potrebbero verificarsi avvii a freddo delle app in esecuzione nel pool di lavoro

Il modo più rapido per modificare le dimensioni delle risorse di calcolo di un pool di lavoro che non esegue alcuna app consiste nel:

- ridurre il totale delle istanze a 0. La deallocazione delle istanze richiederà circa 30 minuti
- selezionare la nuova dimensione di calcolo e il numero di istanze. Questa operazione richiederà da 2 a 3 ore.

Se le app richiedono dimensioni di risorse di calcolo più elevate, le istruzioni indicate in precedenza non risulteranno utili. Anziché modificare le dimensioni del pool di lavoro che ospita tali app è possibile popolare un altro pool di lavoro con ruoli di lavoro delle dimensioni desiderate e spostare le app in tale pool.

- creare le istanze aggiuntive delle dimensioni di calcolo necessarie in un altro pool di lavoro. Questa operazione richiederà da 2 a 3 ore.
- riassegnare i piani del servizio app che ospitano le app che richiedono una dimensione maggiore al pool di lavoro appena configurato. Questa operazione è rapida e richiede meno di un minuto per il completamento.  
- ridurre il primo pool di lavoro se le istanze inutilizzate non sono più necessarie. Questa operazione richiede circa 30 minuti.

**Scalabilità automatica** La scalabilità automatica è uno degli strumenti che consentono di gestire il consumo di risorse di calcolo. La scalabilità automatica può essere applicata a front-end o pool di lavoro. È possibile ad esempio aumentare le istanze di qualsiasi tipo di pool durante la mattinata e ridurlo nelle ore serali oppure aggiungere istanze quando il numero di ruoli di lavoro disponibili in un pool di lavoro scende al di sotto di una determinata soglia. Se si vuole impostare le regole di scalabilità automatica per le metriche del pool di risorse di calcolo, tenere presente il tempo necessario per il provisioning. Per altre informazioni sulla scalabilità automatica degli ambienti del servizio app, vedere [Scalabilità automatica e ambiente del servizio app][ASEAutoscale]

### Archiviazione

Ogni ambiente del servizio app è configurato con 500 GB di spazio di archiviazione. Questo spazio viene usato da tutte le app incluse nell'ambiente. Questo spazio di archiviazione rappresenta una parte dell'ambiente del servizio app e non è possibile cambiare l'impostazione per usare lo spazio di archiviazione del cliente. Se si apportano modifiche al routing o alla sicurezza della rete virtuale è necessario consentire l'accesso all'archiviazione di Azure, altrimenti l'ambiente del servizio app non funziona.

### Database

Il database contiene le informazioni che definiscono l'ambiente, nonché tutti i dettagli sulle app eseguite al suo interno. Anche questo fa parte della sottoscrizione di Azure e i clienti non hanno la possibilità di modificare queste informazioni direttamente. Se si apportano modifiche al routing o alla sicurezza della rete virtuale è necessario consentire l'accesso a SQL Azure, altrimenti l'ambiente del servizio app non funziona.

### Network

La rete virtuale usata con l'ambiente del servizio app può essere una rete creata durante la creazione dell'ambiente o in precedenza. Se si vuole che la rete virtuale si trovi in un gruppo di risorse separato da quello usato dall'ambiente del servizio app, è necessario creare la rete in un flusso diverso da quello usato per la creazione dell'ambiente del servizio app. Se la subnet viene creata durante la creazione dell'ambiente del servizio app, l'ambiente apparterrà allo stesso gruppo di risorse della rete virtuale.

Esistono alcune restrizioni per la rete virtuale usata per un ambiente del servizio app:

- Attualmente è disponibile solo il supporto per le reti virtuali V1 "classiche".
- la rete virtuale deve essere una rete virtuale regionale
- Le reti virtuali usate per l'hosting di un ambiente del servizio app devono usare indirizzi RFC1918, ovvero indirizzi privati
- è necessaria una subnet con 8 o più indirizzi nella posizione in cui viene distribuito l'ambiente del servizio app
- Se una subnet viene usata per l'hosting di un ambiente del servizio app, l'intervallo di indirizzi della subnet non può essere modificato. Per questo motivo è consigliabile usare una subnet contenente almeno 64 indirizzi per supportare un'eventuale crescita futura dell'ambiente del servizio app 

Diversamente dal servizio ospitato che contiene l'ambiente del servizio app, il controllo di [rete virtuale][virtualnetwork] e subnet spetta all'utente. È possibile amministrare la rete virtuale tramite l'interfaccia utente di Rete virtuale o tramite PowerShell.

Poiché il servizio app di Azure è collocato all'interno della rete virtuale, le app ospitate nell'ambiente del servizio app possono accedere direttamente alle risorse rese disponibili tramite le reti private virtuali ExpressRoute o da sito a sito. Le app all'interno degli ambienti del servizio app non richiedono ulteriori funzionalità di rete per accedere alle risorse disponibili nella rete virtuale che ospita l'ambiente del servizio app. Questo significa che non è necessario usare le funzionalità Integrazione rete virtuale o Connessioni ibride per accedere alle risorse incluse o connesse alla rete virtuale. È comunque possibile usare entrambe queste funzionalità per accedere a risorse in reti non connesse alla propria rete virtuale. Si può ad esempio usare la funzionalità Integrazione rete virtuale per eseguire l'integrazione con una rete virtuale inclusa nella propria sottoscrizione ma non connessa alla rete virtuale in cui è ospitato l'ambiente del servizio app oppure usare la funzionalità Connessioni ibride per accedere a risorse in altre reti, come normalmente.

Se la rete virtuale è configurata con una VPN ExpressRoute, è necessario tenere conto delle eventuali esigenze di routing specifiche dell'ambiente del servizio app. Esistono alcune configurazioni di route definite dall'utente che non sono compatibili con un ambiente del servizio app. Per altre informazioni sull'esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute, vedere l'articolo relativo all'[esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute][ExpressRoute]

È anche possibile controllare gli accessi alle app tramite i gruppi di sicurezza di rete. Questa funzionalità consente di bloccare l'ambiente del servizio app e consentire l'accesso solo agli indirizzi IP desiderati. Per altre informazioni su come limitare gli accessi, vedere [Come controllare il traffico in ingresso in un ambiente del servizio app](app-service-app-service-environment-control-inbound-traffic.md).

## Portale

L'interfaccia utente per gestire e monitorare l'ambiente del servizio app è accessibile tramite il portale di Azure. Se è stato creato un ambiente del servizio app, verrà visualizzato il simbolo del servizio app nell'intestazione laterale. Questo simbolo viene usato per rappresentare gli ambienti del servizio app nel portale di Azure.

![][1]

È possibile usare l'icona o selezionare il simbolo > (segno di maggiore di) nella parte inferiore dell'intestazione laterale e selezionare Ambienti del servizio app. Entrambi svolgono la stessa operazione, ovvero aprire l'interfaccia utente che elenca tutti gli ambienti del servizio app disponibili. Se si seleziona uno degli ambienti del servizio app elencati, verrà visualizzata l'interfaccia utente per monitorarlo e gestirlo.

![][2]

Il primo pannello mostra alcune proprietà dell'ambiente del servizio app e un grafico di metriche per ogni pool di risorse. Alcune delle proprietà mostrate nel blocco Informazioni di base sono anche collegamenti ipertestuali che consentono di aprire il pannello associato. Ad esempio, se si seleziona Nome rete virtuale, verrà aperta l'interfaccia utente associata alla rete virtuale in cui è in esecuzione l'ambiente del servizio app. I collegamenti relativi ai piani e alle app del servizio app consentono di aprire i pannelli che visualizzano gli elementi corrispondenti inclusi nell'ambiente del servizio app.

### Monitoraggio

I grafici consentono di visualizzare un'ampia gamma di metriche sulle prestazioni in ogni pool di risorse. Per il pool di front-end, monitorare l'utilizzo medio di CPU e memoria. Per i pool di lavoro, monitorare la quantità usata e la quantità disponibile. I ruoli di lavoro in un pool di lavoro possono essere usati da più piani di servizio app. Poiché il carico di lavoro non viene distribuito nello stesso modo in cui viene distribuito per i server front-end, l'utilizzo della CPU e della memoria non offre informazioni utili. È più importante monitorare il numero di ruoli di lavoro usati e di quelli disponibili, soprattutto se questo sistema viene gestito per essere usato da altri.

Tutte le metriche che possono essere monitorate nei grafici possono essere usate anche per impostare gli avvisi. L'impostazione degli avvisi funziona come per tutte le altre metriche nel servizio app. È possibile impostare gli avvisi dall'interfaccia utente per gli avvisi oppure direttamente dall'interfaccia utente delle singole metriche facendo clic su Aggiungi avviso.
 
![][3]

Le metriche illustrate sopra sono le metriche dell'ambiente del servizio app. Sono anche disponibili metriche a livello del piano di servizio app. In questo caso, il monitoraggio di CPU e memoria risulta particolarmente utile. In un ambiente del servizio app, tutti i piani di servizio app sono piani dedicati. Di conseguenza, le uniche app in esecuzione negli host allocati a tale piano di servizio app sono le app incluse in quello stesso piano. Per visualizzare i dettagli relativi al piano di servizio app, visualizzare il piano di servizio app da uno degli elenchi nell'interfaccia utente dell'ambiente del servizio app o dalla pagina di esplorazione dei piani di servizio app in cui sono elencati tutti i piani.

### Impostazioni

Il pannello dell'ambiente del servizio app include una sezione Impostazioni che contiene diverse funzionalità importanti

**Impostazioni > Proprietà** Il pannello Impostazioni viene aperto automaticamente quando si visualizza il pannello dell'ambiente del servizio app. Nella parte superiore è disponibile la voce Proprietà. In questa sezione sono presenti numerose voci visualizzate anche nella sezione Informazioni di base, ma quelle più importanti sono Indirizzo VIP e Indirizzo IP in uscita.

![][4]

**Impostazioni > Indirizzi IP** Quando viene creata un'app IP SSL nell'ambiente del servizio app è necessario un indirizzo IP SSL. Di conseguenza, nell'ambiente del servizio app devono essere disponibili indirizzi IP SSL da allocare. Quando viene creato, l'ambiente del servizio app include un indirizzo IP SSL, ma è possibile aggiungerne altri. Per gli indirizzi IP SSL aggiuntivi è previsto un addebito come indicato in [Prezzi del servizio app][AppServicePricing] nella sezione relativa alle connessioni SSL. Il prezzo indicato è il prezzo aggiuntivo per la connessione IP SSL.

**Impostazioni > Pool front end/Pool di lavoro** Ognuno di questi pannelli dei pool di risorse consente di visualizzare informazioni relative al pool specifico, oltre ai controlli per ridimensionarlo completamente.

Il pannello di base per ogni pool di risorse mostra un grafico con le metriche relative al pool di risorse specifico. Esattamente come per i grafici del pannello dell'ambiente del servizio app, è possibile passare al grafico e impostare gli avvisi in base alle esigenze. La procedura di configurazione di un avviso dal pannello dell'ambiente del servizio app per un pool di risorse specifico equivale alla stessa operazione nel pool di risorse. Dal pannello Impostazioni del pool di lavoro è possibile accedere all'elenco di tutte le app o dei piani di servizio app in esecuzione nel pool di lavoro.

![][5]

### Funzionalità di scalabilità del portale  

Le operazioni di ridimensionamento disponibili sono tre:

- modifica del numero di indirizzi IP dell'ambiente del servizio app disponibili per l'utilizzo con la connessione IP SSL
- modifica delle dimensioni della risorsa di calcolo usata in un pool di risorse
- modifica del numero di risorse di calcolo usate in un pool di risorse sia manualmente che tramite scalabilità automatica

Nel portale sono disponibili tre modi per controllare il numero di server disponibili nei pool di risorse

- Operazione di ridimensionamento dal pannello dell'ambiente del servizio app principale nella parte superiore È possibile apportare più modifiche alla configurazione di scalabilità di front-end e pool di lavoro che vengono applicate come un'unica operazione.
- Operazione di ridimensionamento manuale dal pannello di ridimensionamento del singolo pool di risorse in Impostazioni
- Ridimensionamento automatico impostato dal pannello di ridimensionamento del singolo pool di risorse

Per eseguire l'operazione di ridimensionamento nel pannello dell'ambiente del servizio app, trascinare il dispositivo di scorrimento sulla quantità desiderata e salvare. Questa interfaccia utente supporta anche la modifica delle dimensioni.

![][6]

Per usare le funzionalità di scalabilità automatica o manuale in un pool di risorse specifico, passare a *Impostazioni > Pool front-end/Pool di lavoro* e aprire il pool che si vuole modificare. Passare a *Impostazioni > Scala orizzontalmente o Impostazioni > Scala verticalmente*. Il pannello *Scala orizzontalmente* consente di controllare la quantità di istanze. *Scala verticalmente* consente di controllare le dimensioni delle risorse.

![][7]

## Considerazioni sulla tolleranza di errore

Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end. Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per garantire la tolleranza di errore, è tuttavia necessario allocare una risorsa di calcolo aggiuntiva in base alle regole seguenti:

- per ogni pool di lavoro è necessaria almeno una risorsa di calcolo aggiuntiva a cui non è possibile assegnare un carico di lavoro
- quando la quantità di risorse di calcolo in un pool di lavoro supera un determinato valore, è necessaria un'altra risorsa di calcolo per la tolleranza di errore. Ciò non accade per il pool front-end.

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore di X risorse assegnate a un pool di lavoro:

- se X è compreso tra 2 e 20, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-1
- se X è compreso tra 21 e 40, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-2
- se X è compreso tra 41 e 53, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-3

Il footprint minimo include 2 server front-end e 2 ruoli di lavoro. In base alle istruzioni precedenti, ecco alcuni esempi.

- Se sono presenti 30 ruoli di lavoro in un singolo pool, è possibile usarne 28 per ospitare i carichi di lavoro. 
- Se sono presenti 2 ruoli di lavoro in un singolo pool, è possibile usarne 1 per ospitare i carichi di lavoro.
- Se sono presenti 20 ruoli di lavoro in un singolo pool, è possibile usarne 19 per ospitare i carichi di lavoro.  
- Se sono presenti 21 ruoli di lavoro in un singolo pool, è possibile usarne solo 19 per ospitare i carichi di lavoro.  

L'aspetto relativo alla tolleranza di errore è importante ed è necessario tenerne conto se si aumentano le risorse o le istanze al di sopra di determinate soglie. Se si vuole aggiungere capacità a partire da 20 istanze, passare a 22 o più, in quanto con 21 non viene aggiunta altra capacità. Lo stesso vale per 40 istanze. Anche in questo caso, il valore successivo per aggiungere capacità è 42.

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

<!---HONumber=AcomDC_0413_2016-->