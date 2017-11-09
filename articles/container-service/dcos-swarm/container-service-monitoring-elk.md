---
title: Monitorare un cluster DC/OS di Azure - Stack ELK | Documentazione Microsoft
description: Monitorare un cluster DC/OS nel cluster del servizio contenitore di Azure con ELK (Elasticsearch, Logstash e Kibana).
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: Contenitori, DC/OS, Azure, monitoraggio, ELK
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcfa277cdd0f3cebc0fbbb23e771fb23ffbe2ca6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Monitorare un cluster del servizio contenitore di Azure con ELK
Questo articolo descrive come distribuire lo stack ELK (Elasticsearch, Logstash, Kibana) in un cluster DC/OS nel servizio contenitore di Azure. 

## <a name="prerequisites"></a>Prerequisiti
[Distribuire](container-service-deployment.md) e [connettere](../container-service-connect.md) un cluster DC/OS configurato dal servizio contenitore di Azure. Esplorare il dashboard di DC/OS e i servizi Marathon [qui](container-service-mesos-marathon-ui.md). Installare anche il [servizio di bilanciamento del carico Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Lo stack ELK, una combinazione di Elasticsearch, Logstash e Kibana, fornisce uno stack end-to-end che può essere usato per monitorare e analizzare i log nel cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurare lo stack ELK in un cluster DC/OS
Accedere all'interfaccia utente di DC/OS usando [http://localhost:80/](http://localhost:80/) Nell'interfaccia utente di DC/OS passare a **Universe**. Cercare e installare Elasticsearch, Logstash e Kibana da DC/OS Universe in questo ordine specifico. Altre informazioni sulla configurazione sono disponibili nel collegamento relativo all'**installazione avanzata**.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Dopo che i contenitori ELK sono attivi, è necessario abilitare l'accesso a Kibana tramite Marathon-LB. Passare a **Servizi** > **kibana** e fare clic su **Modifica**, come illustrato di seguito.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Passare alla **modalità JSON** ed eseguire lo scorrimento fino alla sezione relativa alle etichette.
È necessario aggiungere una voce `"HAPROXY_GROUP": "external"` qui, come mostrato di seguito.
Dopo avere fatto clic su **Distribuisci modifiche**, il contenitore viene riavviato.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se si vuole verificare che Kibana sia registrato come servizio nel dashboard di HAPROXY, è necessario aprire la porta 9090 nel cluster agente poiché HAPROXY viene eseguito in questa porta.
Per impostazione predefinita vengono aperte le porte 80, 8080 e 443 nel cluster agente DC/OS.
Istruzioni su come aprire una porta e fornire l'accesso pubblico sono disponibili [qui](container-service-enable-public-access.md).

Per accedere al dashboard di HAPROXY, aprire l'interfaccia di amministrazione di Marathon LB all'indirizzo: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Quando si passa all'URL, verrà visualizzato il dashboard di HAPROXY come illustrato di seguito e verrà visualizzata una voce del servizio per Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Per accedere al dashboard di Kibana distribuito nella porta 5601, è necessario aprire la porta 5601. Seguire le istruzioni [qui](container-service-enable-public-access.md). Aprire quindi il dashboard di Kibana all'indirizzo: `http://localhost:5601`.

## <a name="next-steps"></a>Passaggi successivi

* Per l'inoltro e la configurazione del log di sistema e dell'applicazione, vedere [Log Management in DC/OS with ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/) (Gestione log nel controller di dominio o sistema operativo con ELK).

* Per filtrare i log, vedere [Filtering Logs with ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/) (Filtrare i log con ELK). 

 

