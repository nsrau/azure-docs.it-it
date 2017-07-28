---
title: Creare un ambiente del servizio app di Azure esterno
description: Questo articolo illustra come creare un ambiente del servizio app di Azure durante la creazione di un'app o un'app autonoma
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>Creare un ambiente del servizio app esterno #

L'ambiente del servizio app (ASE) è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure (VNet). Per distribuire un ambiente del servizio app è possibile procedere in due modi:

- con un indirizzo VIP su un indirizzo IP esterno, spesso denominato _ambiente del servizio app esterno_.
- con l'indirizzo VIP su un indirizzo IP interno, spesso denominato _ambiente del servizio app ILB_ perché l'endpoint interno è un servizio di bilanciamento del carico interno (ILB).

In questo articolo viene illustrato come creare un ambiente del servizio app esterno. Per una panoramica sull'ambiente del servizio app è possibile iniziare con [un'introduzione all'ambiente del servizio App][Intro]. Per informazioni dettagliate sulla creazione di un ambiente del servizio app ILB, vedere [Create and use an ILB ASE][MakeILBASE] (Creare e usare un ambiente del servizio app ILB).

## <a name="before-you-create-your-ase"></a>Prima di creare l'ambiente del servizio app ##

È importante essere a conoscenza degli elementi che non è possibile modificare dopo la creazione dell'ambiente del servizio app, ovvero:

- Località
- Sottoscrizione
- Gruppo di risorse
- Rete virtuale usata
- Subnet usata
- Dimensioni della subnet

> [!NOTE]
> Quando si sceglie una rete virtuale e si specifica una subnet, assicurarsi che le dimensioni siano sufficienti per supportare la crescita futura. Le dimensioni consigliate sono `/25` con 128 indirizzi.
>

## <a name="three-ways-to-create-an-ase"></a>Tre modi per creare un ambiente del servizio app ##

Sono disponibili tre modi per creare un ambiente del servizio app. È possibile creare un ambiente del servizio app:

- durante la creazione di un piano di servizio app, che consente di creare l'ambiente del servizio app e il piano di servizio app in un unico passaggio.
- Dall'interfaccia utente di creazione dell'ambiente del servizio app, che crea un ambiente del servizio app vuoto. Si tratta di un'interfaccia utente di creazione dell'ambiente del servizio app più avanzata, cui si accede per creare un ambiente del servizio app con un servizio di bilanciamento di carico interno (ILB).
- Da un modello di Resource Manager. Questa procedura è destinata agli utenti Advanced ed è descritta in [Create an ASE from a template][MakeASEfromTemplate] (Creare un ambiente del servizio app da un modello).

Un ambiente del servizio app creato senza un bilanciamento del carico interno possiede un indirizzo VIP pubblico. Ciò significa che tutto il traffico HTTP per le applicazioni dell'ambiente del servizio app accederà a un indirizzo IP accessibile da Internet. Un ambiente del servizio app con ILB ha un endpoint su un indirizzo IP di rete virtuale. Tali app non sono esposte direttamente a Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Creare contemporaneamente un ambiente del servizio app e un piano di servizio app ##

Il piano di servizio app è un contenitore di applicazioni. Quando si crea un'applicazione nel servizio app, è sempre necessario scegliere o creare un piano di servizio app. Il modello di contenitore è: gli ambienti contengono i piani di servizio app e i piani di servizio app contengono le app.

Per creare un ambiente del servizio app durante la creazione del piano di servizio app:

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuovo &gt; Web e dispositivi mobili &gt; App Web**

    ![][1]
1. Selezionare la propria sottoscrizione. Se sono presenti più sottoscrizioni, ricordare che per creare un'app nell'ambiente del servizio app è necessario usare la stessa sottoscrizione usata per la creazione dell'ambiente.
1. Selezionare o creare un gruppo di risorse. I *gruppi di risorse* consentono di gestire risorse di Azure correlate come un'unità e sono utili per stabilire le regole di *controllo degli accessi in base al ruolo*  per le app. Per altre informazioni, vedere [Panoramica di Azure Resource Manager][ARMOverview].
1. Fare clic sul piano di servizio app, quindi selezionare **Crea nuovo**.

    ![][2]
