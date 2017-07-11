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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dc3ae52b1ec6717c7e19a160e3e7ea5d211f1f5f
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017



---

<a id="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes" class="xliff"></a>

# Usare Draft con il servizio contenitore di Azure e il Registro contenitori di Azure per compilare e distribuire un'applicazione in Kubernetes

[Draft](https://aka.ms/draft) è un nuovo strumento open source che semplifica lo sviluppo di applicazioni basate su contenitori e la loro distribuzione in cluster Kubernetes senza necessità di conoscere a fondo Docker e Kubernetes, né di installarli. Con strumenti come Draft, gli sviluppatori e i loro team possono concentrarsi sulla compilazione dell'applicazione con Kubernetes, senza fare molta attenzione all'infrastruttura.

È possibile usare Draft con qualsiasi registro di immagini Docker e cluster Kubernetes, anche in locale. Questa esercitazione mostra come usare il servizio contenitore di Azure con Kubernetes, il Registro contenitori di Azure e il servizio DNS di Azure per creare una pipeline di sviluppo CI/CD (integrazione continua/distribuzione continua) live con Draft.


<a id="create-an-azure-container-registry" class="xliff"></a>

## Creare un Registro contenitori di Azure
È possibile [creare un nuovo Registro contenitori di Azure](../container-registry/container-registry-get-started-azure-cli.md) facilmente, ma i passaggi sono i seguenti:

1. Creare un gruppo di risorse di Azure per gestire il Registro contenitori di Azure e il cluster Kubernetes nel servizio contenitore di Azure.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Creare un registro di immagini del Registro contenitori di Azure usando [az acr create](/cli/azure/acr#create)
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


<a id="create-an-azure-container-service-with-kubernetes" class="xliff"></a>

## Creare un servizio contenitore di Azure con Kubernetes

A questo punto, è possibile usare [az acs create](/cli/azure/acs#create) per creare un cluster ACS usando Kubernetes come valore `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
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

<a id="install-and-configure-draft" class="xliff"></a>

## Installare e configurare Draft
Le istruzioni di installazione di Draft sono disponibili nel [repository di Draft](https://github.com/Azure/draft/blob/master/docs/install.md). La procedura è relativamente semplice, ma richiede alcune operazioni di configurazione, in quanto dipende da [Helm](https://aka.ms/helm) per la creazione e la distribuzione di un grafico Helm nel cluster Kubernetes.

1. [Scaricare e installare Helm](https://aka.ms/helm#install).
2. Usare Helm per cercare e installare `stable/traefik` e il controller di ingresso per abilitare le richieste in ingresso per le compilazioni.
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Impostare quindi un'espressione di controllo sul controller `ingress` per acquisire il valore IP esterno quando viene distribuito. Questo indirizzo IP sarà quello [mappato al dominio di distribuzione](#wire-up-deployment-domain) nella sezione successiva.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    In questo caso, l'IP esterno per il dominio di distribuzione è `13.64.108.240`. È ora possibile mappare il dominio a tale IP.

<a id="wire-up-deployment-domain" class="xliff"></a>

## Collegare il dominio di distribuzione

Draft crea una versione per ogni grafico Helm creato, ovvero ogni applicazione a cui si sta lavorando. Ogni versione ottiene un nome generato, usato da Draft come _sottodominio_ del _dominio di distribuzione_ radice controllato dall'utente. In questo esempio viene usato `squillace.io` come dominio di distribuzione. Per abilitare questo comportamento del sottodominio, è necessario creare un record A per `'*'` nelle voci DNS per il dominio di distribuzione, in modo che venga eseguito il routing di ogni sottodominio generato al controller di ingresso del cluster Kubernetes.

Ogni provider di dominio assegna i server DNS in un modo specifico. Per [delegare i server dei nomi di dominio al servizio DNS di Azure](../dns/dns-delegate-domain-azure-dns.md), seguire questa procedura:

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
3. Aggiungere i server DNS ottenuti al provider di dominio per il dominio di distribuzione, per poter usare il servizio DNS di Azure per modificare il puntamento del dominio come desiderato.
4. Creare una voce di recordset A per il mapping del dominio di distribuzione all'IP `ingress` del passaggio 2 della sezione precedente.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
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
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Configurare Draft per usare il registro e creare sottodomini per ogni grafico Helm creato. Per configurare Draft sono necessari:
  - Il nome del Registro contenitori di Azure (in questo esempio, `draft`)
  - La chiave, o password, del registro da `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.
  - Il dominio di distribuzione radice configurato per il mapping all'indirizzo IP esterno di ingresso di Kubernetes (in questo caso, `squillace.io`)

  Chiamare `draft init`. Il processo di configurazione chiederà i valori indicati sopra. Alla prima esecuzione, il processo è simile al seguente.
    ```
    draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

A questo punto, è possibile distribuire un'applicazione.


<a id="build-and-deploy-an-application" class="xliff"></a>

## Compilare e distribuire un'applicazione

Nel repository di Draft ci sono [sei semplici applicazioni di esempio](https://github.com/Azure/draft/tree/master/examples). Clonare il repository e usare l'[esempio Python](https://github.com/Azure/draft/tree/master/examples/python). Passare alla directory examples/Python e digitare `draft create` per compilare l'applicazione. Il risultato dovrebbe essere simile al seguente.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

L'output include un Dockerfile e un grafico Helm. Per la compilazione e la distribuzione è sufficiente digitare `draft up`. L'output è vasto, ma inizia in modo analogo all'esempio seguente.
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

E, in caso di esito positivo, termina in modo analogo all'esempio seguente.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Qualunque sia il nome del grafico, è ora possibile usare il comando `curl http://gangly-bronco.squillace.io` per ricevere la risposta `Hello World!`.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

Ora che è stato creato un cluster Kubernetes ACS, è possibile provare a usare il [Registro contenitori di Azure](../container-registry/container-registry-intro.md) per creare altre distribuzioni diverse di questo scenario. È ad esempio possibile creare un recordset DNS di dominio draft._basedomain.toplevel_ che controlla le attività all'esterno di un sottodominio più profondo per distribuzioni specifiche del servizio contenitore di Azure.







