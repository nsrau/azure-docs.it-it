---
title: Esempio di esecuzione del contenitore del comando Docker Run
titleSuffix: Azure Cognitive Services
description: Comando Docker Run per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: b19fb3f86be46a5db60fb87f9c7f5c3e28ac6428
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965147"
---
## <a name="install-the-container"></a>Installare il contenitore

Esistono diversi modi per installare ed eseguire il Analisi del testo per il contenitore di integrità. 

- Usare il [portale di Azure](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) per creare una risorsa analisi del testo e usare Docker per ottenere il contenitore.
- Usare i seguenti script PowerShell e l'interfaccia della riga di comando di Azure per automatizzare la distribuzione delle risorse e la configurazione del contenitore

### <a name="run-the-container-locally"></a>Eseguire il contenitore in locale

Per eseguire il contenitore nel proprio ambiente dopo il download dell'immagine del contenitore, trovare il relativo ID immagine:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Eseguire il `docker run` comando seguente. Sostituire i segnaposto seguenti con valori personalizzati:

| Segnaposto | Valore | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave per la risorsa Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Endpoint per l'accesso alla API Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | ID immagine per il contenitore. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Directory di input per il contenitore. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Questo comando:

- Presuppone che la directory di input esista nel computer host
- Esegue un Analisi del testo per il contenitore di integrità dall'immagine del contenitore
- Alloca 6 core CPU e 12 gigabyte (GB) di memoria
- Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
- Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

### <a name="demo-ui-to-visualize-output"></a>Interfaccia utente demo per visualizzare l'output

> [!NOTE]
> La demo è disponibile solo con il contenitore di Analisi del testo per l'integrità.

Il contenitore fornisce le API dell'endpoint di stima della query basata su REST.  È stato inoltre fornito uno strumento di visualizzazione nel contenitore accessibile aggiungendo `/demo` all'endpoint del contenitore. Ad esempio:

```
http://<serverURL>:5000/demo
```

Usare la richiesta cURL di esempio riportata di seguito per inviare una query al contenitore distribuito sostituendo la `serverURL` variabile con il valore appropriato.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installare il contenitore con Azure app Web per contenitori

Azure [app Web per contenitori](https://azure.microsoft.com/services/app-service/containers/) è una risorsa di Azure dedicata all'esecuzione di contenitori nel cloud. Offre funzionalità predefinite, come la scalabilità automatica, il supporto di contenitori Docker e Docker compose, il supporto HTTPS e molto altro ancora.

> [!NOTE]
> Con l'app Web di Azure verrà automaticamente ottenuto un dominio nel formato `<appservice_name>.azurewebsites.net`

Eseguire questo script di PowerShell usando l'interfaccia della riga di comando di Azure per creare una app Web per contenitori, usando la sottoscrizione e l'immagine del contenitore su HTTPS. Attendere il completamento dello script (circa 25-30 minuti) prima di inviare la prima richiesta.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installare il contenitore usando l'istanza di contenitore di Azure

È anche possibile usare un'istanza di contenitore di Azure (ACI) per semplificare la distribuzione. ACI è una risorsa che consente di eseguire i contenitori Docker su richiesta in un ambiente Azure gestito senza server. 

Vedere [come usare istanze di contenitore di Azure](../how-tos/text-analytics-how-to-use-container-instances.md) per i passaggi per la distribuzione di una risorsa ACI usando il portale di Azure. È anche possibile usare lo script di PowerShell seguente usando l'interfaccia della riga di comando di Azure, che creerà un ACI nella sottoscrizione usando l'immagine del contenitore.  Attendere il completamento dello script (circa 25-30 minuti) prima di inviare la prima richiesta.  A causa del limite per il numero massimo di CPU per ogni risorsa ACI, non selezionare questa opzione se si prevede di inviare più di 5 documenti di grandi dimensioni (circa 5000 caratteri ciascuno) per ogni richiesta.
Per informazioni sulla disponibilità, vedere l'articolo del [supporto tecnico regionale di ACI](../../../container-instances/container-instances-region-availability.md) . 

> [!NOTE] 
> Le istanze di contenitore di Azure non includono il supporto HTTPS per i domini predefiniti. Se è necessario HTTPS, sarà necessario configurarlo manualmente, inclusa la creazione di un certificato e la registrazione di un dominio. È possibile trovare le istruzioni per eseguire questa operazione con NGINX sotto.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Connettività ACI sicura

Per impostazione predefinita, quando si usa ACI con l'API contenitore non viene fornita alcuna sicurezza. Questo perché in genere i contenitori vengono eseguiti come parte di un pod protetto dall'esterno da un Bridge di rete. È tuttavia possibile modificare un contenitore con un componente front-end, mantenendo l'endpoint del contenitore privato. Gli esempi seguenti usano [nginx](https://www.nginx.com) come gateway di ingresso per supportare HTTPS/SSL e l'autenticazione del certificato client.

> [!NOTE]
> NGINX è un proxy e un server HTTP open source a prestazioni elevate. Un contenitore NGINX può essere usato per terminare una connessione TLS per un singolo contenitore. Sono possibili anche le soluzioni di terminazione TLS basate sul traffico in ingresso NGINX più complesse.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurare NGINX come gateway di ingresso

NGINX USA [i file di configurazione](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) per abilitare le funzionalità in fase di esecuzione. Per abilitare la terminazione TLS per un altro servizio, è necessario specificare un certificato SSL per terminare la connessione TLS e  `proxy_pass` specificare un indirizzo per il servizio. Di seguito è riportato un esempio.


> [!NOTE]
> `ssl_certificate` prevede che un percorso sia specificato all'interno del file system locale del contenitore NGINX. L'indirizzo specificato per `proxy_pass` deve essere disponibile nella rete del contenitore nginx.

Il contenitore NGINX caricherà tutti i file in `_.conf_` montati nel `/etc/nginx/conf.d/` percorso di configurazione http.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>File Docker compose di esempio

L'esempio seguente illustra come creare un file [Docker compose](https://docs.docker.com/compose/reference/overview) per distribuire NGINX e analisi del testo per i contenitori di integrità:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Per avviare il file Docker compose, eseguire il comando seguente da una console a livello di radice del file:

```bash
docker-compose up
```

Per ulteriori informazioni, vedere la documentazione di NGINX sulla [terminazione di nginx SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).

