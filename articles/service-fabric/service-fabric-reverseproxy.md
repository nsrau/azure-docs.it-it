<properties
   pageTitle="Proxy inverso di Service Fabric | Microsoft Azure"
   description="Usare un proxy inverso di Service Fabric per comunicare con i microservizi dall'interno e dall'esterno del cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman,vturecek"
   manager="timlt"
   editor="vturecek"/> 

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="vturecek"/>

# Proxy inverso di Service Fabric

Il proxy inverso di Service Fabric è un proxy inverso integrato in Service Fabric che consente di indirizzare microservizi nel cluster di Service Fabric che espongono endpoint HTTP.

## Modello di comunicazione di microservizi

I microservizi in Service Fabric sono in genere eseguiti in un sottoinsieme della VM nel cluster e possono spostarsi tra VM per vari motivi. Pertanto, gli endpoint per microservizi possono cambiare in modo dinamico. Il modello tipico di comunicazione con il microservizio è il ciclo di risoluzione riportato di seguito:

1. Risolvere la posizione del servizio inizialmente tramite il servizio Naming.
2. Connettersi al servizio.
3. Determinare la causa degli errori di connessione e risolvere nuovamente la posizione del servizio quando necessario.

Questo processo implica in genere il wrapping delle librerie di comunicazione lato client in un ciclo di nuovi tentativi che implementa la risoluzione del servizio e i criteri di ripetizione. Per altre informazioni su questo argomento, vedere [Connettersi e comunicare con i servizi in Service Fabric](service-fabric-connect-and-communicate-with-services.md).

### Comunicazione attraverso il proxy inverso di SF
Il proxy inverso di Service Fabric viene eseguito in tutti i nodi del cluster. Esegue l'intero processo di risoluzione del servizio per conto di un client e quindi inoltra la richiesta del client. Pertanto, i client in esecuzione nel cluster possono usare solo le librerie di comunicazione HTTP lato client per comunicare con il servizio di destinazione tramite il proxy inverso di SF in esecuzione in locale nello stesso nodo.

![Comunicazione interna][1]

## Raggiungere i microservizi dall'esterno del cluster
Il modello di comunicazione esterna predefinito per i microservizi è **consenso**. Per impostazione predefinita, non è quindi possibile accedere a un servizio direttamente da client esterni. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) è un limite di rete tra microservizi e client esterni, che esegue la funzionalità NAT (Network Address Translation) e inoltra le richieste esterne agli endpoint interni **IP:port**. Per rendere un endpoint del microservizio direttamente accessibile ai client esterni, è prima necessario configurare Azure Load Balancer per l'inoltro del traffico a ogni porta usata dal servizio nel cluster. Inoltre, molti microservizi (specialmente i microservizi con stato) non risiedono in tutti i nodi del cluster e possono spostarsi tra i nodi in caso di failover. In questi casi Azure Load Balancer non può quindi determinare in modo efficace il nodo di destinazione delle repliche a cui inoltrare il traffico.

### Raggiungere i microservizi tramite il proxy inverso di SF dall'esterno del cluster

Anziché configurare porte singole del servizio in Azure Load Balancer, è possibile configurare solo la porta del proxy inverso di SF in Azure Load Balancer. Ciò permette ai client esterni al cluster di raggiungere i servizi interni tramite il proxy inverso senza configurazioni aggiuntive.

![Comunicazione esterna][0]

>[AZURE.WARNING] La configurazione della porta del proxy inverso sul bilanciamento del carico rende tutti i microservizi nel cluster che espongono endpoint HTTP indirizzabili dall'esterno del cluster.


## Formato URI di indirizzamento dei servizi tramite il proxy inverso

Il proxy inverso usa un formato URI specifico per identificare la partizione di servizio alla quale inoltrare la richiesta in ingresso:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http(s):** il proxy inverso può essere configurato per accettare il traffico HTTP o HTTPS. In caso di traffico HTTPS, la terminazione SSL si verifica in corrispondenza del proxy inverso. Le richieste dal proxy inverso ai servizi del cluster sono inoltrate tramite HTTP.
 - **FQDN del cluster| internal IP:** For external clients, the reverse proxy can be configured so that it is reachable through the cluster domain (e.g., mycluster.eastus.cloudapp.azure.com). By default the reverse proxy runs on every node, so for internal traffic it can be reached on localhost or on any internal node IP (e.g., 10.0.0.1).
 - **Port:** la porta che è stata specificata per il proxy inverso. Ad esempio: 19008.
 - **ServiceInstanceName:** questo è il nome dell'istanza del servizio distribuito completo che si sta tentando di raggiungere senza lo schema "fabric:/". Ad esempio, per raggiungere il servizio *fabric:/myapp/myservice/*, si usa *myapp/myservice*.
 - **Suffix path:** si tratta del percorso URL effettivo per il servizio al quale si desidera connettersi. Ad esempio, *myapi/values/add/3*
 - **PartitionKey:** per un servizio partizionato, questa è la chiave di partizione calcolata della partizione che si desidera raggiungere. Si noti che questo *non* è il GUID dell'ID di partizione. Questo parametro non è obbligatorio per i servizi che usano lo schema di partizione singleton.
 - **PartitionKind:** lo schema di partizione del servizio. Può essere "Int64Range" o "Named". Questo parametro non è obbligatorio per i servizi che usano lo schema di partizione singleton.
 - **Timeout:** specifica il timeout per la richiesta HTTP creata dal proxy inverso per il servizio per conto della richiesta del client. Il valore predefinito è 60 secondi. Questo è un parametro facoltativo.

