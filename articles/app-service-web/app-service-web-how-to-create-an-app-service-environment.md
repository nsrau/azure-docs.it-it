<properties 
	pageTitle="Come creare un ambiente del servizio app" 
	description="Descrizione del flusso di creazione per gli ambienti del servizio app" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="10/26/2015" 
	ms.author="ccompy"/>

# Come creare un ambiente del servizio app #

Gli Ambienti di servizio app (ASE) sono un'opzione di servizio Premium del servizio app di Azure che offre una funzionalità avanzata che non è disponibile negli indicatori di multi-tenant. In breve, la funzionalità Ambiente del servizio app distribuisce il servizio app di Azure nella rete virtuale del cliente. Per informazioni approfondite delle funzionalità offerte dagli ambienti del servizio app, vedere [Informazioni sull'ambiente del servizio app][WhatisASE].

### Panoramica ###

La creazione di un ambiente del servizio app richiede ai clienti di fornire le informazioni seguenti:

- nome dell'ambiente del servizio app
- sottoscrizione da usare per l'ambiente del servizio app  
- gruppo di risorse
- selezione della rete virtuale di Azure e di una subnet
- definizione del pool di risorse dell'ambiente del servizio app

Ognuno di questi elementi prevede dettagli importanti. - Il nome dell'ambiente del servizio app verrà usato nel sottodominio per tutte le app create in tale ambiente del servizio app - Tutte le app create in un ambiente del servizio app si troveranno nella stessa sottoscrizione in cui si trova l'ambiente del servizio app - Se non si ha accesso alla sottoscrizione usata per creare l'ambiente del servizio app, non sarà possibile usare l'ambiente per creare le app - Le reti virtuali usate per ospitare un ambiente del servizio app devono essere reti virtuali "v1" classiche regionali - La subnet usata per ospitare l'ambiente del servizio app non può contenere altre risorse di calcolo - In una subnet può essere presente un solo ambiente del servizio app

Ogni distribuzione dell'ambiente del servizio app è un servizio ospitato gestito e mantenuto da Azure. Le risorse di calcolo che ospitano i ruoli di sistema dell'ambiente del servizio app non sono accessibili al cliente anche se questi può gestire la quantità di istanze e le relative dimensioni.

È possibile accedere all'interfaccia utente di creazione dell'ambiente del servizio app in due modi. È possibile trovarla cercando ***Ambiente del servizio app*** in Azure Marketplace o facendo clic su Nuovo -> Web e dispositivi mobili.

Se si vuole che il gruppo di risorse della rete virtuale sia distinto da quello dell'ambiente del servizio app, è prima necessario creare separatamente la rete virtuale e quindi selezionarla durante la creazione dell'ambiente del servizio app. Se si vuole creare una subnet in una rete virtuale esistente durante la creazione dell'ambiente del servizio app, l'ambiente deve trovarsi nello stesso gruppo di risorse della rete virtuale.

### Creazione rapida ###
L'esperienza di creazione di un ambiente del servizio app prevede un set di impostazioni predefinite per consentire una procedura di creazione rapida. Per creare rapidamente un ambiente del servizio app è sufficiente immettere un nome per la distribuzione. In questo modo, l'ambiente del servizio app verrà creato nell'area più vicina e includerà gli elementi seguenti:

- rete virtuale con 512 indirizzi 
- subnet con 256 indirizzi
- Pool front-end con 2 risorse di calcolo P2
- Pool di lavoro con 2 risorse di calcolo P1
- singolo indirizzo IP da usare per IP SSL

Si tratta delle dimensioni minime per un ambiente del servizio app. I pool front-end richiedono risorse P2 o superiori. Assicurarsi di selezionare la sottoscrizione in cui si vuole includere l'ambiente del servizio app. Gli unici account che possono usare l'ambiente del servizio app per ospitare il contenuto, devono appartenere alla sottoscrizione usata per crearlo.

![][1]

Il nome specificato per l'ambiente del servizio app verrà usato per le app create nell'ambiente stesso. Se il nome dell'ambiente del servizio app è appsvcenvdemo, il nome di dominio sarà .*appsvcenvdemo.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Il nome dell'ambiente del servizio app non può contenere spazi. Anche se nel nome vengono usati caratteri maiuscoli, il nome di dominio corrisponderà alla versione in caratteri minuscoli del nome.

Le impostazioni predefinite sono molto utili nella maggior parte delle situazioni, ma in alcuni casi sarà necessario apportare alcune modifiche. Le sezioni successive descrivono in modo dettagliato le sezioni della procedura di configurazione relative all'ambiente del servizio app.

### Rete virtuale ###
Sebbene sia disponibile la funzionalità di creazione rapida che consente di creare automaticamente una nuova rete virtuale, è anche possibile selezionare una rete virtuale esistente o crearne una manualmente. È possibile selezionare una rete virtuale esistente (al momento sono supportate solo reti virtuali classiche “v1”) se questa è sufficientemente grande per supportare la distribuzione di un ambiente del servizio app. La rete virtuale deve includere 8 o più indirizzi.

Se si seleziona una rete virtuale esistente, sarà necessario specificare anche la subnet da usare o crearne una nuova. La subnet deve includere 8 o più indirizzi e non può contenere altre risorse già esistenti. La creazione dell'ambiente del servizio app non riuscirà se si usa una subnet a cui sono già allocate macchine virtuali.

Nell'interfaccia utente di creazione della rete virtuale, sarà necessario specificare:

- Nome della rete virtuale
- Intervallo di indirizzi della rete virtuale nella notazione CIDR
- Location

La località della rete virtuale corrisponde alla località dell'ambiente del servizio app, perché l'ambiente è distribuito in tale rete virtuale.

