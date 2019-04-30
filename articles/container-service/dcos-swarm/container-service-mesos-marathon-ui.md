---
title: (DEPRECATO) Gestire un cluster DC/OS di Azure con l'interfaccia utente Marathon
description: Distribuire contenitori in un cluster del servizio Azure Container usando l'interfaccia utente Web di Marathon.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 16c16c0217a796ffbb57e10430f90cb4a7660ac6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468227"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(DEPRECATO) Gestire un cluster DC/OS del servizio contenitore di Azure tramite l'interfaccia utente Web di Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come iniziare la distribuzione di contenitori con Marathon. 


## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire questi esempi, è necessario avere un cluster DC/OS configurato nel servizio Azure Container. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

* [Distribuire un cluster del servizio Azure Container](container-service-deployment.md)
* [Connettersi a un cluster del servizio Azure Container](../container-service-connect.md)

> [!NOTE]
> Questo articolo presuppone il tunneling dell'utente al cluster DC/OS tramite la porta locale 80.
>

## <a name="explore-the-dcos-ui"></a>Esplorare l'interfaccia utente di DC/OS
Con un tunnel Secure Shell (SSH) [stabilita](../container-service-connect.md), passare a http: \/ /localhost /. Verrà caricata l'interfaccia utente Web di DC/OS che visualizza informazioni sul cluster, ad esempio le risorse usate, gli agenti attivi e i servizi in esecuzione.

![Interfaccia utente di DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Esplorare l'interfaccia utente di Marathon
Per visualizzare l'interfaccia utente di Marathon, passare a http: \/ /localhost/marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster DC/OS del servizio Azure Container. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.  

![Interfaccia utente di Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Distribuire un contenitore Docker formattato
Per distribuire un nuovo contenitore con Marathon, fare clic sul pulsante **Create Application** (Crea applicazione) e immettere le informazioni seguenti nel modulo:

| Campo | Value |
| --- | --- |
| ID |nginx |
| Memoria | 32 |
| Image |nginx |
| Network |Bridged |
| Host Port |80 |
| Protocol |TCP |

![Interfaccia utente New Application--General](./media/container-service-mesos-marathon-ui/dcos4.png)

![Interfaccia utente New Application--Docker Container](./media/container-service-mesos-marathon-ui/dcos5.png)

![Interfaccia utente New Application--Ports and Service Discovery](./media/container-service-mesos-marathon-ui/dcos6.png)

Per eseguire il mapping statico della porta del contenitore su una porta dell'agente è necessario usare la modalità JSON. A tale scopo impostare la procedura guidata New Application su **JSON Mode** usando l'interruttore. Specificare quindi l'impostazione seguente sotto la sezione `portMappings` della definizione dell'applicazione. Questo esempio associa la porta 80 del contenitore alla porta 80 dell'agente DC/OS. Dopo aver apportato questa modifica è possibile uscire dalla modalità JSON della procedura guidata.

```none
"hostPort": 80,
```

![Interfaccia utente New Application--esempio con porta 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Se si vuole abilitare le verifiche di integrità, impostare un percorso nella scheda **Health Checks** (Verifiche integrità).

![Nuova interfaccia utente dell'applicazione - Verifiche di integrità](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

Il cluster CD/OS viene distribuito con un set di agenti privati e pubblici. Per consentire al cluster di accedere alle applicazioni da Internet è necessario distribuire le applicazioni a un agente di pubblico. A tale scopo, selezionare la scheda **Optional** (Facoltativo) della procedura guidata New Application (Nuova applicazione) e immettere **slave_public** in **Accepted Resource Roles** (Ruoli risorsa accettati).

Fare clic su **Create Application** (Crea applicazione).

![Interfaccia utente New Application--impostazione dell'agente pubblico](./media/container-service-mesos-marathon-ui/dcos14.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore. Inizialmente è visualizzato lo stato **Deploying** (Distribuzione). Dopo una corretta distribuzione, lo stato cambia in **Running** (In esecuzione).

![Interfaccia utente della pagina principale di Marathon--stato di distribuzione contenitore](./media/container-service-mesos-marathon-ui/dcos7.png)

Quando si passa al controller di dominio/sistema operativo di un'interfaccia utente web (http:\//localhost/), noterete che un'attività (in questo caso, un contenitore Docker formattato) sia in esecuzione nel cluster DC/OS.

![Interfaccia utente Web di DC/OS--attività in esecuzione nel cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

Per visualizzare il nodo del cluster in cui viene eseguita l'attività, fare clic sulla scheda **Nodes** (Nodi).

![Interfaccia utente Web di DC/OS--nodo del cluster dell'attività](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Raggiungere il contenitore

In questo esempio l'applicazione è in esecuzione su un nodo dell'agente pubblico. Si raggiunge l'applicazione da Internet passando al FQDN dell'agente del cluster `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, dove:

* **DNSPREFIX** è il prefisso DNS specificato al momento della distribuzione del cluster.
* **REGION** è l'area in cui si trova il gruppo di risorse.

    ![Nginx da Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Passaggi successivi
* [Usare DC/OS e l'API Marathon](container-service-mesos-marathon-rest.md)

* Approfondimento sul servizio Azure Container con Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
