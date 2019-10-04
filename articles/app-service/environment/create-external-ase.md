---
title: Creare un ambiente del servizio app esterno - Azure
description: Questo articolo illustra come creare un ambiente del servizio app durante la creazione di un'app o un'app autonoma
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 19d58ed90de4bdbd3cd7606d15c115bb1633770a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069701"
---
# <a name="create-an-external-app-service-environment"></a>Creare un ambiente del servizio app esterno

L'ambiente del servizio app di Azure è una distribuzione di Servizio app di Azure in una subnet in una rete virtuale di Azure.

> [!NOTE]
> Ogni ambiente del servizio app dispone di un indirizzo IP virtuale (VIP), che può essere usato per contattare l'ambiente del servizio app.

È possibile distribuire un ambiente del servizio app in due modi:

- Con un indirizzo VIP su un indirizzo IP esterno, spesso denominato ambiente del servizio app esterno.
- Con l'indirizzo VIP in un indirizzo IP interno, spesso denominato ambiente del servizio app con bilanciamento del carico interno perché l'endpoint interno è un servizio di bilanciamento del carico interno.

In questo articolo viene illustrato come creare un ambiente del servizio app esterno. Per una panoramica dell'ambiente del servizio app, vedere [Introduzione al ambiente del servizio app][Intro]. Per informazioni su come creare un ambiente del servizio app ILB, vedere [creare e usare un][MakeILBASE]ambiente del servizio app ILB.

## <a name="before-you-create-your-ase"></a>Prima di creare l'ambiente del servizio app

Dopo avere creato l'ambiente del servizio app, non è possibile modificare quanto segue:

- Location
- Sottoscrizione
- Gruppo di risorse
- Rete virtuale usata
- Subnet usata
- Dimensioni della subnet

> [!NOTE]
> Quando si sceglie una rete virtuale e si specifica una subnet, assicurarsi che le dimensioni siano sufficienti per supportare la crescita futura e adattarvisi. È consigliabile una dimensione pari a `/24` con 256 indirizzi.
>

## <a name="three-ways-to-create-an-ase"></a>Tre modi per creare un ambiente del servizio app

Esistono tre modi per creare un ambiente del servizio app:

- **Durante la creazione di un piano di servizio app**. Questo metodo crea l'ambiente del servizio app e il piano di servizio app in un solo passaggio.
- **Come azione autonoma**. Questo metodo crea un ambiente del servizio app autonomo, ovvero un ambiente del servizio app vuoto. Questo metodo è un processo avanzato per creare un ambiente del servizio app. È possibile usarlo per creare un ambiente del servizio app con bilanciamento del carico interno.
- **Da un modello di Azure Resource Manager**. Questo metodo è per gli utenti avanzati. Per altre informazioni, vedere [creare un ambiente del servizio app da un modello][MakeASEfromTemplate].

Un ambiente del servizio app esterno ha un indirizzo VIP pubblico, quindi tutto il traffico HTTP/HTTPS verso le app nell'ambiente del servizio app usa un indirizzo IP accessibile da Internet. Un ambiente del servizio app con bilanciamento del carico interno ha un indirizzo IP dalla subnet usata dall'ambiente del servizio app. Le app ospitate in un ambiente del servizio app con bilanciamento del carico interno non sono esposte direttamente a Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Creare contemporaneamente un ambiente del servizio app e un piano di servizio app

Il piano di servizio app è un contenitore di applicazioni. Quando si crea un'applicazione nel servizio app, scegliere o creare un piano di servizio app. Gli ambienti del servizio app di Azure contengono piani del servizio app, che a loro volta contengono app.

Per creare un ambiente del servizio app quando si crea un piano di servizio app:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Crea una risorsa** > **Web e dispositivi mobili** > **App Web**.

    ![Creazione dell'app Web][1]

2. Selezionare la propria sottoscrizione. L'app e l'ambiente del servizio app vengono creati nelle stesse sottoscrizioni.

3. Selezionare o creare un gruppo di risorse. Con i gruppi di risorse è possibile gestire insieme le risorse di Azure correlate. I gruppi di risorse sono utili anche quando si stabiliscono le regole di controllo degli accessi in base al ruolo per le app. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure][ARMOverview].

4. Selezionare il sistema operativo (Windows, Linux o Docker). 

5. Selezionare il piano di servizio app, quindi selezionare **Crea nuovo**. Le app Web di Linux e di Windows non possono essere presenti nello stesso piano di servizio app, ma possono essere presenti nello stesso ambiente del servizio app. 

    ![Nuovo piano di servizio app][2]

