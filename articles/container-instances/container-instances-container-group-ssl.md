---
title: Abilitare SSL in istanze di contenitore di Azure
description: Creare un endpoint SSL o TLS per un gruppo di contenitori in esecuzione in istanze di contenitore di Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 12de4ef31084d8ac8586c79ffe3d0a8e891727bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411390"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Abilitare un endpoint SSL in un gruppo di contenitori

Questo articolo illustra come creare un [gruppo di contenitori](container-instances-container-groups.md) con un contenitore di applicazione e un contenitore sidecar che eseguono un provider SSL. Configurazione di un gruppo di contenitori con un endpoint SSL separato, abilitare le connessioni SSL per l'applicazione senza modificare il codice dell'applicazione.

Illustra come configurare un gruppo di contenitori costituito da due contenitori:
* Un contenitore di applicazione che esegue una semplice app web usando Microsoft pubblici [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) immagine. 
* Un contenitore sidecar che esegue il pubblico [Nginx](https://hub.docker.com/_/nginx) immagine, configurata per utilizzare SSL. 

In questo esempio, il gruppo di contenitori espone solo la porta 443 per Nginx con il proprio indirizzo IP pubblico. Nginx indirizza le richieste HTTPS per l'app web complementare, che è in ascolto internamente sulla porta 80. È possibile adattare l'esempio per le app contenitore in ascolto su altre porte.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è consigliabile usare la versione 2.0.55 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per configurare Nginx come provider di SSL, è necessario un certificato SSL. Questo articolo illustra come creare e configurare un certificato SSL autofirmato. Per gli scenari di produzione, si dovrebbe ottenere un certificato da un'autorità di certificazione.

Per creare un certificato SSL autofirmato, usare il [OpenSSL](https://www.openssl.org/) dello strumento disponibili in Azure Cloud Shell e molte distribuzioni di Linux o usare uno strumento client confrontabile nel sistema operativo.

Prima di tutto creare una richiesta di certificato (file con estensione CSR) in una directory di lavoro locale:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Seguire le istruzioni per aggiungere le informazioni di identificazione. Nome comune immettere il nome host associato al certificato. Quando viene richiesta una password, premere INVIO senza digitare, per ignorare l'aggiunta di una password.

Eseguire il comando seguente per creare il certificato autofirmato (file con estensione CRT) dalla richiesta di certificato. Ad esempio:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Si noterà ora tre file nella directory: richiesta di certificato (`ssl.csr`), la chiave privata (`ssl.key`) e il certificato autofirmato (`ssl.crt`). Si utilizza `ssl.key` e `ssl.crt` nei passaggi successivi.

## <a name="configure-nginx-to-use-ssl"></a>Configurare Nginx per usare SSL

### <a name="create-nginx-configuration-file"></a>Creare il file di configurazione di Nginx

In questa sezione si crea un file di configurazione per Nginx usare SSL. Per iniziare, copiare il testo seguente in un nuovo file denominato`nginx.conf`. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```console
code nginx.conf
```

Nelle `location`, assicurarsi di impostare `proxy_pass` con la porta corretta per l'app. In questo esempio, abbiamo impostato la porta 80 per il `aci-helloworld` contenitore.

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

### <a name="base64-encode-secrets-and-configuration-file"></a>I segreti con codifica Base64 e file di configurazione

Codifica Base64 del file di configurazione Nginx, il certificato SSL e la chiave SSL. Nella sezione successiva, immettere il contenuto codificato in un file YAML usato per distribuire il gruppo di contenitori.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuire un gruppo di contenitori

Ora distribuire il gruppo di contenitori, specificando le configurazioni del contenitore in un [file YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Creare file YAML

Copiare il codice YAML seguente in un nuovo file denominato `deploy-aci.yaml`. In Azure Cloud Shell, è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```console
code deploy-aci.yaml
```

Immettere il contenuto della codifica base 64 file dove indicato sotto `secret`. Ad esempio, `cat` ogni file con codifica base64 per visualizzarne il contenuto. Durante la distribuzione, questi file vengono aggiunti a un [volume segreto](container-instances-volume-secret.md) nel gruppo di contenitori. In questo esempio, è montato il volume segreto per il contenitore Nginx.

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

Creare un gruppo di risorse con il [creare il gruppo di az](/cli/azure/group#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il gruppo di contenitori con il [contenitore di az creare](/cli/azure/container#az-container-create) comando, passando il file YAML come argomento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando seguente [show di contenitore di az](/cli/azure/container#az-container-show) comando:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Per una corretta distribuzione, l'output è simile al seguente:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verificare la connessione SSL

Per visualizzare l'applicazione in esecuzione, passare al relativo indirizzo IP nel browser. Ad esempio, l'indirizzo IP mostrato in questo esempio è `52.157.22.76`. È necessario usare `https://<IP-ADDRESS>` per visualizzare l'applicazione in esecuzione, a causa della configurazione del server Nginx. Prova a connettersi con `http://<IP-ADDRESS>` esito negativo.

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Poiché questo esempio Usa un certificato autofirmato e non uno da un'autorità di certificazione, il browser visualizza un avviso di sicurezza quando ci si connette al sito tramite HTTPS. Si tratta di un comportamento previsto.
>

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come configurare un contenitore Nginx per abilitare le connessioni SSL a un'app web in esecuzione nel gruppo di contenitori. È possibile adattare questo esempio per le app che sono in ascolto su porte diverse dalla porta 80. È anche possibile aggiornare il file di configurazione Nginx per reindirizzare automaticamente le connessioni al server sulla porta 80 (HTTP) per usare HTTPS.

Sebbene questo articolo Usa Nginx in sidecar, è possibile usare un altro provider SSL, ad esempio [Caddy](https://caddyserver.com/).

È un altro approccio per l'abilitazione di SSL in un gruppo di contenitori per distribuire il gruppo in un' [rete virtuale di Azure](container-instances-vnet.md) con un [gateway applicazione di Azure](../application-gateway/overview.md). Il gateway può essere configurato come un endpoint SSL. Vedere un campione [modello di distribuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) è possibile adattare per abilitare la terminazione SSL nel gateway.
