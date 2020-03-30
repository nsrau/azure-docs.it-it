---
title: Configurare un contenitore Linux personalizzatoConfigure a custom Linux container
description: Informazioni su come configurare un contenitore Linux personalizzato nel servizio app di Azure.Learn how to configure a custom Linux container in Azure App Service. Questo articolo illustra le attività di configurazione più comuni.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280144"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurare un contenitore Linux personalizzato per il servizio app di AzureConfigure a custom Linux container for Azure App Service

Questo articolo illustra come configurare un contenitore Linux personalizzato per l'esecuzione nel servizio app di Azure.This article shows you how to configure a custom Linux container to run on Azure App Service.

Questa guida fornisce concetti chiave e istruzioni per la containerizzazione delle app Linux nel servizio app. Se non si è mai usato il servizio app di Azure, seguire prima la guida introduttiva e [l'esercitazione](tutorial-custom-docker-image.md) del [contenitore personalizzato.](quickstart-docker-go.md) C'è anche una guida introduttiva e [un'esercitazione](tutorial-multi-container-app.md)per [app multi-contenitore.](quickstart-multi-container.md)

## <a name="configure-port-number"></a>Configurare il numero di porta

Il server Web nell'immagine personalizzata può utilizzare una porta diversa da 80. Si indica ad Azure la porta usata dal contenitore personalizzato usando l'impostazione dell'app. `WEBSITES_PORT` La pagina di GitHub per l'[esempio di Python in questa esercitazione](https://github.com/Azure-Samples/docker-django-webapp-linux) indica che è necessario impostare `WEBSITES_PORT` su _8000_. È possibile impostarlo [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) eseguendo il comando in Cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Il contenitore personalizzato può usare variabili di ambiente che devono essere fornite esternamente. È possibile passarli [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) eseguendo il comando in Cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Questo metodo funziona sia per le app a contenitore singolo che per le app multicontenitore, in cui le variabili di ambiente sono specificate nel file *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Usare l'archiviazione condivisa permanenteUse persistent shared storage

Puoi usare la directory */home* nel file system dell'app per rendere persistenti i file tra i riavvii e condividerli tra le istanze. L'app `/home` viene fornita per consentire all'app contenitore di accedere all'archiviazione permanente.

Quando l'archiviazione permanente è `/home` disabilitata, le scritture nella directory non vengono rese persistenti tra i riavvii dell'app o tra più istanze. L'unica eccezione è la `/home/LogFiles` directory, che viene utilizzata per archiviare i registri Docker e Contenitore. Quando l'archiviazione permanente è `/home` abilitata, tutte le scritture nella directory vengono rese persistenti ed è accessibile da tutte le istanze di un'app con scalabilità orizzontale.

Per impostazione predefinita, l'archiviazione permanente è *abilitata* e l'impostazione non è esposta nelle impostazioni dell'applicazione. Per disabilitarla, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` imposta l'impostazione dell'app eseguendo [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) il comando in Cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> È inoltre possibile [configurare la propria risorsa di archiviazione permanente](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Abilitare SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Affinché un contenitore personalizzato supporti SSH, è necessario aggiungerlo nel Dockerfile stesso.

> [!TIP]
> Tutti i contenitori Linux incorporati hanno aggiunto le istruzioni SSH nei repository di immagini. È possibile passare attraverso le seguenti istruzioni con il [repository Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) per vedere come è abilitato lì.

- Utilizzare l'istruzione [RUN](https://docs.docker.com/engine/reference/builder/#run) per installare il server SSH `"Docker!"`e impostare la password per l'account root su . Ad esempio, per un'immagine basata su [Alpine Linux](https://hub.docker.com/_/alpine), sono necessari i seguenti comandi:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile `https://<app-name>.scm.azurewebsites.net` solo tramite ed autenticato con le credenziali di pubblicazione.

- Aggiungere [questo file sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) al repository di immagini e utilizzare l'istruzione [COPY](https://docs.docker.com/engine/reference/builder/#copy) per copiare il file nella directory */etc/ssh/.* Per ulteriori informazioni sui *file di sshd_config,* vedere la [documentazione di OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti:
    > - `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

- Utilizzare l'istruzione [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) per aprire la porta 2222 nel contenitore. Anche se la password principale è nota, la porta 2222 non è accessibile da Internet. È accessibile solo da contenitori all'interno della rete bridge di una rete virtuale privata.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Nello script di avvio del contenitore avviare il server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Per un esempio, vedere come il [contenitore Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) predefinito avvia il server SSH.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurare app multi-contenitore

- [Usare l'archiviazione permanente in Docker ComposeUse persistent storage in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limiti di anteprima](#preview-limitations)
- [Opzioni di composizione della finestra mobile](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usare l'archiviazione permanente in Docker ComposeUse persistent storage in Docker Compose

Applicazioni multi-contenitore come WordPress hanno bisogno di archiviazione persistente per funzionare correttamente. Per abilitarlo, la configurazione Docker Compose deve puntare a una posizione di archiviazione *esterna al* contenitore. Le posizioni di archiviazione all'interno del contenitore non mantengono le modifiche successive al riavvio dell'app.

Abilitare l'archiviazione `WEBSITES_ENABLE_APP_SERVICE_STORAGE` permanente impostando l'impostazione dell'app usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nel file *docker-compose.yml,* `volumes` mappare `${WEBAPP_STORAGE_HOME}`l'opzione su . 

`WEBAPP_STORAGE_HOME` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Ad esempio:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limiti di anteprima

Multi-container è attualmente in anteprima. Le funzionalità della piattaforma del servizio app seguenti non sono supportate:The following App Service platform features are not supported:

- Autenticazione/Autorizzazione
- Identità gestite

### <a name="docker-compose-options"></a>Opzioni di composizione della finestra mobile

Gli elenchi seguenti mostrano le opzioni di configurazione Docker Compose supportate e non supportate:

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
> Tutte le altre opzioni non chiamate in modo esplicito vengono ignorate nell'anteprima pubblica.

## <a name="configure-vnet-integration"></a>Configurare l'integrazione della rete virtuale

L'utilizzo di un contenitore personalizzato con l'integrazione della rete virtuale può richiedere una configurazione aggiuntiva del contenitore. Vedere [Integrazione di un'app in una rete virtuale di Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire dal repository del contenitore privatoTutorial: Deploy from private container repository](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontainer](tutorial-multi-container-app.md)
