---
title: Bilanciare il carico dei contenitori in un cluster DC/OS di Azure
description: "Bilanciare il carico tra più contenitori in un cluster DC/OS del servizio contenitore di Azure."
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6f5467d0fbcc577a548f1100ed6e4d380fe38759
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Bilanciare il carico dei contenitori in un cluster DC/OS del servizio contenitore di Azure

Questo articolo illustra come creare un servizio di bilanciamento del carico interno in un servizio contenitore di Azure gestito di tipo DC/OS con Marathon-LB. Questa configurazione supporta la scalabilità orizzontale delle applicazioni e consente anche di sfruttare i cluster di agenti pubblici e privati inserendo i servizi di bilanciamento del carico nel cluster pubblico e i contenitori di applicazioni nel cluster privato. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare un servizio di bilanciamento del carico Marathon
> * Distribuire un'applicazione tramite il bilanciamento del carico
> * Configurare Azure Load Balancer

È necessario un cluster del controller di dominio/sistema operativo del servizio contenitore di Azure per completare i passaggi in questa esercitazione. Se necessario, questo [script di esempio](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) può crearne uno appositamente.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Panoramica del bilanciamento del carico

In un cluster DC/OS del servizio contenitore di Azure esistono due livelli di bilanciamento del carico: 

**Azure Load Balancer** offre punti di ingresso pubblici, ovvero i punti accessibili per gli utenti finali. Questa funzionalità viene fornita automaticamente dal servizio contenitore di Azure ed è configurata per impostazione predefinita in modo da esporre le porte 80, 443 e 8080.

Il **servizio di bilanciamento del carico Marathon (marathon-lb)** indirizza le richieste in ingresso a istanze del contenitore che gestiscono tali richieste. Quando i contenitori che mettono a disposizione il servizio Web vengono ridimensionati, marathon-lb si adatta in modo dinamico. Questo servizio di bilanciamento del carico non viene fornito per impostazione predefinita nel servizio contenitore, ma è semplice da installare.

## <a name="configure-marathon-load-balancer"></a>Configurare il servizio di bilanciamento del carico Marathon

Il servizio di bilanciamento del carico Marathon si riconfigura in base ai contenitori distribuiti. È anche resiliente in caso di perdita di un contenitore o di un agente. In questi casi, Apache Mesos riavvia il contenitore in un'altra posizione e marathon-lb si adatta.

Eseguire il comando seguente per installare il servizio di bilanciamento del carico Marathon nel cluster dell'agente pubblico.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Distribuire un'applicazione con bilanciamento del carico

Ora che il pacchetto marathon-lb è disponibile, è possibile distribuire un contenitore di applicazioni di cui si vuole bilanciare il carico. 

Innanzitutto, ottenere il nome di dominio completo degli agenti esposti pubblicamente.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Creare poi un file denominato *hello-web.json* e copiarvi il contenuto seguente. L'etichetta `HAPROXY_0_VHOST` deve essere aggiornata con il nome di dominio completo degli agenti DC/OS. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Usare l'interfaccia della riga di comando di DC/OS per eseguire l'applicazione. Per impostazione predefinita, Marathon distribuisce l'applicazione nel cluster privato. Ciò significa che la distribuzione di cui sopra è accessibile solo tramite il servizio di bilanciamento del carico, che corrisponde in genere al comportamento desiderato.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Una volta distribuita l'applicazione, passare al nome di dominio completo del cluster dell'agente per visualizzare l'applicazione con bilanciamento del carico.

![Immagine dell'applicazione con bilanciamento del carico](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configurare Azure Load Balancer

Per impostazione predefinita, Azure Load Balancer espone le porte 80, 8080 e 443. Se si usa una di queste tre porte, come nell'esempio precedente, non è necessario eseguire alcuna operazione. Dovrebbe essere possibile accedere al nome FQDN del servizio di bilanciamento del carico dell'agente e, ogni volta che si esegue l'aggiornamento, si accederà a uno dei tre server Web in modalità round robin. 

Se si usa una porta diversa, è necessario aggiungere una regola di round robin e un probe nel servizio di bilanciamento del carico per la porta usata. È possibile eseguire questa operazione dall'[interfaccia della riga di comando di Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) con i comandi `azure network lb rule create` e `azure network lb probe create`.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata illustrata la funzionalità di bilanciamento del carico nel servizio contenitore di Azure sia con il servizio di bilanciamento del carico Marathon che con Azure Load Balancer, incluse le azioni seguenti:

> [!div class="checklist"]
> * Configurare un servizio di bilanciamento del carico Marathon
> * Distribuire un'applicazione tramite il bilanciamento del carico
> * Configurare Azure Load Balancer

Passare alla prossima esercitazione per informazioni sull'integrazione dell'archiviazione di Azure con DC/OS in Azure.

> [!div class="nextstepaction"]
> [Montare una condivisione file di Azure nel cluster DC/OS](container-service-dcos-fileshare.md)