1. Nell'elenco a discesa **Posizione**, selezionare l'area in cui si desidera creare l'ambiente del servizio app. Se si seleziona un ambiente del servizio app esistente, non sarà possibile abilitare la creazione di un nuovo ambiente del servizio app ma verrà creato semplicemente il piano di servizio app nell'ambiente del servizio app selezionato.
1. Fare clic sull'interfaccia utente **Piano tariffario** e selezionare un codice di riferimento del prodotto **Isolato**. Se si seleziona una scheda SKU **Isolata** e una posizione diversa da un ambiente del servizio app, significa che si desidera creare un nuovo ambiente del servizio app in tale posizione. In questo modo si rivela l'interfaccia utente di creazione dell'ambiente del servizio app dopo aver fatto clic su **Seleziona** nella pagina relativa ai prezzi. Il codice di riferimento del prodotto **Isolato** è disponibile solo in combinazione con un ambiente del servizio app. Inoltre, non è possibile usare qualsiasi altro codice di riferimento del prodotto per i prezzi in un ambiente del servizio app diverso da **Isolato**.

    ![][3]
1. Immettere il nome per l'ambiente del servizio app. Il nome dell'ambiente del servizio app viene usato nel nome indirizzabile per le app. Se il nome dell'ambiente del servizio app è _appsvcenvdemo_, il nome del sottodominio sarà *.appsvcenvdemo.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Il nome dell'ambiente del servizio app non può contenere spazi. Anche se nel nome vengono usati caratteri maiuscoli, il nome di dominio corrisponderà alla versione in caratteri minuscoli del nome.

    ![][4]
1. Scegliere **Crea nuovo** o **Seleziona esistente**. L'opzione per selezionare una rete virtuale esistente è disponibile solo se si dispone di una rete virtuale nell'area selezionata. Se si seleziona **Crea nuovo**, si specifica un nome per la rete virtuale e viene creata una nuova rete virtuale di Gestione risorse con tale nome, con lo spazio degli indirizzi `192.168.250.0/23` nell'area selezionata. Se si sceglie **Seleziona esistente**, è necessario:
    1. Selezionare il blocco di indirizzi della rete virtuale, se ne è disponibile più di uno.
    2. Specificare un nuovo nome di subnet.
    3. Selezionare la dimensione della subnet. **Promemoria: deve essere un valore sufficientemente grande per supportare la crescita futura dell'ambiente del servizio app.** La dimensione consigliata è `/25`, contenente 128 indirizzi, in grado di gestire un ambiente del servizio app con dimensione massima. Non è consigliato, ad esempio, `/28`, in quanto sono disponibili solo 16 indirizzi. L'infrastruttura utilizzerà almeno 5 indirizzi, lasciando solo un massimo di ridimensionamento di 11 istanze al massimo in una subnet `/28`.
    4. Selezionare l'intervallo IP della subnet.

Il processo di creazione dell'ambiente del servizio app inizierà dopo aver selezionato **Crea**. Verrà inoltre creato il piano di servizio app e l'applicazione. L'ambiente del servizio app, il piano di servizio app e l'applicazione saranno tutti inclusi nella stessa sottoscrizione e nello stesso gruppo di risorse. Se è necessario che l'ambiente del servizio app rientri in un gruppo di risorse distinto dal piano di servizio app e dall'applicazione o se è necessario un ambiente del servizio app ILB, ricorrere alla creazione dell'ambiente del servizio app autonomo.

## <a name="create-an-ase-by-itself"></a>Creare un ambiente del servizio app autonomo ##

Seguendo il flusso di creazione dell'ambiente del servizio app autonomo verrà creato un ambiente del servizio app vuoto. Un ambiente del servizio app vuoto continuerà a dover pagare una quota mensile per l'infrastruttura. È consigliabile passare attraverso questo flusso di lavoro per creare un ambiente del servizio app con un bilanciamento del carico interno o per creare un ambiente del servizio app nel proprio gruppo di risorse. Dopo aver creato l'ambiente del servizio app, è possibile creare app nell'ambiente del servizio app usando le normali procedure di creazione di applicazioni e selezionando il nuovo ambiente del servizio app come posizione.

Accedere all'interfaccia utente di creazione dell'ambiente del servizio app cercando ***Ambiente del servizio app*** in Azure Marketplace oppure scegliendo Nuovo -&gt; Web e dispositivi mobili -&gt; Ambiente del servizio app. Per creare un ambiente del servizio app tramite la creazione autonoma:

