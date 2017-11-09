---
title: Usare Draft con il servizio contenitore di Azure e il Registro contenitori di Azure | Microsoft Docs
description: Creare un cluster Kubernetes ACS e un Registro contenitori di Azure per creare la prima applicazione in Azure con Draft.
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: Docker, contenitori, microservizi, Kubernetes, Draft, Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 08fd66ed68b651bd24bc1bc58ec9631106665381
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Usare Draft con il servizio contenitore di Azure e il Registro contenitori di Azure per compilare e distribuire un'applicazione in Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) è un nuovo strumento open source che semplifica lo sviluppo di applicazioni basate su contenitori e la loro distribuzione in cluster Kubernetes senza necessità di conoscere a fondo Docker e Kubernetes, né di installarli. Con strumenti come Draft, gli sviluppatori e i loro team possono concentrarsi sulla compilazione dell'applicazione con Kubernetes, senza fare molta attenzione all'infrastruttura.

È possibile usare Draft con qualsiasi registro di immagini Docker e cluster Kubernetes, anche in locale. Questa esercitazione illustra come usare il servizio contenitore di Azure con Kubernetes e Registro contenitori di Azure per creare una pipeline di sviluppo attiva ma protetta in Kubernetes usando Draft e come usare il servizio DNS di Azure per consentire la visualizzazione di tale pipeline di sviluppo agli altri utenti in un dominio.


## <a name="create-an-azure-container-registry"></a>Creare un Registro contenitori di Azure
È possibile [creare un nuovo Registro contenitori di Azure](../../container-registry/container-registry-get-started-azure-cli.md) facilmente, ma i passaggi sono i seguenti:

