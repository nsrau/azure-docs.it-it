---
title: Gestione di contenitori del servizio contenitore di Azure tramite l&quot;interfaccia utente Web | Documentazione Microsoft
description: Distribuire contenitori in un cluster del servizio contenitore di Azure usando l&quot;interfaccia utente Web di Marathon.
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae2945b0025da3bc933a1e4a8f10f21fd35cb51c


---
# <a name="container-management-through-the-web-ui"></a>Gestione di contenitori tramite l'interfaccia utente Web
DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

* [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md)
* [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Esplorare l'interfaccia utente di DC/OS
Dopo aver stabilito un tunnel SSH (Secure Shell), passare a http://localhost/. Verrà caricata l'interfaccia utente Web di DC/OS che visualizza informazioni sul cluster, ad esempio le risorse usate, gli agenti attivi e i servizi in esecuzione.

![Interfaccia utente di DC/OS](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Esplorare l'interfaccia utente di Marathon
Per visualizzare l'interfaccia utente di Marathon, passare a http://localhost/Marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster DC/OS del servizio contenitore di Azure. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.  

![Interfaccia utente di Marathon](media/dcos/dcos3.png)

## <a name="deploy-a-dockerformatted-container"></a>Distribuire un contenitore Docker formattato
Per distribuire un nuovo contenitore con Marathon, fare clic sul pulsante **Create Application** e immettere le informazioni seguenti nel modulo:

| Campo | Valore |
| --- | --- |
| ID |nginx |
| Image |nginx |
| Network |Bridged |
| Host Port |80 |
| Protocol |TCP |

![Interfaccia utente New Application--General](media/dcos/dcos4.png)

![Interfaccia utente New Application--Docker Container](media/dcos/dcos5.png)

![Interfaccia utente New Application--Ports and Service Discovery](media/dcos/dcos6.png)

Per eseguire il mapping statico della porta del contenitore su una porta dell'agente è necessario usare la modalità JSON. A tale scopo impostare la procedura guidata New Application su **JSON Mode** usando l'interruttore. Specificare quindi il codice seguente sotto la sezione `portMappings` della definizione dell'applicazione. Questo esempio associa la porta 80 del contenitore alla porta 80 dell'agente DC/OS. Dopo aver apportato questa modifica è possibile uscire dalla modalità JSON della procedura guidata.

```none
"hostPort": 80,
```

![Interfaccia utente New Application--esempio con porta 80](media/dcos/dcos13.png)

Il cluster CD/OS viene distribuito con un set di agenti privati e pubblici. Per consentire al cluster di accedere alle applicazioni da Internet è necessario distribuire le applicazioni a un agente di pubblico. A tale scopo, selezionare la scheda **Optional** (Facoltativo) della procedura guidata New Application (Nuova applicazione) e immettere **slave_public** in **Accepted Resource Roles** (Ruoli risorsa accettati).

![Interfaccia utente New Application--impostazione dell'agente pubblico](media/dcos/dcos14.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore.

![Interfaccia utente della pagina principale di Marathon--stato di distribuzione contenitore](media/dcos/dcos7.png)

Quando si passa di nuovo all'interfaccia utente Web di DC/OS (http://localhost/) si noterà che nel cluster DC/OS è in esecuzione un'attività, in questo caso un contenitore in formato Docker.

![Interfaccia utente Web di DC/OS--attività in esecuzione nel cluster](media/dcos/dcos8.png)

Viene anche visualizzato il nodo del cluster in cui viene eseguita l'attività.

![Interfaccia utente Web di DC/OS--nodo del cluster dell'attività](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Ridimensionare i contenitori
È possibile usare l'interfaccia utente di Marathon per ridimensionare il numero di istanze di un contenitore. A questo scopo, passare alla pagina **Marathon**, selezionare il contenitore da ridimensionare e fare clic sul pulsante **Scale** (Ridimensiona). Nella finestra di dialogo **Scale Application** (Ridimensiona applicazione) immettere il numero di istanze del contenitore desiderate e selezionare **Scale Application** (Ridimensiona applicazione).

![Interfaccia utente di Marathon--finestra di dialogo Scale Application](media/dcos/dcos10.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di DC/OS.

![Dashboard dell'interfaccia utente Web di DC/OS--attività distribuita tra agenti](media/dcos/dcos11.png)

![Interfaccia utente Web di DC/OS--nodi](media/dcos/dcos12.png)

## <a name="next-steps"></a>Passaggi successivi
* [Usare DC/OS e l'API Marathon](container-service-mesos-marathon-rest.md)

Approfondimento sul servizio contenitore di Azure con Mesos

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
> 
> 




<!--HONumber=Nov16_HO2-->


