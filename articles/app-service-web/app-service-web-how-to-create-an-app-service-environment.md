<properties 
	pageTitle="Come creare un ambiente del servizio app" 
	description="Descrizione del flusso di creazione per gli ambienti del servizio app" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Come creare un ambiente del servizio app #

Gli ambienti del servizio app sono un'opzione del servizio Premium del servizio app di Azure attualmente in anteprima. Offre una funzionalità di configurazione migliorata non disponibile negli stamp multi-tenant. Per informazioni approfondite delle funzionalità offerte dagli ambienti del servizio app, vedere [Informazioni sull'ambiente del servizio app][WhatisASE].

### Panoramica ###

In breve, la funzionalità Ambiente del servizio app distribuisce il servizio app di Azure nella rete virtuale del cliente. A tale scopo, il cliente deve disporre di quanto segue:

- Una rete virtuale regionale con oltre 512 (/23) o più indirizzi
- Una subnet all'interno della rete virtuale con oltre 256 (24) più indirizzi
- La subnet**non deve contenere altre risorse di calcolo**. In una subnet può essere distribuito un solo ambiente di servizi app. Il tentativo di creazione non riesce se sono presenti altre risorse di calcolo che si trovano già nella subnet.

Se non si ha già una rete virtuale da usare per ospitare l'ambiente del servizio app, è possibile crearne una al momento della creazione dell'ambiente del servizio app.

Ogni distribuzione dell'ambiente del servizio app è un servizio ospitato gestito e mantenuto da Azure. Le risorse di calcolo che ospitano i ruoli di sistema dell'ambiente del servizio app non sono accessibili al cliente anche se questi può gestire la quantità di istanze e le relative dimensioni.

## Creazione dell'ambiente del servizio app ##

È possibile accedere all'interfaccia utente di creazione dell'ambiente del servizio app in due modi. È possibile trovarla cercando ***Ambiente del servizio app*** in Azure Marketplace o facendo clic su Nuovo -> Web e dispositivi mobili.

### Creazione rapida ###
Per creare un ambiente del servizio app nell'interfaccia utente di creazione, immettere un nome per la distribuzione. Verrà quindi creata una rete virtuale con 512 indirizzi, una subnet con 256 indirizzi all'interno della rete virtuale e un ambiente del servizio app con 2 server front-end e 2 processi di lavoro nel pool di lavoro 1. Assicurarsi di selezionare la località in cui deve essere creato il sistema e la sottoscrizione in cui si vuole includere l'ambiente. Gli unici account che possono usare l'ambiente del servizio app per ospitare il contenuto, devono appartenere alla sottoscrizione usata per crearlo.

Il nome specificato per l'ambiente del servizio app verrà usato per le app Web create nell'ambiente stesso. Se il nome del servizio app è appsvcenvdemo, il nome di dominio sarà .*appsvcenvdemo.p.azurewebsites.net*. Se è stata creata un'app Web denominata mytestapp, questa sarà disponibile all'indirizzo *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Il nome non può contenere spazi. Anche se nel nome vengono usati caratteri maiuscoli, il nome di dominio corrisponderà alla versione in caratteri minuscoli del nome.


![][1]

### Pool di risorse di calcolo ###

Le risorse di calcolo usate per l'ambiente del servizio app sono gestite in pool di risorse di calcolo per consentire la configurazione del numero di istanze di calcolo disponibili nel pool e le relative dimensioni. Un ambiente del servizio app è costituito da server front-end e processi di lavoro. I server front-end gestiscono il carico di connessione dell'app e i processi di lavoro eseguono il codice dell'app. I server front-end sono gestiti in un pool di risorse di calcolo dedicato. I processi di lavoro sono a loro volta gestiti in 3 pool di risorse di calcolo separati, denominati

- pool di lavoro 1
- pool di lavoro 2
- pool di lavoro 3

Se la richiesta per app Web semplici è elevata, è consigliabile aumentare il numero di server front-end e ridurre il numero di processi di lavoro. Se sono presenti app Web a utilizzo elevato di memoria o di CPU con un traffico leggero, non sono necessari molti server front-end ma un maggior numero di processi di lavoro o processi di lavoro con dimensioni più elevate.

Indipendentemente dalle dimensioni delle risorse di calcolo, il footprint minimo prevede 2 server front-end e 2 processi di lavoro. Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end. Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per fornire la tolleranza di errore, è tuttavia necessario allocare una risorsa di calcolo aggiuntiva in base alle regole seguenti:

- per ogni pool di lavoro è necessaria almeno una risorsa di calcolo aggiuntiva a cui non è possibile assegnare un carico di lavoro
- quando la quantità di risorse di calcolo in un pool supera un determinato valore, è necessaria un'altra risorsa di calcolo

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore di X risorse assegnate a un pool di lavoro:

- se X è compreso tra 2 e 20, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-1
- se X è compreso tra 21 e 40, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-2
- se X è compreso tra 41 e 53, la quantità di risorse di calcolo che si può usare per i carichi di lavoro è pari a X-3

