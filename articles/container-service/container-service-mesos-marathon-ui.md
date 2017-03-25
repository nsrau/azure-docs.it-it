---
title: Gestire un cluster DC/OS di Azure con l&quot;interfaccia utente di Marathon | Documentazione Microsoft
description: Distribuire contenitori in un cluster del servizio contenitore di Azure usando l&quot;interfaccia utente Web di Marathon.
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f26b191d9d98768d766e4c974138c9d191340027
ms.lasthandoff: 03/15/2017


---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>Gestire un cluster DC/OS del servizio contenitore di Azure tramite l'interfaccia utente Web di Marathon
DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. 


## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire questi esempi, è necessario avere un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

* [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md)
* [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Esplorare l'interfaccia utente di DC/OS
Dopo aver [stabilito](container-service-connect.md) un tunnel SSH (Secure Shell), passare a http://localhost/. Verrà caricata l'interfaccia utente Web di DC/OS che visualizza informazioni sul cluster, ad esempio le risorse usate, gli agenti attivi e i servizi in esecuzione.

![Interfaccia utente di DC/OS](./media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Esplorare l'interfaccia utente di Marathon
Per visualizzare l'interfaccia utente di Marathon, andare su http://localhost/marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster DC/OS del servizio contenitore di Azure. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.  

![Interfaccia utente di Marathon](./media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Distribuire un contenitore Docker formattato
Per distribuire un nuovo contenitore con Marathon, fare clic sul pulsante **Create Application** (Crea applicazione) e immettere le informazioni seguenti nel modulo:

| Campo | Valore |
| --- | --- |
| ID |nginx |
| Memoria | 32 |
| Image |nginx |
| Network |Bridged |
| Host Port |80 |
| Protocol |TCP |

![Interfaccia utente New Application--General](./media/dcos/dcos4.png)

![Interfaccia utente New Application--Docker Container](./media/dcos/dcos5.png)

![Interfaccia utente New Application--Ports and Service Discovery](./media/dcos/dcos6.png)

Per eseguire il mapping statico della porta del contenitore su una porta dell'agente è necessario usare la modalità JSON. A tale scopo impostare la procedura guidata New Application su **JSON Mode** usando l'interruttore. Specificare quindi l'impostazione seguente sotto la sezione `portMappings` della definizione dell'applicazione. Questo esempio associa la porta 80 del contenitore alla porta 80 dell'agente DC/OS. Dopo aver apportato questa modifica è possibile uscire dalla modalità JSON della procedura guidata.

```none
"hostPort": 80,
```

![Interfaccia utente New Application--esempio con porta 80](./media/dcos/dcos13.png)

Se si vuole abilitare le verifiche di integrità, impostare un percorso nella scheda **Health Checks** (Verifiche integrità).

![Nuova interfaccia utente dell'applicazione - Verifiche di integrità](./media/dcos/dcos_healthcheck.png)

Il cluster CD/OS viene distribuito con un set di agenti privati e pubblici. Per consentire al cluster di accedere alle applicazioni da Internet è necessario distribuire le applicazioni a un agente di pubblico. A tale scopo, selezionare la scheda **Optional** (Facoltativo) della procedura guidata New Application (Nuova applicazione) e immettere **slave_public** in **Accepted Resource Roles** (Ruoli risorsa accettati).

Fare clic su **Create Application** (Crea applicazione).

![Interfaccia utente New Application--impostazione dell'agente pubblico](./media/dcos/dcos14.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore. Inizialmente è visualizzato lo stato **Deploying** (Distribuzione). Dopo una corretta distribuzione, lo stato cambia in **Running** (In esecuzione).

![Interfaccia utente della pagina principale di Marathon--stato di distribuzione contenitore](./media/dcos/dcos7.png)

Quando si passa di nuovo all'interfaccia utente Web di DC/OS (http://localhost/) si noterà che nel cluster DC/OS è in esecuzione un'attività, in questo caso un contenitore in formato Docker.

![Interfaccia utente Web di DC/OS--attività in esecuzione nel cluster](./media/dcos/dcos8.png)

Per visualizzare il nodo del cluster in cui viene eseguita l'attività, fare clic sulla scheda **Nodes** (Nodi).

![Interfaccia utente Web di DC/OS--nodo del cluster dell'attività](./media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Ridimensionare i contenitori
È possibile usare l'interfaccia utente di Marathon per ridimensionare il numero di istanze di un contenitore. A questo scopo, passare alla pagina **Marathon**, selezionare il contenitore da ridimensionare e fare clic sul pulsante **Scale Application** (Ridimensiona applicazione). Nella finestra di dialogo **Scale Application** (Ridimensiona applicazione) immettere il numero di istanze del contenitore desiderate e fare clic su **Scale Application** (Ridimensiona applicazione).

![Interfaccia utente di Marathon--finestra di dialogo Scale Application](./media/dcos/dcos10.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di DC/OS.

![Dashboard dell'interfaccia utente Web di DC/OS--attività distribuita tra agenti](./media/dcos/dcos11.png)

![Interfaccia utente Web di DC/OS--nodi](./media/dcos/dcos12.png)

## <a name="next-steps"></a>Passaggi successivi
* [Usare DC/OS e l'API Marathon](container-service-mesos-marathon-rest.md)

* Approfondimento sul servizio contenitore di Azure con Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 