### Esempio di utilizzo

Si prenda come esempio il servizio **fabric:/MyApp/MyService** che apre un listener HTTP nell'URL seguente:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Con le risorse seguenti:

 - `/index.html` 
 - `/api/users/<userId>`

Se il servizio usa lo schema di partizionamento singleton, i parametri di stringa di query *PartitionKey* e *PartitionKind* non sono richiesti e il servizio può essere raggiunto tramite il gateway:

 - Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internamente: `http://localhost:19008/MyApp/MyService`

Se il servizio usa lo schema di partizionamento Uniform Int64, è necessario usare i parametri di stringa di query *PartitionKey* e *PartitionKind* per raggiungere una partizione del servizio:

 - Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Per raggiungere le risorse esposte dal servizio, è sufficiente inserire il percorso della risorsa dopo il nome del servizio nell'URL:

 - Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Il gateway inoltrerà quindi queste richieste all'URL del servizio:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html` 
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## Gestione speciale per servizi di condivisione porta

Il gateway applicazione tenta di risolvere di nuovo un indirizzo del servizio e ripetere la richiesta quando non è possibile raggiungere un servizio. Questo è uno dei principali vantaggi del gateway, dal momento che il codice client non necessita di implementare la propria risoluzione del servizio e il proprio ciclo di risoluzione.

Quando un servizio non può essere raggiunto, significa in genere che la replica o l'istanza del servizio si è spostata in un nodo diverso come parte del ciclo di vita normale. In questo caso, il gateway potrebbe ricevere un errore di connessione di rete che indica che un endpoint non è più aperto sull'indirizzo risolto in origine.

Tuttavia, le repliche o le istanze del servizio possono condividere un processo host e una porta quando ospitate da un server Web basato su http.sys, tra cui:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

In questa circostanza è probabile che il server Web sia disponibile nel processo host e che risponda alle richieste, ma la replica o l'istanza del servizio risolto non è più disponibile nell'host. In questo caso il gateway riceve una risposta HTTP 404 dal server Web. Di conseguenza, un errore HTTP 404 presenta due significati distinti:

 1. L'indirizzo del servizio è corretto, ma la risorsa richiesta dall'utente non esiste.
 2. L'indirizzo del servizio non è corretto e la risorsa richiesta dall'utente potrebbe essere presente in un nodo diverso.

Nel primo caso si tratta di una normale situazione HTTP 404, che viene considerata come un errore dell'utente. Nel secondo caso l'utente ha richiesto una risorsa esistente ma che il gateway non è riuscito a individuare a causa dello spostamento del servizio stesso. Il gateway deve quindi risolvere di nuovo l'indirizzo e riprovare.

Il gateway necessita quindi di un modo per distinguere tra questi due casi. Per eseguire questa distinzione, è necessario un suggerimento dal server.

 - Per impostazione predefinita, il gateway presuppone la sussistenza del caso n. 2 e tenta di risolvere di nuovo l'indirizzo e inviare nuovamente la richiesta.
 - Per indicare il caso n.1 al gateway applicazione, il servizio deve restituire l'intestazione della risposta HTTP seguente:

`X-ServiceFabric : ResourceNotFound` 

L'intestazione della risposta HTTP indica una situazione HTTP 404 normale, in cui la risorsa richiesta non esiste e il gateway non tenta di risolvere di nuovo l'indirizzo del servizio.

## Installazione e configurazione
Il proxy inverso di Service Fabric può essere abilitato per il cluster tramite il [modello di Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md).

Dopo aver ottenuto il modello per il cluster che si desidera distribuire (da modelli di esempio o creando un modello di Resource Manager personalizzato), il proxy inverso può essere abilitato nel modello tramite la procedura seguente.

1. Definire una porta per il proxy inverso nella [sezione dei parametri](../resource-group-authoring-templates.md) del modello.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Specificare la porta per ogni oggetto nodetype nella sezione **Cluster** [Tipo di risorsa](../resource-group-authoring-templates.md)

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Per fare riferimento al proxy inverso dall'esterno del cluster di Azure, configurare le **regole di Azure Load Balancer** per la porta specificata nel passaggio 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Per configurare i certificati SSL sulla porta per il proxy inverso, aggiungere il certificato alla proprietà httpApplicationGatewayCertificate nella [sezione del tipo di risorsa](../resource-group-authoring-templates.md) del **cluster**

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## Passaggi successivi
 - Vedere un esempio di comunicazione HTTP tra i servizi in un [progetto di esempio in GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chiamate di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)

 - [Web API che usa OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicazione di WCF tramite Reliable Services](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

<!---HONumber=AcomDC_0921_2016-->