Oltre a poter gestire la quantità di risorse di calcolo che si possono assegnare a un determinato pool, è anche possibile gestire le dimensioni. Con gli ambienti del servizio app, è possibile scegliere tra 4 dimensioni diverse, denominate da P1 a P4. Per informazioni sulle dimensioni e sui relativi prezzi, vedere [Dettagli prezzi del servizio app][AppServicePricing]. Le dimensioni delle risorse di calcolo da P1 a P3 corrispondono a quelle disponibili negli ambienti multi-tenant. La risorsa di calcolo con dimensioni P4 offre 8 core con 14 GB di RAM ed è disponibile solo in un ambiente del servizio app.

I prezzi dell'ambiente del servizio app sono basati sulle risorse di calcolo assegnate. Si paga in base alle risorse di calcolo allocate all'ambiente del servizio app, indipendentemente dal fatto che queste ospitino carichi di lavoro o meno.



### Creazione della rete virtuale ###
Sebbene sia disponibile la funzionalità di creazione rapida che consente di creare automaticamente una nuova rete virtuale, è anche possibile selezionare una rete virtuale esistente o crearne una manualmente. È possibile selezionare una rete virtuale esistente se questa è sufficientemente grande per supportare la distribuzione di un ambiente del servizio app. La rete virtuale deve avere 512 o più indirizzi. Se si seleziona una rete virtuale esistente, sarà necessario specificare anche la subnet da usare o crearne una nuova. La subnet deve avere 256 o più indirizzi.

Nell'interfaccia utente di creazione della rete virtuale, sarà necessario specificare:

- Nome della rete virtuale
- Intervallo di indirizzi della rete virtuale nella notazione CIDR
- Nome della subnet
- Intervallo di indirizzi della subnet nella notazione CIDR

La notazione CIDR accetta il formato 10.0.0.0/22, dove /22 specifica l'intervallo. In questo esempio, /22 indica un intervallo di 1024 indirizzi o da 10.0.0.0 a 10.0.3.255. /23 indica 512 indirizzi e così via.

![][2]

### Definizione delle dimensioni dell'ambiente del servizio app ###

Successivamente, sarà necessario configurare la scala del sistema. Per impostazione predefinita, sono presenti 2 risorse di calcolo P2 front-end, 2 processi di lavoro P1 e 1 indirizzo IP. Sono presenti 2 server front-end per fornire la disponibilità elevata e distribuire il carico. La dimensione minima per i server front-end è P2, per garantire una capacità sufficiente per supportare un sistema di dimensioni modeste. Se il sistema dovrà supportare un numero elevato di richieste, è possibile regolare la quantità di front-end e le dimensioni del server.

Come indicato in precedenza, all'interno di un ambiente del servizio app sono presenti 3 pool di lavoro che possono essere definiti dal cliente. Le dimensioni della risorsa di calcolo possono essere comprese tra P1 e P4. Per impostazione predefinita, sono presenti solo 2 processi di lavoro P1 configurati nel pool di lavoro 1. Questa configurazione è sufficiente per supportare un singolo piano di servizio app con 1 istanza.

I dispositivi di scorrimento vengono regolati automaticamente per riflettere la capacità di calcolo totale disponibile nell'ambiente del servizio app. Una volta regolati i dispositivi di scorrimento all'interno di un pool, gli altri dispositivi di scorrimento cambiano per riflettere la quantità di risorse di calcolo rimanenti prima di raggiungere il limite di 55.
 
![][3]

L'aggiunta di nuove istanze non è un'operazione veloce. Se si prevede che siano necessarie ulteriori risorse di calcolo, è necessario eseguirne il provisioning con largo anticipo. L'operazione di provisioning può richiedere più ore, a seconda del numero di istanze aggiunte al sistema. Tenere presente che per garantire che il sistema soddisfi i requisiti di tolleranza di errore, ogni ambiente del servizio app deve avere un'istanza di riserva in ogni pool di lavoro.

Per impostazione predefinita, un ambiente del servizio app include 1 indirizzo IP disponibile per le connessioni IP SSL. Se si ritiene che ne servano di più, è possibile specificarlo qui o dopo la creazione.
  
### Dopo la creazione dell'ambiente del servizio app ###

Dopo la creazione dell'ambiente del servizio app è possibile modificare:

- Quantità di server front-end (minimo: 2)
- Quantità di processi di lavoro (minimo: 2)
- Quantità di indirizzi IP
- Dimensioni delle risorse di calcolo usate dai server front-end o dai processi di lavoro (dimensioni minime per i front-end: P2)

Non è possibile modificare:

- Località
- Sottoscrizione
- Gruppo di risorse
- Rete virtuale usata
- Subnet usata

Per informazioni dettagliate sulla gestione e il monitoraggio degli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][ASEConfig].

Sono inoltre presenti altre dipendenze non disponibili per la personalizzazione, ad esempio il database e l'archiviazione. Questi sono gestiti da Azure e sono inclusi nel sistema. L'archiviazione di sistema supporta un massimo di 500 GB per l'intero ambiente del servizio app.


## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][WhatisASE].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/

<!---HONumber=July15_HO4-->