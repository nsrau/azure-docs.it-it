<properties
	pageTitle="Come creare un'app Web in un ambiente del servizio app"
	description="Esame del flusso di creazione per app Web e piani di servizio app per un ambiente del servizio app"
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
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# Come creare un'app Web in un ambiente del servizio app #

La procedura di creazione di app Web è quasi simile a quella normalmente usata in un ambiente del servizio app. Se non si ha familiarità con la funzionalità dell'ambiente del servizio app, leggere il documento [Informazioni sull'ambiente del servizio app](app-service-app-service-environment-intro.md).

Per creare un'app Web in un ambiente del servizio app, è necessario verificare innanzitutto di disporre di un ambiente del servizio app. Per informazioni dettagliate sulla creazione di un ambiente del servizio app, leggere il documento [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

Come primo passaggio per la creazione di un'app Web, è necessario selezionare la sottoscrizione. Se si hanno più sottoscrizioni, tenere presente che per creare un'app nell'ambiente del servizio app è necessario usare la stessa sottoscrizione usata per la creazione dell'ambiente del servizio app. Nei passaggi successivi verrà selezionato o creato un gruppo di risorse. Se non si ha familiarità con i gruppi di risorse, vedere l'articolo relativo alla [gestione delle risorse di Azure][ResourceGroups]. Oltre a semplificare la gestione delle risorse, i gruppi di risorse sono importanti per stabilire le regole di controllo degli accessi in base al ruolo per le app.

Dopo aver selezionato la sottoscrizione e il gruppo di risorse, è quindi necessario creare o selezionare un piano di servizio app. Se occorre creare un nuovo piano di servizio app nell'ambiente del servizio app, è necessario specificare un nome per il piano di servizio app, selezionare l'ambiente del servizio app in Località e quindi selezionare il pool di lavoro in cui includere il piano di servizio app. Questa procedura verrà descritta in dettaglio più avanti. Se si seleziona un piano di servizio app in un ambiente del servizio app, il flusso è identico a quello della normale creazione di un'app Web. Per avviare il flusso di creazione, selezionare Nuovo -> Web e dispositivi mobili -> App Web.

![][1]


Se si usa un piano di servizio app già creato nell'ambiente del servizio app, è sufficiente selezionare il piano, immettere il nome dell'app Web e selezionare Crea. Il flusso è identico a quello della normale creazione di un'app Web. È possibile identificare i piani di servizio app nell'ambiente del servizio app cercando la località indicata sotto il nome del piano di servizio app.

![][5]

Quando si crea un'app, questa è raggiungibile all'indirizzo seguente:

[*nomesito*].[*nome dell'ambiente del servizio app*].p.azurewebsites.net

invece dell'indirizzo

[*nomesito*].azurewebsites.net

Per il momento il nome dell'app Web deve essere univoco in tutto il servizio app di Azure. Questo significa che se si vuole creare un'app Web denominata "miaappweb", al momento non possono esistere altre app Web denominate nello stesso modo nel servizio app di Azure.

### Piani di servizio app ###

I piani di servizio app sono costituiti da un set gestito di app Web. Quando si seleziona il prezzo, l'importo addebitato viene applicato al piano di servizio app invece che alle singole app. Per aumentare il numero di istanze di un'app Web, aumentare le istanze del piano di servizio app in quanto tale operazione influisce su tutte le app Web incluse nel piano. Il piano può prevedere restrizioni relative alla quantità per alcune funzionalità, come gli slot di sito o l'integrazione della rete virtuale. Per altre informazioni sui piani di servizio app, leggere il documento [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

La procedura di creazione di un nuovo piano di servizio app è leggermente diversa rispetto a quella di creazione in un ambiente del servizio app. Tra l'altro, le opzioni disponibili per i ruoli di lavoro sono diverse perché in un ambiente del servizio app non esistono ruoli di lavoro condivisi. I ruoli di lavoro utilizzabili sono quelli allocati all'ambiente del servizio app dall'amministratore. Questo significa che per creare un nuovo piano di servizio app, è necessario allocare al pool di lavoro dell'ambiente del servizio app un numero di ruoli di lavoro maggiore del numero totale di istanze in tutti i piani di servizio app già inclusi in quel pool di lavoro. Se nel pool di lavoro dell'ambiente del servizio app non esistono ruoli di lavoro sufficienti per creare il piano di servizio app, è necessario chiedere all'amministratore dell'ambiente di aggiungerli.

Un'altra differenza rispetto ai piani di servizio app ospitati nell'ambiente del servizio app è data dall'impossibilità di scegliere il prezzo. Con un ambiente del servizio app si paga infatti per le risorse di calcolo usate dal sistema e non viene addebito nulla per i piani di servizio app presenti in tale ambiente. In genere, quando si crea un piano di servizio app, è necessario selezionare un piano tariffario che determina la fatturazione. Un ambiente del servizio app è essenzialmente una posizione privata in cui è possibile creare contenuti. Si paga per l'ambiente e non per ospitare il contenuto.

### Creazione di un piano di servizio app per l'ambiente del servizio app ###

Per creare un piano di servizio app in un ambiente del servizio app, selezionare Crea nuovo nell'interfaccia utente per la selezione del piano di servizio app e specificare un nome per il piano, come si farebbe con un normale piano di servizio non incluso in un ambiente del servizio app. Selezionare quindi l'ambiente del servizio app da usare dall'elenco di selezione delle località. Poiché un ambiente del servizio app è essenzialmente una località di distribuzione privata, è visualizzato in Località.

![][2]

Dopo aver selezionato l'ambiente del servizio app nell'elenco di selezione delle località, l'interfaccia utente per la creazione del piano di servizio app verrà aggiornata. Nella località verrà visualizzato il nome del sistema dell'ambiente del servizio app, l'area in cui si trova e l'elenco di selezione del piano tariffario viene sostituito dall'elenco di selezione del pool di lavoro.

![][3]

### Selezione del pool di lavoro ###

Quando si seleziona un piano tariffario dedicato, nel servizio app di Azure e all'esterno di un ambiente del servizio app sono in genere disponibili tre dimensioni di calcolo. Analogamente, i clienti che dispongono di un ambiente del servizio app possono definire fino a tre pool di lavoro e specificare le dimensioni di calcolo usate per il pool di lavoro. Per i tenant questo significa che invece di selezionare un piano tariffario con le dimensioni di calcolo per il piano di servizio app, si seleziona un pool di lavoro.

Nell'interfaccia utente di selezione del pool di lavoro sotto il nome del pool sono indicate le dimensioni di calcolo usate per il pool. La quantità disponibile si riferisce al numero di istanze di calcolo disponibili per l'uso nel pool. In totale nel pool possono esistere più istanze rispetto a tale numero, ma questo valore si riferisce semplicemente al numero di quelle non in uso. Se è necessario adeguare l'ambiente del servizio app e aggiungere altre risorse di calcolo, vedere [Configurazione dell'ambiente del servizio app](app-service-web-configure-an-app-service-environment.md).

![][4]

In questo esempio sono disponibili solo due pool di lavoro, in quanto l'amministratore dell'ambiente del servizio app ha allocato host solo in questi due pool di lavoro. Allocando macchine virtuali a un terzo pool, i pool di lavoro visualizzati saranno tre.

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
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=Oct15_HO4-->