<properties
	pageTitle="Come creare un'app Web in un ambiente del servizio app"
	description="Esame del flusso di creazione per app Web e piani di servizio app per un ambiente del servizio app"
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
	ms.topic="get-started-article" 
	ms.date="04/27/2015"
	ms.author="ccompy"/>

# Come creare un'app Web in un ambiente del servizio app #

La procedura di creazione di app Web è quasi simile a quella normalmente usata in un ambiente del servizio app. Se non si ha familiarità con la funzionalità dell'ambiente del servizio app, leggere il documento [Informazioni sull'ambiente del servizio app](app-service-app-service-environment-intro.md).

Per creare un'app Web in un ambiente del servizio app, è necessario verificare innanzitutto di disporre di un ambiente del servizio app. Per informazioni dettagliate sulla creazione di un ambiente del servizio app, leggere il documento [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

Il primo passaggio per la creazione di un'app Web consiste nel creare o selezionare un piano di servizio app. Per creare un piano di servizio app in un ambiente del servizio app, le operazioni iniziali sono quelle solite per il flusso di creazione delle app Web, ovvero selezionare Nuovo -> Web + Mobile -> App Web.

![][1]


Se si usa un piano di servizio app già creato nell'ambiente del servizio app, selezionare il piano, immettere il nome dell'app Web e selezionare Crea. Il flusso è identico a quello della normale creazione di un'app Web. La principale differenza consiste nel fatto che l'app Web sarà accessibile all'indirizzo

[*nomesito*].[*nome dell'ambiente del servizio app*].p.azurewebsites.net

invece dell'indirizzo

[*nomesito*].azurewebsites.net

Per il momento il nome dell'app Web deve essere univoco in tutto il servizio app di Azure. Questo significa che se si vuole creare un'app Web denominata "miaappweb", al momento non possono esistere altre app Web denominate nello stesso modo nel servizio app di Azure.

### Piani di servizio app ###

I piani di servizio app sono costituiti da un set gestito di app Web. Quando si seleziona il prezzo, l'importo addebitato viene applicato al piano di servizio app invece che alle singole app. Per aumentare il numero di istanze di un'app Web, aumentare le istanze del piano di servizio app in quanto tale operazione influisce su tutte le app Web incluse nel piano. Il piano può prevedere restrizioni relative alla quantità per alcune funzionalità, come gli slot di sito o l'integrazione della rete virtuale. Per altre informazioni sui piani di servizio app, leggere il documento [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

La procedura di creazione di un nuovo piano di servizio app è leggermente diversa rispetto a quella di creazione in un ambiente del servizio app. Tra l'altro, le opzioni disponibili per i processi di lavoro sono diverse perché in un ambiente del servizio app non esistono processi di lavoro condivisi. I processi di lavoro utilizzabili sono quelli allocati all'ambiente del servizio app dall'amministratore. Questo significa che per creare un nuovo piano di servizio app, è necessario allocare all'ambiente del servizio app un numero di processi di lavoro maggiore del numero totale di istanze in tutti i piani di servizio app dell'ambiente del servizio app. Se nell'ambiente del servizio app non esistono processi di lavoro sufficienti per creare il piano di servizio app, è necessario chiedere all'amministratore dell'ambiente di aggiungerli.

Un'altra differenza rispetto ai piani di servizio app ospitati nell'ambiente del servizio app è data dall'impossibilità di scegliere il prezzo. Con un ambiente del servizio app si paga infatti per le risorse di calcolo usate dal sistema e non viene addebito nulla per i piani di servizio app presenti in tale ambiente. In genere, quando si crea un piano di servizio app, è necessario selezionare un piano tariffario che determina la fatturazione. Un ambiente del servizio app è essenzialmente una posizione privata in cui è possibile creare contenuti. Si paga per l'ambiente e non per ospitare il contenuto.

### Selezione dell'ambiente del servizio app ###

Dal momento che un ambiente del servizio app è essenzialmente una posizione di distribuzione privata, per iniziare selezionare l'ambiente da usare nell'elenco di selezione della posizione.

![][2]

Dopo la selezione nell'interfaccia utente l'elenco di selezione del piano tariffario verrà sostituito da un elenco di selezione del pool di lavoro. Nel campo relativo alla posizione è indicato il nome del sistema dell'ambiente del servizio app e l'area geografica in cui si trova. Sotto il campo l'URL nel nome di dominio dell'ambiente del servizio app la parte di indirizzo .azurewebsites.net, in genere presente, viene sostituita con il nome dell'ambiente del servizio app.

![][3]

### Selezione del pool di lavoro ###

Quando si seleziona un piano tariffario dedicato, nel servizio app di Azure e all'esterno di un ambiente del servizio app sono in genere disponibili tre dimensioni. Analogamente, i clienti che dispongono di un ambiente del servizio app possono definire fino a tre pool di lavoro e specificare la dimensione della macchina virtuale che viene usata per il pool di lavoro. Invece di selezionare un piano tariffario per il piano di servizio app, selezionare un cosiddetto pool di lavoro.

Nell'interfaccia utente di selezione del pool di lavoro sotto il nome del pool sono indicate le dimensioni della macchina virtuale usate per il pool. La quantità disponibile si riferisce al numero di macchine virtuali disponibili per l'uso nel pool. In totale nel pool possono esistere più macchine virtuali rispetto a tale numero, ma questo valore si riferisce semplicemente al numero di quelle non in uso. Se è necessario adeguare l'ambiente del servizio app e aggiungere altre risorse di calcolo, vedere [Configurazione dell'ambiente del servizio app](app-service-web-configure-an-app-service-environment.md).

![][4]

In questo esempio sono disponibili solo due pool di lavoro, in quanto l'amministratore dell'ambiente del servizio app ha allocato macchine virtuali solo in questi due pool. Allocando macchine virtuali a un terzo pool, i pool di lavoro visualizzati saranno tre.

### Dopo la creazione dell'app Web ###

Per eseguire app Web e gestire piani di servizio app in un ambiente del servizio app, è opportuno tenere in considerazione alcuni aspetti.

Come notato in precedenza, il proprietario dell'ambiente del servizio app è responsabile delle dimensioni del sistema, di conseguenza spetta al proprietario garantire una capacità sufficiente per ospitare i piani di servizio app desiderati. Se non sono disponibili processi di lavoro, non sarà possibile creare il piano di servizio app. Questa considerazione si applica anche all'aumento delle risorse per l'app Web. Se sono necessarie altre istanze, è necessario chiedere all'amministratore dell'ambiente del servizio app di aggiungere altri processi di lavoro.

Dopo aver creato l'app Web e il piano di servizio app, è consigliabile aumentare le risorse. Per garantire la tolleranza di errore per le app, in un ambiente del servizio app devono essere sempre presenti almeno due istanze del piano di servizio app. La procedura di ridimensionamento di un piano di servizio app in un ambiente del servizio app è identica a quella normale eseguibile tramite l'interfaccia utente del piano di servizio app. Per informazioni dettagliate sul ridimensionamento, leggere il documento [Come ridimensionare un'app Web in un ambiente del servizio app](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=August15_HO6-->