6. Nell'elenco a discesa **Località** selezionare l'area in cui si vuole creare l'ambiente del servizio app. Se si seleziona un ambiente del servizio app esistente, non viene creato un nuovo ambiente del servizio app. Il piano di servizio app viene creato nell'ambiente del servizio app selezionato. 

7. Selezionare **Piano tariffario** e scegliere uno dei prezzi SKU **Isolato**. Se si sceglie una scheda SKU **Isolato** e una località diversa da un ambiente del servizio app, un nuovo ambiente del servizio app viene creato in tale località. Per avviare il processo di creazione di un ambiente del servizio app, selezionare **Seleziona**. Lo SKU **Isolato** è disponibile in combinazione con un ambiente del servizio app. Non è inoltre possibile usare qualsiasi altro codice di riferimento del prodotto per i prezzi in un ambiente del servizio app diverso da **Isolato**. 

    ![Selezione del piano tariffario][3]

8. Immettere il nome per l'ambiente del servizio app. Questo nome viene usato nel nome indirizzabile per le app. Se il nome dell'ambiente del servizio app è _appsvcenvdemo_, il nome di dominio sarà *.appsvcenvdemo.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo mytestapp.appsvcenvdemo.p.azurewebsites.net. Il nome non può contenere spazi. Anche se vengono usati caratteri maiuscoli, il nome di dominio corrisponde alla versione in caratteri minuscoli del nome.

    ![Nome del nuovo piano di servizio app][4]

9. Specificare i dettagli della rete virtuale Azure. Selezionare **Crea nuovo** o **Seleziona esistente**. L'opzione per selezionare una rete virtuale esistente è disponibile solo se si ha una rete virtuale nell'area selezionata. Se si seleziona **Crea nuovo**, immettere un nome per la rete virtuale. Viene creata una nuova rete virtuale con questo nome, che usa lo spazio indirizzi `192.168.250.0/23` nell'area selezionata. Se si sceglie **Seleziona esistente**, è necessario:

    a. Selezionare il blocco di indirizzi della rete virtuale, se ne è disponibile più di uno.

    b. Immettere un nuovo nome di subnet.

    c. Selezionare la dimensione della subnet. *Ricordare di selezionare dimensioni sufficientemente grandi per supportare la crescita futura dell'ambiente del servizio app*. È consigliabile scegliere `/24`, che contiene 128 indirizzi e può gestire un ambiente del servizio app con dimensione massima. Non è consigliato, ad esempio, `/28`, perché sono disponibili solo 16 indirizzi. L'infrastruttura usa almeno sette indirizzi e Rete di Azure ne usa altri cinque. In una subnet `/28`, il ridimensionamento massimo possibile è di quattro istanze del piano di servizio app per un ambiente del servizio app esterno e di appena tre istante del piano di servizio app per un ambiente del servizio app con bilanciamento del carico interno.

    d. Selezionare l'intervallo IP della subnet.

10. Selezionare **Crea** per creare l'ambiente del servizio app. Questo processo crea anche il piano di servizio app e l'applicazione. L'ambiente del servizio app, il piano di servizio app e l'applicazione sono tutti inclusi nella stessa sottoscrizione e nello stesso gruppo di risorse. Se l'ambiente del servizio app richiede un gruppo di risorse separato o se è necessario un ambiente del servizio app con bilanciamento del carico interno, seguire la procedura per creare un ambiente del servizio app autonomo.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Creare un ambiente del servizio app e un'app Web Linux tramite un'immagine personalizzata di Docker

1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Create a Resource** > **Web + Mobile** > **App Web per contenitori** (Crea una risorsa > Web e dispositivi mobili). 

    ![Creazione dell'app Web][7]

1. Selezionare la propria sottoscrizione. L'app e l'ambiente del servizio app vengono creati nelle stesse sottoscrizioni.

1. Selezionare o creare un gruppo di risorse. Con i gruppi di risorse è possibile gestire insieme le risorse di Azure correlate. I gruppi di risorse sono utili anche quando si stabiliscono le regole di controllo degli accessi in base al ruolo per le app. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure][ARMOverview].

1. Selezionare il piano di servizio app, quindi selezionare **Crea nuovo**. Le app Web di Linux e di Windows non possono essere presenti nello stesso piano di servizio app, ma possono essere presenti nello stesso ambiente del servizio app. 

    ![Nuovo piano di servizio app][8]

