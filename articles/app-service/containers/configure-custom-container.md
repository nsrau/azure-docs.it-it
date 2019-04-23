---
title: Configurare i contenitori dei clienti - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare le app Node. js per lavorare in servizio App di Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001882"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurare un contenitore Linux personalizzato per il servizio App di Azure

Questo articolo illustra come configurare un contenitore Linux personalizzato per l'esecuzione nel servizio App di Azure.

Questa guida fornisce i concetti chiave e istruzioni per l'utilizzo dei contenitori delle app di Linux nel servizio App. Se non si è mai usato il servizio App di Azure, seguire le [contenitore personalizzato quickstart](quickstart-docker-go.md) e [esercitazione](tutorial-custom-docker-image.md) prima. È inoltre disponibile un' [Guida introduttiva alle app multi-contenitore](quickstart-multi-container.md) e [esercitazione](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Configurare il numero di porta

Il server web in un'immagine personalizzata può usare una porta diversa da 80. Comunicare ad Azure la porta usata personalizzata usando il `WEBSITES_PORT` impostazione dell'app. La pagina di GitHub per l'[esempio di Python in questa esercitazione](https://github.com/Azure-Samples/docker-django-webapp-linux) indica che è necessario impostare `WEBSITES_PORT` su _8000_. È possibile impostarlo eseguendo [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando in Cloud Shell. Ad esempio: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Contenitore personalizzato può utilizzare le variabili di ambiente che devono essere fornite esternamente. È possibile passarli in eseguendo [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando in Cloud Shell. Ad esempio: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Questo metodo funziona sia per le app di singolo contenitore o multi-contenitore, in cui le variabili di ambiente vengono specificate nel *docker-Compose. yml* file.

## <a name="use-persistent-shared-storage"></a>Usare l'archiviazione condivisa permanente

È possibile usare la */Home* directory nel file system dell'app per rendere persistenti i file tra i riavvii e condividerli tra istanze. Il `/home` nell'app viene fornita per consentire all'app contenitore di accedere a un archivio permanente.

Quando un archivio permanente è disabilitato e quindi scrive il `/home` directory non sono persistenti nei riavvii dell'app o tra più istanze. L'unica eccezione è il `/home/LogFiles` directory, che viene usato per archiviare i log di Docker e un contenitore. Quando è abilitato nell'archivio permanente, tutte le operazioni di scrittura di `/home` directory vengono rese persistenti e sono accessibili da tutte le istanze di un'app con scalabilità orizzontale.

Per impostazione predefinita, è un archivio permanente *disabilitato*. Per abilitarlo o disabilitarlo, impostare il `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app eseguendo [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando in Cloud Shell. Ad esempio: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> È anche possibile [configurare il proprio archivio permanente](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Abilitare SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Affinché un contenitore personalizzato supportare SSH, è necessario aggiungerlo in Dockerfile stesso.

> [!TIP]
> Tutti i contenitori Linux predefiniti sono aggiunte le istruzioni SSH nei loro repository immagini. È possibile scorrere le istruzioni seguenti con il [repository Node. js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) per vedere come è abilitato non esiste.

- Usare la [eseguiti](https://docs.docker.com/engine/reference/builder/#run) istruzioni per installare il server SSH e impostare la password per l'account radice su `"Docker!"`. Ad esempio, per un'immagine basata su [Alpine Linux](https://hub.docker.com/_/alpine), è necessario i comandi seguenti:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite `https://<app-name>.scm.azurewebsites.net` e l'autenticazione con le credenziali di pubblicazione.

- Aggiungere [questo file sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) al repository di immagini e utilizzare il [copia](https://docs.docker.com/engine/reference/builder/#copy) (istruzione) per copiare il file per il */etc/ssh/* directory. Per altre informazioni sulle *sshd_config* i file, vedere [OpenBSD documentazione](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti:
    > - `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

- Usare la [ESPORRE](https://docs.docker.com/engine/reference/builder/#expose) (istruzione) per aprire la porta 2222 nel contenitore. Nonostante sia noto che la password radice, la porta 2222 non è accessibile da internet. È accessibile solo dai contenitori all'interno della rete bridge di una rete virtuale privata.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Nello script di avvio per il contenitore, avviare il server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Per un esempio, vedere come il valore predefinito [contenitore Node. js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) avvia il server SSH.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurare le app multi-contenitore

- [Usare l'archiviazione persistente in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limiti dell'anteprima](#preview-limitations)
- [Opzioni di docker Compose](#docker-compose-options)
- [Opzioni di configurazione di Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usare l'archiviazione persistente in Docker Compose

App multi-contenitore, ad esempio WordPress necessaria l'archiviazione permanente per funzionare correttamente. Per abilitarla, la configurazione di Docker Compose deve puntare a un percorso di archiviazione *esterno* nel contenitore. Percorsi di archiviazione all'interno del contenitore non vengono mantenute le modifiche apportate di là di riavvio dell'app.

Abilitare un archivio permanente, impostando il `WEBSITES_ENABLE_APP_SERVICE_STORAGE` app impostazione, usando il [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando in Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nel *docker-Compose. yml* file, eseguire il mapping di `volumes` possibilità `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Ad esempio: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Usare l'archiviazione personalizzata di Docker Compose

Archiviazione di Azure (file di Azure o Blob di Azure) può essere montate con le app multi-contenitore usando l'id personalizzato. Per visualizzare il nome personalizzato-id, eseguire [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Nel *docker-Compose. yml* file, eseguire il mapping di `volumes` possibilità `custom-id`. Ad esempio: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Limiti di anteprima

Multi-contenitore è attualmente in anteprima. Non sono supportate le seguenti funzionalità della piattaforma servizio App:

- Autenticazione/Autorizzazione
- Identità gestite

### <a name="docker-compose-options"></a>Opzioni di docker Compose

Gli elenchi seguenti illustrano supportate e non supportate opzioni di configurazione di Docker Compose:

#### <a name="supported-options"></a>Opzioni supportate

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opzioni non supportate

- build (non consentita)
- depends_on (ignorata)
- networks (ignorata)
- secrets (ignorata)
- porte diverse da 80 e 8080 (ignorate)

> [!NOTE]
> Tutte le altre opzioni non esplicitamente indicate vengono ignorati in anteprima pubblica.

### <a name="kubernetes-configuration-options"></a>Opzioni di configurazione di Kubernetes

Le opzioni di configurazione seguenti sono supportate per Kubernetes:

- args
- command
- containers
- image
- name
- ports
- spec

> [!NOTE]
> Tutte le altre opzioni non esplicitamente indicate non sono supportati nella versione di anteprima pubblica.
>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la distribuzione da un repository di contenitore privato](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multi-contenitore](tutorial-multi-container-app.md)