1. Creare un gruppo di risorse di Azure per gestire il Registro contenitori di Azure e il cluster Kubernetes nel servizio contenitore di Azure.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Creare un registro di immagini del Registro contenitori di Azure usando [az acr create](/cli/azure/acr#create) e verificare che l'opzione `--admin-enabled` sia impostata su `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Creare un servizio contenitore di Azure con Kubernetes

A questo punto, è possibile usare [az acs create](/cli/azure/acs#create) per creare un cluster ACS usando Kubernetes come valore `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Poiché Kubernetes non è il tipo di agente di orchestrazione predefinito, assicurarsi di usare l'opzione `--orchestrator-type kubernetes`.

L'output, in caso di esito positivo, è simile al seguente.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Dopo aver creato un cluster, è possibile importare le credenziali usando il comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). A questo punto, si ha un file di configurazione locale per il cluster, necessario per Helm e Draft.

## <a name="install-and-configure-draft"></a>Installare e configurare Draft


1. Scaricare Draft per il proprio ambiente all'indirizzo https://github.com/Azure/draft/releases e installarlo in PATH in modo da poter usare il comando.
2. Scaricare Helm per il proprio ambiente all'indirizzo https://github.com/kubernetes/helm/releases e [installarlo in PATH in modo da poter usare il comando](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Configurare Draft per usare il registro e creare sottodomini per ogni grafico Helm creato. Per configurare Draft sono necessari:
  - Il nome del Registro contenitori di Azure (in questo esempio, `draftacsdemo`)
  - La chiave, o password, del registro da `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Chiamare `draft init`. Il processo di configurazione richiede i valori sopra indicati. Si noti che il formato dell'URL per l'URL del registro è il nome del registro (in questo esempio, `draftacsdemo`) più `.azurecr.io`. Il nome utente è il nome del registro da solo. Alla prima esecuzione, il processo è simile al seguente.
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

A questo punto, è possibile distribuire un'applicazione.


## <a name="build-and-deploy-an-application"></a>Compilare e distribuire un'applicazione

Nel repository di Draft ci sono [sei semplici applicazioni di esempio](https://github.com/Azure/draft/tree/master/examples). Clonare il repository e usare l'[esempio Java](https://github.com/Azure/draft/tree/master/examples/java). Passare alla directory examples/java e digitare `draft create` per compilare l'applicazione. Il risultato dovrebbe essere simile al seguente.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

L'output include un Dockerfile e un grafico Helm. Per la compilazione e la distribuzione è sufficiente digitare `draft up`. L'output è esteso, ma dovrebbe essere simile all'esempio seguente.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Visualizzare in modo sicuro l'applicazione

Il contenitore viene ora eseguito in servizio contenitore di Azure. Per visualizzarlo, usare il comando `draft connect` che crea una connessione protetta all'indirizzo IP del cluster con una porta specifica per l'applicazione in modo da consentire la visualizzazione in locale. Se ha esito positivo, cercare l'URL per la connessione all'app nella prima riga dopo l'indicatore **SUCCESS**.

> [!NOTE]
> Se si riceve un messaggio che informa che nessun pod è pronto, attendere e riprovare oppure controllare quando i pod sono pronti con `kubectl get pods -w` e quindi riprovare.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Nell'esempio precedente è possibile digitare `curl -s http://localhost:46143` per ricevere la risposta, `Hello World, I'm Java!`. Premendo CTRL+ o CMD+C (a seconda del sistema operativo), il tunnel sicuro viene eliminato ed è possibile continuare a eseguire iterazioni.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Condivisione dell'applicazione tramite la configurazione di un dominio di distribuzione con DNS di Azure

È già stato eseguito il ciclo di iterazione di sviluppo creato da Draft nei passaggi precedenti. Tuttavia, è possibile condividere l'applicazione su Internet eseguendo queste operazioni:
1. Installazione di un ingresso nel cluster del servizio contenitore di Azure (per fornire un indirizzo IP pubblico per visualizzare l'app)
2. Delega del dominio personalizzato a DNS di Azure e mapping del dominio all'indirizzo IP assegnato dal servizio contenitore di Azure al controller di ingresso

### <a name="use-helm-to-install-the-ingress-controller"></a>Usare Helm per installare il controller di ingresso.
Usare **Helm** per cercare e installare `stable/traefik` e il controller di ingresso per abilitare le richieste in ingresso per le compilazioni.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Impostare quindi un'espressione di controllo sul controller `ingress` per acquisire il valore IP esterno quando viene distribuito. Questo indirizzo IP sarà quello [mappato al dominio di distribuzione](#wire-up-deployment-domain) nella sezione successiva.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

In questo caso, l'IP esterno per il dominio di distribuzione è `13.64.108.240`. È ora possibile mappare il dominio a tale IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mappare l'indirizzo IP dell'ingresso a un sottodominio personalizzato

Draft crea una versione per ogni grafico Helm creato, ovvero ogni applicazione a cui si sta lavorando. Ogni versione ottiene un nome generato, usato da **Draft** come _sottodominio_ del _dominio di distribuzione_ radice controllato dall'utente. In questo esempio viene usato `squillace.io` come dominio di distribuzione. Per abilitare questo comportamento del sottodominio, è necessario creare un record A per `'*.draft'` nelle voci DNS per il dominio di distribuzione, in modo che venga eseguito il routing di ogni sottodominio generato al controller di ingresso del cluster Kubernetes. 

Ogni provider di dominio assegna i server DNS in un modo specifico. Per [delegare i server dei nomi di dominio al servizio DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md), seguire questa procedura:

1. Creare un gruppo di risorse per la propria zona.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Creare una zona DNS per il proprio dominio.
Usare il comando [az network dns zone create](/cli/azure/network/dns/zone#create) per ottenere i server dei nomi per delegare il controllo DNS al servizio DNS di Azure per il dominio.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Aggiungere i server DNS ottenuti al provider di dominio per il dominio di distribuzione, per poter usare il servizio DNS di Azure per modificare il puntamento del dominio come desiderato. Il modo in cui si esegue questa operazione varia in base al dominio specificato. L'articolo [Delegare un dominio al servizio DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md) contiene alcune informazioni che è necessario conoscere. 
4. Dopo aver delegato il dominio al servizio DNS di Azure, creare una voce di recordset A per il mapping del dominio di distribuzione all'indirizzo IP `ingress` del passaggio 2 della sezione precedente.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
  ```
L'output è simile al seguente:
  ```json
  {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Reinstallare **Draft**

   1. Rimuovere **draftd** dal cluster digitando `helm delete --purge draft`. 
   2. Reinstallare **Draft** usando lo stesso comando `draft-init`, ma con l'opzione `--ingress-enabled`:
    ```bash
    draft init --ingress-enabled
    ```
   Rispondere alle richieste, come la prima volta. Occorre tuttavia rispondere a un'altra domanda usando il percorso completo del dominio configurato con DNS di Azure.

6. Immettere il dominio di primo livello per il traffico in ingresso (ad esempio draft.example.com): draft.squillace.io
7. Quando si chiama `draft up` questa volta, sarà possibile visualizzare l'applicazione (o `curl`) nell'URL nel formato `<appname>.draft.<domain>.<top-level-domain>`. Nel caso descritto in questo esempio, `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un cluster Kubernetes ACS, è possibile provare a usare il [Registro contenitori di Azure](../../container-registry/container-registry-intro.md) per creare altre distribuzioni diverse di questo scenario. È ad esempio possibile creare un recordset DNS di dominio draft._basedomain.toplevel_ che controlla le attività all'esterno di un sottodominio più profondo per distribuzioni specifiche del servizio contenitore di Azure.