1. Nell'elenco a discesa **Località** selezionare l'area in cui si vuole creare l'ambiente del servizio app. Se si seleziona un ambiente del servizio app esistente, non viene creato un nuovo ambiente del servizio app. Il piano di servizio app viene creato nell'ambiente del servizio app selezionato. 

1. Selezionare **Piano tariffario** e scegliere uno dei prezzi SKU **Isolato**. Se si sceglie una scheda SKU **Isolato** e una località diversa da un ambiente del servizio app, un nuovo ambiente del servizio app viene creato in tale località. Per avviare il processo di creazione di un ambiente del servizio app, selezionare **Seleziona**. Lo SKU **Isolato** è disponibile in combinazione con un ambiente del servizio app. Non è inoltre possibile usare qualsiasi altro codice di riferimento del prodotto per i prezzi in un ambiente del servizio app diverso da **Isolato**. 

    ![Selezione del piano tariffario][3]

1. Immettere il nome per l'ambiente del servizio app. Questo nome viene usato nel nome indirizzabile per le app. Se il nome dell'ambiente del servizio app è _appsvcenvdemo_, il nome di dominio sarà *.appsvcenvdemo.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo mytestapp.appsvcenvdemo.p.azurewebsites.net. Il nome non può contenere spazi. Anche se vengono usati caratteri maiuscoli, il nome di dominio corrisponde alla versione in caratteri minuscoli del nome.

    ![Nome del nuovo piano di servizio app][4]

1. Specificare i dettagli della rete virtuale Azure. Selezionare **Crea nuovo** o **Seleziona esistente**. L'opzione per selezionare una rete virtuale esistente è disponibile solo se si ha una rete virtuale nell'area selezionata. Se si seleziona **Crea nuovo**, immettere un nome per la rete virtuale. Viene creata una nuova rete virtuale con questo nome, che usa lo spazio indirizzi `192.168.250.0/23` nell'area selezionata. Se si sceglie **Seleziona esistente**, è necessario:

    a. Selezionare il blocco di indirizzi della rete virtuale, se ne è disponibile più di uno.

    b. Immettere un nuovo nome di subnet.

    c. Selezionare la dimensione della subnet. *Ricordare di selezionare dimensioni sufficientemente grandi per supportare la crescita futura dell'ambiente del servizio app*. È consigliabile scegliere `/24`, che contiene 128 indirizzi e può gestire un ambiente del servizio app con dimensione massima. Non è consigliato, ad esempio, `/28`, perché sono disponibili solo 16 indirizzi. L'infrastruttura usa almeno sette indirizzi e Rete di Azure ne usa altri cinque. In una subnet `/28`, il ridimensionamento massimo possibile è di quattro istanze del piano di servizio app per un ambiente del servizio app esterno e di appena tre istante del piano di servizio app per un ambiente del servizio app con bilanciamento del carico interno.

    d. Selezionare l'intervallo IP della subnet.

1.  Selezionare l'opzione "Configura contenitore".
    * Immettere il nome dell'immagine personalizzata (è possibile usare Registro Azure Container, Hub Docker e il proprio registro privato). Se non si intende usare il proprio contenitore personalizzato, è possibile usare il proprio codice e un'immagine personalizzata con il servizio app in Linux seguendo le istruzioni precedenti. 

    ![Configura contenitore][9]

1. Selezionare **Crea** per creare l'ambiente del servizio app. Questo processo crea anche il piano di servizio app e l'applicazione. L'ambiente del servizio app, il piano di servizio app e l'applicazione sono tutti inclusi nella stessa sottoscrizione e nello stesso gruppo di risorse. Se l'ambiente del servizio app richiede un gruppo di risorse separato o se è necessario un ambiente del servizio app con bilanciamento del carico interno, seguire la procedura per creare un ambiente del servizio app autonomo.


## <a name="create-an-ase-by-itself"></a>Creare un ambiente del servizio app autonomo

Se si crea un ambiente del servizio app autonomo, risulterà vuoto. Un ambiente del servizio app vuoto continua a dover pagare una quota mensile per l'infrastruttura. Seguire questa procedura per creare un ambiente del servizio app con bilanciamento del carico interno o per creare un ambiente del servizio app nel proprio gruppo di risorse. Dopo avere creato l'ambiente del servizio app, è possibile crearvi app usando il normale processo. Selezionare il nuovo ambiente del servizio app come località.

1. In Azure Marketplace cercare **Ambiente del servizio app** o selezionare **Crea una risorsa** > **Web e dispositivi mobili** > **Ambiente del servizio app**. 

