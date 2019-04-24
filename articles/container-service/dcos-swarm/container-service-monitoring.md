---
title: (DEPRECATO) Monitorare un cluster del controller di dominio/sistema operativo di Azure - Datadog
description: Monitorare un cluster del servizio Azure Container con Datadog. Usare l'interfaccia utente Web del controller di dominio/sistema operativo per distribuire gli agenti Datadog al cluster.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60508125"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(DEPRECATO) Monitorare un cluster del controller di dominio/sistema operativo del servizio contenitore di Azure con DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In questo articolo verranno distribuiti agenti di Datadog in tutti i nodi agente nel cluster del servizio Azure Container. Per questa configurazione, sarà necessario un account con Datadog. 

## <a name="prerequisites"></a>Prerequisiti
[Distribuire](container-service-deployment.md) e [connettere](../container-service-connect.md) un cluster configurato dal servizio Azure Container. Esplorare l' [interfaccia utente](container-service-mesos-marathon-ui.md)di Marathon. Passare a [https://datadoghq.com](https://datadoghq.com) per configurare un account Datadog. 

## <a name="datadog"></a>Datadog
Datadog è un servizio che raccoglie dati di monitoraggio dai contenitori all'interno del cluster del servizio Azure Container. Datadog è dotato di un dashboard di integrazione Docker in cui è possibile visualizzare metriche specifiche all'interno dei propri contenitori. Le metriche raccolte dai contenitori sono organizzate per CPU, memoria, rete e I/O. Datadog suddivide le metriche in contenitori e immagini. Un esempio dell'aspetto dell'interfaccia utente per l'utilizzo della CPU è riportato di seguito.

![Interfaccia utente Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurare una distribuzione Datadog con Marathon
Questi passaggi illustrano come configurare e distribuire le applicazioni Datadog nel cluster con Marathon. 

Accedere all'interfaccia utente del controller di dominio/sistema operativo da [http://localhost:80/](http://localhost:80/). Dall'interfaccia utente del controller di dominio/sistema operativo passare a "Universe" ("Universo") in basso a sinistra e quindi cercare "Datadog" e fare clic su "Install".

![Pacchetto Datadog in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring/datadog1.png)

Per completare la configurazione, sarà necessario un account cloud Datadog o un account di valutazione gratuita. Dopo aver eseguito l'accesso al sito Web di Datadog, a sinistra passare a Integrations (Integrazioni) -> [APIs](https://app.datadoghq.com/account/settings#api) (API). 

![Chiave API di Datadog](./media/container-service-monitoring/datadog2.png)

Immettere quindi la chiave API nella configurazione di Datadog in Universe (Universo) per il controller di dominio/sistema operativo. 

![Configurazione di Datadog in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring/datadog3.png) 

Nella configurazione precedente le istanze sono impostate su 10000000 in modo che quando viene aggiunto un nuovo nodo al cluster Datadog distribuirà automaticamente un agente in tale nodo. Si tratta di una soluzione temporanea. Dopo aver installato il pacchetto si dovrebbe tornare al sito Web Datadog e cercare "[Dashboards](https://app.datadoghq.com/dash/list)" (Dashboard). Da qui si vedrà Custom and Integration Dashboards. Il [Docker dashboard](https://app.datadoghq.com/screen/integration/docker) (Dashboard Docker) conterrà tutte le metriche del contenitore necessarie per il monitoraggio del cluster. 

