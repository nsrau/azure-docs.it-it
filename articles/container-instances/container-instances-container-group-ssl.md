---
title: Abilitare SSL in istanze di contenitore di Azure
description: Creare un endpoint SSL o TLS per un gruppo di contenitori in esecuzione in istanze di contenitore di Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: f11fb209f8d2ace51081fd81f453faf9505af27c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326077"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Abilitare un endpoint SSL in un gruppo di contenitori

Questo articolo illustra come creare un [gruppo di contenitori](container-instances-container-groups.md) con un contenitore di applicazioni e un contenitore sidecar che esegue un provider SSL. Impostando un gruppo di contenitori con un endpoint SSL separato, si abilitano le connessioni SSL per l'applicazione senza modificare il codice dell'applicazione.

Si configura un gruppo di contenitori costituito da due contenitori:
* Un contenitore di applicazioni che esegue una semplice app Web usando l'immagine pubblica Microsoft [ACI-HelloWorld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) . 
* Un contenitore sidecar che esegue l'immagine pubblica [nginx](https://hub.docker.com/_/nginx) , configurata per l'uso di SSL. 

In questo esempio, il gruppo di contenitori espone solo la porta 443 per Nginx con il relativo indirizzo IP pubblico. Nginx instrada le richieste HTTPS all'app Web complementare, che è in ascolto interna sulla porta 80. È possibile adattare l'esempio per le app contenitore che restano in attesa su altre porte.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è consigliabile usare la versione 2.0.55 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per configurare nginx come provider SSL, è necessario un certificato SSL. Questo articolo illustra come creare e configurare un certificato SSL autofirmato. Per gli scenari di produzione, è necessario ottenere un certificato da un'autorità di certificazione.

Per creare un certificato SSL autofirmato, usare lo strumento [openssl](https://www.openssl.org/) disponibile in Azure cloud Shell e molte distribuzioni Linux oppure usare uno strumento client analogo nel sistema operativo.

Creare prima di tutto una richiesta di certificato (file con estensione CSR) in una directory di lavoro locale:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Seguire le istruzioni per aggiungere le informazioni di identificazione. Per nome comune, immettere il nome host associato al certificato. Quando viene richiesta una password, premere INVIO senza digitare per ignorare l'aggiunta di una password.

Eseguire il comando seguente per creare il certificato autofirmato (file con estensione CRT) dalla richiesta del certificato. Ad esempio:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Verranno ora visualizzati tre file nella directory: la richiesta di certificato (`ssl.csr`), la chiave privata (`ssl.key`) e il certificato autofirmato (`ssl.crt`). Usare `ssl.key` e`ssl.crt` nei passaggi successivi.

## <a name="configure-nginx-to-use-ssl"></a>Configurare Nginx per l'uso di SSL

### <a name="create-nginx-configuration-file"></a>Creare il file di configurazione Nginx

In questa sezione viene creato un file di configurazione per Nginx per l'uso di SSL. Per iniziare, copiare il testo seguente in un nuovo file`nginx.conf`denominato. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```console
code nginx.conf
```

In `location`, assicurarsi di impostare `proxy_pass` con la porta corretta per l'app. In questo esempio viene impostata la porta 80 per il `aci-helloworld` contenitore.

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
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

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

### <a name="base64-encode-secrets-and-configuration-file"></a>File di configurazione e segreti con codifica Base64

Base64: codificare il file di configurazione Nginx, il certificato SSL e la chiave SSL. Nella sezione successiva si immetterà il contenuto codificato in un file YAML usato per distribuire il gruppo di contenitori.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuire un gruppo di contenitori

A questo punto, distribuire il gruppo di contenitori specificando le configurazioni del contenitore in un [file YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Crea file YAML

Copiare il codice YAML seguente in un nuovo file `deploy-aci.yaml`denominato. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```console
code deploy-aci.yaml
```

Immettere il contenuto dei file con codifica Base64, dove indicato in `secret`. Ad esempio, `cat` ognuno dei file con codifica Base64 per visualizzarne il contenuto. Durante la distribuzione, questi file vengono aggiunti a un [volume segreto](container-instances-volume-secret.md) nel gruppo di contenitori. In questo esempio il volume Secret viene montato nel contenitore nginx.

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

Creare un gruppo di risorse con il comando [AZ Group create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il gruppo di contenitori con il comando [AZ container create](/cli/azure/container#az-container-create) , passando il file YAML come argomento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [AZ container Show](/cli/azure/container#az-container-show) seguente:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Per una distribuzione corretta, l'output è simile al seguente:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verificare la connessione SSL

Per visualizzare l'applicazione in esecuzione, passare all'indirizzo IP nel browser. Ad esempio, l'indirizzo IP illustrato in questo esempio è `52.157.22.76`. È necessario usare `https://<IP-ADDRESS>` per visualizzare l'applicazione in esecuzione, a causa della configurazione del server nginx. Tentativi di connessione con `http://<IP-ADDRESS>` esito negativo.

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Poiché in questo esempio viene utilizzato un certificato autofirmato e non uno da un'autorità di certificazione, nel browser viene visualizzato un avviso di sicurezza quando si esegue la connessione al sito tramite HTTPS. Si tratta di un comportamento previsto.
>

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come configurare un contenitore Nginx per abilitare le connessioni SSL a un'app Web in esecuzione nel gruppo di contenitori. Questo esempio può essere adattato alle app che restano in ascolto su porte diverse dalla porta 80. È anche possibile aggiornare il file di configurazione Nginx per reindirizzare automaticamente le connessioni server sulla porta 80 (HTTP) per usare HTTPS.

Anche se in questo articolo viene usato nginx nel sidecar, è possibile usare un altro provider SSL, ad esempio [Caddy](https://caddyserver.com/).

Un altro approccio all'abilitazione di SSL in un gruppo di contenitori consiste nel distribuire il gruppo in una [rete virtuale di Azure](container-instances-vnet.md) con un [gateway applicazione di Azure](../application-gateway/overview.md). Il gateway può essere configurato come endpoint SSL. Vedere un [modello di distribuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) di esempio che è possibile adattare per abilitare la terminazione SSL nel gateway.