1. Specificare il nome dell'ambiente del servizio app. Il nome specificato per l'ambiente del servizio app verrà usato per le app create nell'ambiente stesso. Se il nome dell'ambiente del servizio app è *mynewdemoase*, il nome del sottodominio sarà *.mynewdemoase.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo *mytestapp.mynewdemoase.p.azurewebsites.net*. Il nome dell'ambiente del servizio app non può contenere spazi. Anche se nel nome vengono usati caratteri maiuscoli, il nome di dominio corrisponderà alla versione in caratteri minuscoli del nome. Se si usa un servizio di bilanciamento del carico interno, il nome dell'ambiente del servizio app non viene usato nel sottodominio ma viene dichiarato in modo esplicito durante la creazione dell'ambiente.

    ![][5]
1.  Selezionare la propria sottoscrizione. La sottoscrizione usata per l'ambiente del servizio app corrisponde a quella con cui verranno create tutte le app in tale ambiente. Non è possibile posizionare l'ambiente del servizio app in una rete virtuale che si trova in un'altra sottoscrizione.
1.  Selezionare o specificare un nuovo gruppo di risorse. Il gruppo di risorse per l'ambiente del servizio app deve essere uguale a quello usato per la rete virtuale. Se si seleziona una rete virtuale già esistente , la selezione del gruppo di risorse per l'ambiente del servizio app verrà aggiornata per riflettere il valore della rete virtuale.

    ![][6]
1. Selezionare la rete virtuale e la località. È possibile scegliere di creare una nuova rete virtuale o selezionare una rete virtuale esistente. Se si seleziona una nuova rete virtuale, è possibile specificare un nome e una località. Alla nuova rete virtuale verrà assegnato l'intervallo di indirizzi 192.168.250.0/23 e una subnet denominata **predefinita**, definita come 192.168.250.0/24. È possibile selezionare solo una rete virtuale di Resource Manager. La selezione del **Tipo di indirizzo VIP** indica se è possibile accedere all'ambiente del servizio app direttamente da Internet (Esterno) o se si usa un Servizio di bilanciamento del carico interno. Per altre informazioni, vedere [Creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][MakeILBASE]. Se si seleziona un indirizzo VIP di tipo **Esterno**, è possibile selezionare il numero di indirizzi IP esterni con cui viene creato il sistema per finalità IPSSL. Se si seleziona **Interno**, è necessario specificare il sottodominio che verrà usato dall'ambiente del servizio app. Gli ambienti del servizio app possono essere distribuiti nelle reti virtuali che usano *gli* intervalli di indirizzi pubblici *o* spazi di indirizzi RFC1918, ovvero indirizzi privati. Per usare una rete virtuale con un intervallo di indirizzi pubblici, è necessario creare in anticipo la rete virtuale. Quando si seleziona una rete virtuale già esistente, è necessario creare una nuova subnet durante la creazione dell'ambiente del servizio app. **Non è possibile usare una subnet creata in precedenza nel portale. È possibile creare un ambiente del servizio app con una subnet già esistente se si crea l'ambiente del servizio app usando un modello di Resource Manager.** Per creare un ASE da un modello, vedere [Create an App Service Environment from template][MakeASEfromTemplate] (Creare un ambiente del servizio app da un modello)

## <a name="app-service-environment-v1"></a>Ambiente del servizio app 1 ##

È comunque possibile creare istanze della prima versione della funzione ambiente del servizio app (ASEv1). Per eseguire tale operazione cercare **Ambiente del servizio app 1** in Marketplace. L'operazione di creazione è uguale all'operazione di creazione di un ambiente del servizio app autonomo. Al termine, verrà creato l'ambiente del servizio app 1 con due front-end e due ruoli di lavoro. Con l'ambiente del servizio app 1 è necessario gestire i front-end e i ruoli di lavoro. Questi non vengono aggiunti automaticamente quando si creano i piani di servizio app. Le risorse front-end fungono da endpoint HTTP/HTTPS e inviano il traffico ai ruoli di lavoro, ovvero i ruoli che ospitano le applicazioni. È possibile regolare la quantità dopo la creazione dell'ambiente del servizio app. Per altre informazioni sull'ambiente del servizio app 1, vedere [Introduction to the App Service Environment v1][ASEv1Intro] (Introduzione all'ambiente del servizio app 1). Per altri dettagli sul ridimensionamento, la gestione e il monitoraggio di un ambiente del servizio app 1, vedere [Configurazione di un ambiente del servizio app][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

