---
title: Abilitare SSL con contenitore sidecar
description: Creare un endpoint SSL o TLS per un gruppo di contenitori in esecuzione nelle istanze del contenitore di Azure eseguendo Nginx in un contenitore sidecarCreate an SSL or TLS endpoint for a container group running in Azure Container Instances by running Nginx in a sidecar container
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294955"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>Abilitare un endpoint SSL in un contenitore sidecarEnable an SSL endpoint in a sidecar container

Questo articolo illustra come creare un gruppo di [contenitori](container-instances-container-groups.md) con un contenitore di applicazioni e un contenitore sidecar che esegue un provider SSL. Configurando un gruppo di contenitori con un endpoint SSL separato, si abilitano le connessioni SSL per l'applicazione senza modificare il codice dell'applicazione.

Impostare un gruppo di contenitori di esempio costituito da due contenitori:You set up an example container group consisting of two containers:
* Contenitore di applicazioni che esegue una semplice app Web usando l'immagine pubblica di Microsoft [aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Contenitore sidecar che esegue l'immagine [Nginx](https://hub.docker.com/_/nginx) pubblica, configurata per l'utilizzo di SSL. 

In questo esempio, il gruppo di contenitori espone solo la porta 443 per Nginx con il relativo indirizzo IP pubblico. Nginx indirizza le richieste HTTPS all'app Web complementare, che ascolta internamente sulla porta 80. È possibile adattare l'esempio per le app contenitore in ascolto su altre porte. 

Vedere [Passaggi successivi](#next-steps) per altri approcci per abilitare SSL in un gruppo di contenitori.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è consigliabile usare la versione 2.0.55 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](/cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per configurare Nginx come provider SSL, è necessario un certificato SSL. In questo articolo viene illustrato come creare e configurare un certificato SSL autofirmato. Per gli scenari di produzione, è necessario ottenere un certificato da un'autorità di certificazione.

Per creare un certificato SSL autofirmato, usare lo strumento [OpenSSL](https://www.openssl.org/) disponibile in Azure Cloud Shell e in molte distribuzioni Linux oppure uno strumento client comparabile nel sistema operativo.

Creare innanzitutto una richiesta di certificato (file con estensione csr) in una directory di lavoro locale:First create a certificate request (.csr file) in a local working directory:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Seguire le istruzioni visualizzate per aggiungere le informazioni di identificazione. In Nome comune immettere il nome host associato al certificato. Quando viene richiesta una password, premere INVIO senza digitare per ignorare l'aggiunta di una password.

Eseguire il comando seguente per creare il certificato autofirmato (file con estensione crt) dalla richiesta di certificato. Ad esempio:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Verranno visualizzati tre file nella directory:`ssl.csr`la richiesta`ssl.key`di certificato ( ),`ssl.crt`la chiave privata ( ) e il certificato autofirmato ( ). Utilizzare `ssl.key` e `ssl.crt` nei passaggi successivi.

## <a name="configure-nginx-to-use-ssl"></a>Configurare Nginx per l'utilizzo di SSL

### <a name="create-nginx-configuration-file"></a>Creare un file di configurazione NginxCreate Nginx configuration file

In questa sezione viene creato un file di configurazione per Nginx per l'utilizzo di SSL. Iniziare copiando il testo seguente `nginx.conf`in un nuovo file denominato . In Azure Cloud Shell è possibile usare Visual Studio Code per creare il file nella directory di lavoro:In Azure Cloud Shell, you can use Visual Studio Code to create the file in your working directory:

```console
code nginx.conf
```

In `location`, assicurati `proxy_pass` di impostare con la porta corretta per la tua app. In questo esempio viene impostata `aci-helloworld` la porta 80 per il contenitore.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Codifica base64 dei segreti e del file di configurazione

Codifica Base64 il file di configurazione Nginx, il certificato SSL e la chiave SSL. Nella sezione successiva immettere il contenuto codificato in un file YAML utilizzato per distribuire il gruppo di contenitori.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuire il gruppo di contenitoriDeploy container group

Distribuire ora il gruppo di contenitori specificando le configurazioni del contenitore in un [file YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Crea file YAML

Copiare il seguente YAML `deploy-aci.yaml`in un nuovo file denominato . In Azure Cloud Shell è possibile usare Visual Studio Code per creare il file nella directory di lavoro:In Azure Cloud Shell, you can use Visual Studio Code to create the file in your working directory:

```console
code deploy-aci.yaml
```

Immettere il contenuto dei file con codifica `secret`base64 dove indicato in . Ad esempio, `cat` ognuno dei file con codifica base64 per visualizzarne il contenuto. Durante la distribuzione, questi file vengono aggiunti a un [volume segreto](container-instances-volume-secret.md) nel gruppo di contenitori. In questo esempio, il volume segreto viene montato nel contenitore Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Distribuire il gruppo di contenitori

Creare un gruppo di risorse con il comando [az group create:Create](/cli/azure/group#az-group-create) a resource group with the az group create command:

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Distribuire il gruppo di contenitori con il comando [az container create,](/cli/azure/container#az-container-create) passando il file YAML come argomento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [az container show](/cli/azure/container#az-container-show) seguente:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Per una distribuzione corretta, l'output è simile al seguente:For a successful deployment, output is similar to the following:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verificare la connessione SSL

Utilizzare il browser per passare all'indirizzo IP pubblico del gruppo di contenitori. L'indirizzo IP mostrato `52.157.22.76`in questo esempio **https://52.157.22.76**è , quindi l'URL è . È necessario utilizzare HTTPS per visualizzare l'applicazione in esecuzione, a causa della configurazione del server Nginx. I tentativi di connessione tramite HTTP hanno esito negativo.

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Poiché in questo esempio viene utilizzato un certificato autofirmato e non uno di un'autorità di certificazione, il browser visualizza un avviso di sicurezza quando ci si connette al sito tramite HTTPS. Potrebbe essere necessario accettare l'avviso o modificare le impostazioni del browser o del certificato per passare alla pagina. Si tratta di un comportamento previsto.

>

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come configurare un contenitore Nginx per abilitare le connessioni SSL a un'app Web in esecuzione nel gruppo di contenitori. È possibile adattare questo esempio per le app in ascolto su porte diverse dalla porta 80.You can adapt this example for apps that listen on ports other than port 80. È inoltre possibile aggiornare il file di configurazione Nginx per reindirizzare automaticamente le connessioni server sulla porta 80 (HTTP) per utilizzare HTTPS.

Mentre in questo articolo viene utilizzato Nginx nel sidecar, è possibile utilizzare un altro provider SSL, ad esempio [Caddy](https://caddyserver.com/).

Se si distribuisce il gruppo di contenitori in una rete virtuale di Azure, è possibile considerare altre opzioni per abilitare un endpoint SSL per un'istanza del contenitore back-end, tra cui:If you deploy your container group in an [Azure virtual network,](container-instances-vnet.md)you can consider other options to enable an SSL endpoint for a backend container instance, including:

* [Proxy di funzioni di AzureAzure Functions Proxies](../azure-functions/functions-proxies.md)
* [Gestione API di Azure](../api-management/api-management-key-concepts.md)
* [Gateway applicazione di Azure:](../application-gateway/overview.md) vedere un modello di [distribuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)di esempio.
