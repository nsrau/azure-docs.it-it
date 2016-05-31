<properties
   pageTitle="Bilanciare il carico di un cluster del servizio contenitore di Azure | Microsoft Azure"
   description="Bilanciare il carico di un cluster del servizio contenitore di Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenitori, Micro-Service, controller di dominio/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Bilanciare il carico di un cluster del servizio contenitore di Azure

In questo articolo verrà configurato un front-end Web di cui è possibile aumentare le prestazioni per fornire servizi dietro il servizio di bilanciamento del carico di Azure.


## Prerequisiti

[Distribuire un'istanza del servizio contenitore di Azure](container-service-deployment.md) con agente di orchestrazione di tipo DCOS, [assicurarsi che il client possa connettersi al cluster](container-service-connect.md) e[AZURE.INCLUDE [installare l'interfaccia della riga di comando del controller di dominio/sistema operativo](../../includes/container-service-install-dcos-cli-include.md)].


## Bilanciamento del carico.

In un cluster del servizio contenitore sono disponibili due livelli di bilanciamento del carico: il servizio di bilanciamento del carico di Azure per i punti di ingresso pubblici (quelli a cui accederanno gli utenti finali) e la soluzione marathon-lb sottostante che instrada le richieste in ingresso alle istanze del contenitore che gestiscono le richieste. Quando i contenitori che forniscono il servizio vengono ridimensionati, marathon-lb si adatterà in modo dinamico.

## Marathon LB 

La soluzione Marathon LB si riconfigurerà in modo dinamico in base ai contenitori distribuiti. È anche resiliente alla perdita di un contenitore o di un agente. In questi casi, Mesos riavvierà semplicemente il contenitore altrove e riconfigurerà Marathon LB.

Per installare Marathon LB, eseguire il comando seguente dal computer client:

```bash
dcos package install marathon-lb 
``` 

Ora che il pacchetto marathon-lb è disponibile, è possibile distribuire un semplice server Web usando la configurazione seguente:


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
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

Le operazioni chiave sono:
  * Impostare il valore di HAProxy\_0\_VHOST sul nome FQDN del servizio di bilanciamento del carico per gli agenti con il formato `<acsName>agents.<region>.cloudapp.azure.com`. Ad esempio, se è stato creato un cluster del servizio contenitore con il nome `myacs` nell'area `West US`, il nome FQDN sarà: `myacsagents.westus.cloudapp.azure.com`. Per trovarlo, è anche possibile cercare il servizio di bilanciamento del carico con "agent" nel nome quando si esaminano le risorse del gruppo di risorse creato per il servizio contenitore nel [portale di Azure](https://portal.azure.com).
  * Impostare servicePort su una porta >= 10.000. In questo modo viene identificato il servizio in esecuzione nel contenitore. Marathon-lb può così identificare i servizi tra cui eseguire il bilanciamento.
  * Impostare l'etichetta HAPROXY\_GROUP su "external".
  * Impostare hostPort su 0. In questo modo marathon allocherà arbitrariamente una porta disponibile.

Copiare questo codice JSON in un file denominato `hello-web.json` e usarlo per distribuire un contenitore:

```bash
dcos marathon app add hello-web.json 
``` 

## Servizio di bilanciamento del carico di Azure 

Per impostazione predefinita, il servizio di bilanciamento del carico di Azure espone le porte 80, 8080 e 443. Se si usa una di queste tre porte (come nell'esempio precedente), non è necessario eseguire alcuna operazione: dovrebbe essere possibile accedere al nome FQDN del servizio di bilanciamento del carico dell'agente e, ogni volta che si esegue l'aggiornamento, si accederà a uno dei tre server Web in modalità round robin. Se, tuttavia, si usa una porta diversa, è necessario aggiungere una regola di round robin e un probe nel servizio di bilanciamento del carico di Azure per la porta usata. Questa operazione può essere eseguita dall'[interfaccia della riga di comando XPLAT di Azure](../xplat-cli-azure-resource-manager.md) con i comandi `azure lb rule create` e `azure lb probe create`.


## Scenari aggiuntivi

È possibile avere uno scenario in cui si usano domini diversi per esporre servizi diversi, ad esempio:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

A tale scopo, vedere la pagina relativa agli [host virtuali](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), che illustra come associare i domini a percorsi di marathon-lb specifici.

In alternativa, è possibile esporre porte diverse e modificarne il mapping al servizio corretto dietro marathon lb, ad esempio:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432
 

## Passaggi successivi

Vedere [questo post di blog](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) per altre informazioni su Marathon LB.

<!---HONumber=AcomDC_0525_2016-->