1. Immettere il nome dell'ambiente del servizio app. Questo nome viene usato per le app create nell'ambiente del servizio app. Se il nome è *mynewdemoase*, il nome del sottodominio è *.mynewdemoase.p.azurewebsites.net*. Se è stata creata un'app denominata *mytestapp*, questa sarà disponibile all'indirizzo mytestapp.mynewdemoase.p.azurewebsites.net. Il nome non può contenere spazi. Anche se vengono usati caratteri maiuscoli, il nome di dominio corrisponde alla versione in caratteri minuscoli del nome. Se si usa un servizio di bilanciamento del carico interno, il nome dell'ambiente del servizio app non viene usato nel sottodominio, ma viene dichiarato in modo esplicito durante la creazione dell'ambiente.

    ![Denominazione dell'ambiente del servizio app][5]

1. Selezionare la propria sottoscrizione. Questa sottoscrizione è anche quella usata da tutte le app nell'ambiente del servizio app. Non è possibile inserire l'ambiente del servizio app in una rete virtuale che si trova in un'altra sottoscrizione.

1. Selezionare o specificare un nuovo gruppo di risorse. Il gruppo di risorse per l'ambiente del servizio app deve essere uguale a quello usato per la rete virtuale. Se si seleziona una rete virtuale esistente, la selezione del gruppo di risorse per l'ambiente del servizio app viene aggiornata per riflettere il valore della rete virtuale. *È possibile creare un ambiente del servizio app con un gruppo di risorse diverso dal gruppo di risorse della rete virtuale se si usa un modello di Resource Manager*. Per creare un ambiente del servizio app da un modello, vedere [creare un ambiente del servizio app da un modello][MakeASEfromTemplate].

    ![Selezione del gruppo di risorse][6]

1. Selezionare la rete virtuale e la località. È possibile creare una nuova rete virtuale o selezionare una rete virtuale esistente: 

    * Se si seleziona una nuova rete virtuale, è possibile specificare un nome e una località. 
    
    * Alla nuova rete virtuale vengono assegnati l'intervallo di indirizzi 192.168.250.0/23 e una subnet denominata predefinita. La subnet è definita come 192.168.250.0/24. È possibile selezionare solo una rete virtuale di Resource Manager. La selezione di **Tipo di indirizzo VIP** indica se è possibile accedere all'ambiente del servizio app direttamente da Internet (Esterno) o se si usa un servizio di bilanciamento del carico interno. Per ulteriori informazioni su queste opzioni, vedere [creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app][MakeILBASE]. 

      * Se si seleziona **Esterno** come **Tipo di indirizzo VIP**, è possibile selezionare il numero di indirizzi IP esterni con cui viene creato il sistema per finalità di associazione SSL basata su IP. 
    
      * Se si seleziona **Interno** come **Tipo di indirizzo VIP**, è necessario specificare il dominio usato dall'ambiente del servizio app. È possibile distribuire un ambiente del servizio app in una rete virtuale che usa gli intervalli di indirizzi pubblici o privati. Per usare una rete virtuale con un intervallo di indirizzi pubblici, è necessario creare in anticipo la rete virtuale. 
    
    * Se si seleziona una rete virtuale di esistente, viene creata una nuova subnet contemporaneamente all'ambiente del servizio app. *Non è possibile usare una subnet creata in precedenza nel portale. È possibile creare un ambiente del servizio app con una subnet esistente se si usa un modello di Resource Manager.* Per creare un ambiente del servizio app da un modello, vedere [creare un ambiente del servizio app da un modello][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente del servizio app 1

È tuttavia possibile creare istanze della prima versione dell'ambiente del servizio app. Per avviare tale processo, cercare **App Service Environment v1** (Ambiente del servizio app 1) nel Marketplace. L'ambiente del servizio app viene creato esattamente come si crea l'ambiente del servizio app autonomo. Al termine, l'ambiente del servizio app 1 ha due front-end e due ruoli di lavoro. Con l'ambiente del servizio app 1 è necessario gestire i front-end e i ruoli di lavoro. Questi non vengono aggiunti automaticamente quando si creano i piani di servizio app. Le risorse front-end fungono da endpoint HTTP/HTTPS e inviano il traffico ai ruoli di lavoro. I ruoli di lavoro ospitano le app. Dopo avere creato l'ambiente del servizio app, è possibile modificare la quantità di front-end e ruoli di lavoro. 

Per altre informazioni su ASEv1, vedere [Introduzione alla ambiente del servizio app V1][ASEv1Intro]. Per ulteriori informazioni sul ridimensionamento, la gestione e il monitoraggio di ASEv1, vedere [come configurare un ambiente del servizio app][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