Dopo aver specificato o selezionato la rete virtuale, è necessario creare o selezionare la subnet. I dettagli da specificare in questa sezione sono: - Nome subnet - Intervallo di indirizzi della subnet nella notazione CIDR

Se non si ha familiarità con la notazione CIDR (Classless Inter-Domain Routing), questa assume il formato di un indirizzo IP separato da una barra da valore CIDR. L'aspetto sarà simile al seguente *10.0.0.0/22*. Il valore CIDR indica il numero di bit di apertura mascherati dall'indirizzo IP visualizzato. Per esprimere il concetto in modo più facile, i valori CIDR specificano un indirizzo IP. In questo esempio 10.0.0.0/22 indica un intervallo di 1024 indirizzi o dall'indirizzo 10.0.0.0 a 10.0.3.255. /23 indica 512 indirizzi e così via.

Se si vuole creare una subnet in una rete virtuale esistente, l'ambiente del servizio app si troverà nello stesso gruppo di risorse della rete virtuale. Per mantenere l'ambiente del servizio app in un gruppo di risorse distinto dalla rete virtuale è sufficiente creare la rete virtuale e la subnet separatamente e prima di creare l'ambiente dall'ambiente del servizio app.

![][2]


### Pool di risorse di calcolo ###

Durante la creazione dell'ambiente del servizio app è possibile impostare il numero di risorse e le relative dimensioni per ogni pool di risorse. È possibile impostare le dimensioni dei pool di risorse durante la creazione dell'ambiente del servizio app e modificarle in seguito con le opzioni di scalabilità automatica o manuale.

Come indicato in precedenza, un ambiente del servizio app è costituito da server front-end e ruoli di lavoro. I server front-end gestiscono il carico di connessione dell'app e i processi di lavoro eseguono il codice dell'app. I server front-end sono gestiti in un pool di risorse di calcolo dedicato. I processi di lavoro sono a loro volta gestiti in 3 pool di risorse di calcolo separati, denominati

- pool di lavoro 1
- pool di lavoro 2
- pool di lavoro 3

Se la richiesta per app Web semplici è elevata, è consigliabile aumentare il numero di server front-end e ridurre il numero di ruoli di lavoro. Se sono presenti app Web a utilizzo elevato di memoria o di CPU con un traffico leggero, non sono necessari molti server front-end ma un maggior numero di processi di lavoro o processi di lavoro con dimensioni più elevate.

![][3]

Indipendentemente dalle dimensioni delle risorse di calcolo, il footprint minimo prevede 2 server front-end e 2 processi di lavoro. Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end. Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per garantire la tolleranza di errore, è tuttavia necessario allocare una risorsa di calcolo aggiuntiva in base alle regole seguenti:

- per ogni pool di lavoro è necessaria almeno una risorsa di calcolo aggiuntiva a cui non è possibile assegnare un carico di lavoro
- quando la quantità di risorse di calcolo in un pool supera un determinato valore, è necessaria un'altra risorsa di calcolo

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore di X risorse assegnate a un pool di lavoro:

- se X è compreso tra 2 e 20, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-1
- se X è compreso tra 21 e 40, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-2
- se X è compreso tra 41 e 53, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-3

Oltre a poter gestire la quantità di risorse di calcolo che si possono assegnare a un determinato pool, è anche possibile gestire le dimensioni. Con gli ambienti del servizio app, è possibile scegliere tra 4 dimensioni diverse, denominate da P1 a P4. Per informazioni sulle dimensioni e sui relativi prezzi, vedere [Dettagli prezzi del servizio app][AppServicePricing]. Le dimensioni delle risorse di calcolo da P1 a P3 corrispondono a quelle disponibili negli ambienti multi-tenant. La risorsa di calcolo con dimensioni P4 offre 8 core con 14 GB di RAM ed è disponibile solo in un ambiente del servizio app.

I prezzi dell'ambiente del servizio app sono basati sulle risorse di calcolo assegnate. Si paga in base alle risorse di calcolo allocate all'ambiente del servizio app, indipendentemente dal fatto che queste ospitino carichi di lavoro o meno.

Per impostazione predefinita, un ambiente del servizio app include 1 indirizzo IP disponibile per le connessioni IP SSL. Se si ritiene che ne servano di più, è possibile specificarlo qui o dopo la creazione.
  
### Dopo la creazione dell'ambiente del servizio app ###

Dopo la creazione dell'ambiente del servizio app è possibile modificare:

- Quantità di server front-end (minimo: 2)
- Quantità di processi di lavoro (minimo: 2)
- Quantità di indirizzi IP disponibili per IP SSL
- Dimensioni delle risorse di calcolo usate dai server front-end o dai processi di lavoro (dimensioni minime per i front-end: P2)

Non è possibile modificare:

- Località
- Sottoscrizione
- Gruppo di risorse
- Rete virtuale usata
- Subnet usata

Per altre informazioni sulla scalabilità manuale, la gestione e il monitoraggio degli ambienti del servizio app, vedere [Come configurare un ambiente del servizio app][ASEConfig]

Per informazioni sulla scalabilità automatica, vedere la guida seguente: [Come configurare la scalabilità automatica per un ambiente del servizio app][ASEAutoscale]

Sono inoltre presenti altre dipendenze non disponibili per la personalizzazione, ad esempio il database e l'archiviazione. Questi sono gestiti da Azure e sono inclusi nel sistema. Il servizio di archiviazione del sistema supporta fino a 500 GB per l'intero ambiente del servizio app e il database viene rettificato da Azure in base alle esigenze, tramite il ridimensionamento del sistema.


## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][WhatisASE].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/

<!---HONumber=Nov15_HO1-->