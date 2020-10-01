---
title: "Esercitazione: Creare ed eseguire un'immagine personalizzata nel servizio app di Azure"
description: Guida dettagliata per creare un'immagine Linux o Windows personalizzata, eseguirne il push in Registro Azure Container e quindi distribuirla nel servizio app di Azure. Informazioni su come eseguire la migrazione della distribuzione di un software personalizzato al servizio app in un contenitore personalizzato.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: servizio app di azure, app web, linux, windows, docker, contenitore
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: fdc15ecd79a6672d2a46b4da284533965977d753
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982871"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Eseguire la migrazione di un software personalizzato al Servizio app di Azure usando un contenitore personalizzato

::: zone pivot="container-windows"  

Il [servizio app di Azure](overview.md) offre stack di applicazioni predefiniti in Windows, ad esempio ASP.NET o Node.js, eseguiti in IIS. L'ambiente Windows preconfigurato blocca il sistema operativo impedendo l'accesso amministrativo, le installazioni di software, le modifiche alla Global Assembly Cache e così via (vedere [Funzionalità del sistema operativo in Servizio app di Azure](operating-system-functionality.md)). Tuttavia, usando un contenitore Windows personalizzato nel Servizio app (Anteprima) è possibile apportare le modifiche del sistema operativo necessarie per l'app, per poter eseguire in modo semplice la migrazione di un'app locale che richiede una configurazione personalizzata di software e sistema operativo. Questa esercitazione illustra come eseguire la migrazione al servizio app di un'app ASP.NET che usa tipi di carattere personalizzati installati nella libreria dei tipi di carattere Windows. Si distribuisce un'immagine Windows configurata in modo personalizzato da Visual Studio a [Registro Azure Container](../container-registry/index.yml) e quindi la si esegue nel servizio app.

![Mostra l'app Web in esecuzione in un contenitore di Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- <a href="https://hub.docker.com/" target="_blank">Iscriversi per ottenere un account Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installare Docker per Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Impostare Docker per eseguire contenitori Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installare Visual Studio 2019</a> con i carichi di lavoro **Sviluppo ASP.NET e Web** e **Sviluppo di Azure**. Se Visual Studio 2019 è già installato:
    - Installare gli aggiornamenti più recenti in Visual Studio facendo clic su **?**  > **Controlla aggiornamenti**.
    - Aggiungere i carichi di lavoro in Visual Studio facendo clic su **Strumenti** > **Ottieni strumenti e funzionalità**.

## <a name="set-up-the-app-locally"></a>Configurare l'app in locale

### <a name="download-the-sample"></a>Scaricare l'esempio

In questo passaggio si configura il progetto .NET locale.

- [Scaricare il progetto di esempio](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Estrarre (decomprimere) il file *custom-font-win-container.zip*.

Il progetto di esempio contiene un'applicazione ASP.NET semplice che usa un tipo di carattere personalizzato installato nella libreria dei tipi di carattere Windows. Non è necessario installare i tipi di carattere, ma si tratta di un esempio di app integrata con il sistema operativo sottostante. Per eseguire la migrazione di un'app di questo tipo al servizio app, è necessario riprogettare il codice per rimuovere l'integrazione oppure è possibile eseguire la migrazione dell'app senza modifiche in un contenitore Windows personalizzato.

### <a name="install-the-font"></a>Installare il tipo di carattere

In Esplora risorse passare a _custom-font-win-container-master/CustomFontSample_, fare clic con il pulsante destro del mouse su _FrederickatheGreat-Regular.ttf_ e scegliere **Installa**.

Questo tipo di carattere è disponibile pubblicamente in [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Eseguire l'app

Aprire il file *custom-font-win-container/CustomFontSample.sln* in Visual Studio. 

Digitare `Ctrl+F5` per eseguire l'app senza debug. L'app viene visualizzata nel browser predefinito. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Screenshot che mostra l'app visualizzata nel browser predefinito.":::

Poiché usa un tipo di carattere installato, l'app non può essere eseguita nell'ambiente sandbox del servizio app. È tuttavia possibile distribuirla usando un contenitore Windows, perché è possibile installare il tipo di carattere nel contenitore Windows.

### <a name="configure-windows-container"></a>Configurare il contenitore Windows

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CustomFontSample** e scegliere **Aggiungi** > **Container Orchestration Support** (Supporto orchestrazione contenitori).

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Screenshot che mostra l'app visualizzata nel browser predefinito.":::

Selezionare **Docker Compose** > **OK**.

Il progetto è ora configurato per l'esecuzione in un contenitore Windows. Verrà aggiunto un file _Dockerfile_ al progetto **CustomFontSample** e un progetto **docker-compose** verrà aggiunto alla soluzione. 

Da Esplora soluzioni aprire **Dockerfile**.

È necessario usare un'[immagine padre supportata](quickstart-custom-container.md#use-a-different-parent-image). Modificare l'immagine padre sostituendo la riga `FROM` con il codice seguente:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Alla fine del file aggiungere la riga seguente e quindi salvare il file:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ è disponibile nel progetto **CustomFontSample**. Si tratta di un semplice script che installa il tipo di carattere. Una versione più complessa dello script è disponibile in [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Per testare il contenitore Windows in locale, assicurarsi di aver avviato Docker nel computer locale.
>

## <a name="publish-to-azure-container-registry"></a>Eseguire la pubblicazione in Registro Azure Container

[Registro Azure Container](../container-registry/index.yml) consente di archiviare le immagini per le distribuzioni di contenitori. È possibile configurare il servizio app per usare le immagini ospitate in Registro Azure Container.

### <a name="open-publish-wizard"></a>Aprire la pubblicazione guidata

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CustomFontSample** e scegliere **Pubblica**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Screenshot che mostra l'app visualizzata nel browser predefinito.":::

### <a name="create-registry-and-publish"></a>Creare il registro ed eseguire la pubblicazione

Nella pubblicazione guidata selezionare **Registro contenitori** > **Crea nuovo Registro Azure Container** > **Pubblica**.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Screenshot che mostra l'app visualizzata nel browser predefinito.":::

### <a name="sign-in-with-azure-account"></a>Accedere con l'account di Azure

Nella finestra di dialogo **Crea un nuovo Registro Azure Container** selezionare **Aggiungi un account** e accedere alla sottoscrizione di Azure. Se è già stato eseguito l'accesso, selezionare l'account contenente la sottoscrizione desiderata dall'elenco a discesa.

![Accedere ad Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Configurare il registro

Configurare il nuovo registro contenitori in base ai valori suggeriti nella tabella seguente. Al termine, fare clic su **Crea**.

| Impostazione  | Valore consigliato | Per ulteriori informazioni |
| ----------------- | ------------ | ----|
|**Prefisso DNS**| Mantenere il nome del registro generato oppure modificarlo scegliendo un altro nome univoco. |  |
|**Gruppo di risorse**| Fare clic su **Nuovo**, digitare **myResourceGroup** e fare clic su **OK**. |  |
|**SKU**| Basic | [Piani tariffari](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Percorso del registro**| Europa occidentale | |

![Configurare il Registro Azure Container](./media/tutorial-custom-container/configure-registry.png)

Verrà aperta una finestra del terminale in cui viene visualizzato l'avanzamento della distribuzione dell'immagine. Attendere il completamento della distribuzione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-web-app"></a>Creare un'app Web

Dal menu a sinistra scegliere **Crea una risorsa** > **Web** > **App Web per contenitori**.

### <a name="configure-app-basics"></a>Configurare le informazioni di base dell'app

Nella scheda **Informazioni di base** configurare le impostazioni in base alla tabella seguente e quindi fare clic su **Avanti: Docker**.

| Impostazione  | Valore consigliato | Per ulteriori informazioni |
| ----------------- | ------------ | ----|
|**Sottoscrizione**| Assicurarsi che sia selezionata la sottoscrizione corretta. |  |
|**Gruppo di risorse**| Selezionare **Crea nuovo**, digitare **myResourceGroup** e scegliere **OK**. |  |
|**Nome**| Digitare un nome univoco. | L'URL dell'app Web è `http://<app-name>.azurewebsites.net`, dove `<app-name>` è il nome dell'app. |
|**Pubblica**| Contenitore Docker | |
|**Sistema operativo**| Windows | |
|**Area**| Europa occidentale | |
|**Piano Windows**| Selezionare **Crea nuovo**, digitare **myAppServicePlan** e scegliere **OK**. | |

La scheda **Informazioni di base** avrà un aspetto simile al seguente:

![Mostra la scheda Informazioni di base usata per configurare l'app Web.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Configurare il contenitore Windows

Nella scheda **Docker** configurare il contenitore Windows personalizzato come illustrato nella tabella seguente e selezionare **Rivedi e crea**.

| Impostazione  | Valore consigliato |
| ----------------- | ------------ |
|**Origine immagine**| Registro Azure Container |
|**Registro**| Selezionare il [registro creato in precedenza](#publish-to-azure-container-registry). |
|**Immagine**| customfontsample |
|**Tag**| più recenti |

### <a name="complete-app-creation"></a>Completare la creazione dell'app

Fare clic su **Crea** e attendere che Azure crei le risorse necessarie.

## <a name="browse-to-the-web-app"></a>Passare all'app Web

Al termine dell'operazione di Azure, verrà visualizzata una casella di notifica.

![Indica che l'operazione di Azure è stata completata.](media/tutorial-custom-container/portal-create-finished.png)

1. Fare clic su **Vai alla risorsa**.

2. Nella pagina dell'app fare clic sul collegamento sotto **URL**.

Verrà aperta la nuova pagina del browser illustrata di seguito:

![Mostra la nuova pagina del browser per l'app Web.](media/tutorial-custom-container/app-starting.png)

Attendere alcuni minuti e riprovare, finché non viene visualizzata la pagina iniziale, con il carattere desiderato:

![Mostra la home page con il tipo di carattere configurato.](media/tutorial-custom-container/app-running.png)

**Congratulazioni** È stata eseguita la migrazione di un'applicazione ASP.NET in Servizio app di Azure in un contenitore Windows.

## <a name="see-container-start-up-logs"></a>Visualizzare i log di avvio del contenitore

Il caricamento del contenitore Windows potrebbe richiedere tempo. Per visualizzare lo stato di avanzamento, passare all'URL seguente sostituendo *\<app-name>* con il nome dell'app.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

I log trasmessi sono simili al seguente:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Il servizio app di Azure usa la tecnologia dei contenitori Docker per ospitare immagini predefinite e personalizzate. Per vedere un elenco di immagini predefinite, eseguire il comando dell'interfaccia della riga di comando di Azure ['az webapp list-runtimes --linux'](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes). Se queste immagini non soddisfano le proprie esigenze, è possibile creare e distribuire un'immagine personalizzata.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'immagine personalizzata se nessuna immagine predefinita soddisfa le proprie esigenze
> * Eseguire il push dell'immagine personalizzata in un registro contenitori privato in Azure
> * Eseguire l'immagine personalizzata nel servizio app
> * Configurare le variabili di ambiente
> * Aggiornare e ridistribuire l'immagine
> * Accedere ai log di diagnostica
> * Connettersi al contenitore con SSH

Il completamento di questa esercitazione comporta un piccolo addebito nell'account Azure per il registro contenitori e può comportare altri costi per ospitare il contenitore per più di un mese.

## <a name="set-up-your-initial-environment"></a>Configurare l'ambiente iniziale

* Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Installare [Docker](https://docs.docker.com/get-started/#setup), che è possibile usare per creare immagini Docker. L'installazione di Docker potrebbe richiedere il riavvio del computer.
* Installare l'<a href="/cli/azure/install-azure-cli" target="_blank">interfaccia della riga di comando di Azure</a> 2.0.80 o versione successiva, con cui si eseguono i comandi in qualsiasi shell per il provisioning e la configurazione delle risorse di Azure.

Dopo aver installato Docker e l'interfaccia della riga di comando di Azure, aprire una finestra del terminale e verificare che Docker sia installato:

```bash
docker --version
```

Verificare anche che la versione dell'interfaccia della riga di comando di Azure sia 2.0.80 o successiva:

```azurecli
az --version
```

Quindi accedere ad Azure tramite l'interfaccia della riga di comando:

```azurecli
az login
```

Il comando `az login` apre un browser per raccogliere le credenziali. Al termine dell'esecuzione del comando, viene visualizzato un output JSON contenente le informazioni sulle sottoscrizioni.

Dopo aver eseguito l'accesso, è possibile eseguire i comandi di Azure con l'interfaccia della riga di comando di Azure per usare le risorse nella sottoscrizione.

## <a name="clone-or-download-the-sample-app"></a>Clonare o scaricare l'app di esempio

È possibile ottenere l'esempio per questa esercitazione tramite git clone o download.

### <a name="clone-with-git"></a>Clonare con git

Clonare il repository di esempio:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Assicurarsi di includere l'argomento `--config core.autocrlf=input` per garantire le terminazioni di riga corrette nei file usati all'interno del contenitore Linux:

Passare quindi alla cartella:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Scaricare da GitHub

Invece di usare git clone, è possibile visitare [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux), selezionare **Clona**e quindi selezionare **Scarica ZIP**. 

Decomprimere il file ZIP in una cartella denominata *docker-django-webapp-linux*. 

Aprire quindi una finestra del terminale nella cartella *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Facoltativo) Esaminare il file Docker

Il file nell'esempio denominato _Dockerfile_ che descrive l'immagine Docker e contiene le istruzioni di configurazione:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Il primo gruppo di comandi installa i requisiti dell'app nell'ambiente.
* Il secondo gruppo di comandi crea un server [SSH](https://www.ssh.com/ssh/protocol/) per la comunicazione sicura tra il contenitore e l'host.
* L'ultima riga, `ENTRYPOINT ["init.sh"]`, richiama `init.sh` per avviare il servizio SSH e il server Python.

## <a name="build-and-test-the-image-locally"></a>Creare e testare l'immagine in locale

1. Eseguire il comando seguente per creare l'immagine:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Testare il funzionamento della build eseguendo il contenitore Docker in locale:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Questo comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) specifica la porta con l'argomento `-p` seguito dal nome dell'immagine. 
    
    > [!TIP]
    > Se si usa Windows e viene visualizzato l'errore *standard_init_linux.go:211: exec user process caused "no such file or directory"* , il file *init.sh* contiene terminazioni di riga CR-LF invece di quelle LF previste. Questo errore si verifica se è stato usato git per clonare il repository di esempio ma è stato omesso il parametro `--config core.autocrlf=input`. In questo caso, clonare nuovamente il repository con l'argomento '--config". Questo errore potrebbe essere visualizzato anche se il file *init.sh* è stato modificato e salvato con le terminazioni CRLF. In questo caso, salvare di nuovo il file solo con le terminazioni LF.

1. Passare a `http://localhost:8000` per verificare che l'app Web e il contenitore funzionino correttamente.

    ![Testare l'app Web in locale](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Questa sezione e quelle che seguono illustrano come effettuare il provisioning di risorse in Azure in cui eseguire il push dell'immagine e quindi come distribuire un contenitore nel servizio app di Azure. Per iniziare, creare un gruppo di risorse in cui raccogliere tutte queste risorse.

Eseguire il comando [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) per creare un gruppo di risorse:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

È possibile cambiare il valore di `--location` per specificare un'area nelle vicinanze.

## <a name="push-the-image-to-azure-container-registry"></a>Eseguire il push delle immagini in Registro Azure Container

In questa sezione si esegue il push dell'immagine in Registro Azure Container da cui verrà distribuita con il servizio app.

1. Eseguire il comando [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) per creare un'istanza di Registro Azure Container:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Sostituire `<registry-name>` con un nome appropriato per il registro. Il nome deve essere univoco in tutto Azure e può contenere solo lettere e numeri.

1. Eseguire il comando [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) per recuperare le credenziali per il registro:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    L'output JSON di questo comando fornisce due password insieme al nome utente del registro.
    
1. Usare il comando `docker login` per accedere al registro contenitori:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Sostituire `<registry-name>` e `<registry-username>` con i valori dei passaggi precedenti. Quando richiesto, digitare una delle password del passaggio precedente.

    Usare lo stesso nome del registro in tutti i passaggi rimanenti di questa sezione.

1. Una volta completato l'accesso, contrassegnare l'immagine Docker locale per il registro:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Usare il comando `docker push` per eseguire il push dell'immagine nel registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    La prima volta possono essere necessari alcuni minuti per caricare l'immagine, perché è inclusa l'immagine di base. I caricamenti successivi sono in genere più veloci.

    Mentre si è in attesa, è possibile completare i passaggi della sezione successiva per configurare il servizio app per la distribuzione dal registro.

1. Usare il comando `az acr repository list` per verificare che il push sia stato eseguito correttamente:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    L'output dovrebbe mostrare il nome dell'immagine.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Configurare il servizio app per distribuire l'immagine dal registro

Per distribuire un contenitore nel servizio app di Azure, creare prima un'app Web nel servizio app, quindi connettere l'app Web al registro contenitori. All'avvio dell'app, il servizio app esegue automaticamente il pull dell'immagine dal registro.

1. Creare un piano di servizio app usando il comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create):

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Un piano di servizio app corrisponde alla macchina virtuale che ospita l'app Web. Per impostazione predefinita, il comando precedente usa un [piano tariffario B1](https://azure.microsoft.com/pricing/details/app-service/linux/) poco costoso, gratuito per il primo mese. È possibile controllare il livello con il parametro `--sku`.

1. Creare l'app Web con il comando [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create):

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Sostituire `<app-name>` con un nome per l'app Web, che deve essere univoco in tutto Azure. Sostituire inoltre `<registry-name>` con il nome del registro creato nel passaggio precedente.

1. Usare [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) per impostare la variabile di ambiente `WEBSITES_PORT` come previsto dal codice dell'app: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Sostituire `<app-name>` con il nome usato nel passaggio precedente.
    
    Per altre informazioni su questa variabile di ambiente, vedere il file [readme nel repository GitHub dell'esempio](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Abilitare '[identità gestita](./overview-managed-identity.md) per l'app Web usando il comando [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign):

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Sostituire `<app-name>` con il nome usato nel passaggio precedente. L'output del comando (filtrato in base agli argomenti `--query` e `--output`) è l'entità servizio dell'identità assegnata, che verrà usata tra breve.

    L'identità gestita consente di concedere le autorizzazioni all'app Web per accedere ad altre risorse di Azure senza la necessità di credenziali specifiche.

1. Recuperare l'ID sottoscrizione con il comando [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show), che sarà necessario nel passaggio successivo:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Concedere all'app Web l'autorizzazione per accedere al registro contenitori:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Sostituire i valori seguenti:
    - `<principal-id>` con l'ID dell'entità servizio del comando `az webapp identity assign`
    - `<registry-name>` con il nome del registro contenitori
    - `<subscription-id>` con l'ID sottoscrizione recuperato dal comando `az account show`

Per altre informazioni su queste autorizzazioni, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../role-based-access-control/overview.md) 

## <a name="deploy-the-image-and-test-the-app"></a>Distribuire l'immagine e testare l'app

È possibile completare questi passaggi dopo aver eseguito il push dell'immagine nel registro contenitori e una volta completato il provisioning del servizio app.

1. Usare il comando [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set) per specificare il registro contenitori e l'immagine da distribuire per l'app Web:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Sostituire `<app_name>` con il nome dell'app Web e `<registry-name>` in due posizioni con il nome del registro. 

    - Se si usa un registro diverso da Docker Hub (come mostrato in questo esempio), `--docker-registry-server-url` deve essere formattato come `https://` seguito dal nome di dominio completo del registro.
    - Il messaggio analogo a "Non sono state specificate credenziali per accedere a Registro Azure Container. È in corso il tentativo di eseguire una ricerca" indica che Azure usa l'identità gestita dell'app per l'autenticazione con il registro contenitore invece di chiedere un nome utente e una password.
    - Se viene visualizzato il messaggio di errore "AttributeError: L'oggetto 'NoneType' non ha l'attributo 'reserved'", verificare che `<app-name>` sia corretto.

    > [!TIP]
    > È possibile recuperare le impostazioni del contenitore dell'app Web in qualsiasi momento con il comando `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`. L'immagine è specificata nella proprietà `DOCKER_CUSTOM_IMAGE_NAME`. Se l'app Web viene distribuita tramite Azure DevOps o modelli di Azure Resource Manager, l'immagine può essere visualizzata anche in una proprietà denominata `LinuxFxVersion`. Le due proprietà hanno lo stesso scopo. Se sono presenti entrambe nella configurazione dell'app Web, `LinuxFxVersion` avrà la precedenza.

1. Al termine del comando `az webapp config container set`, l'app Web dovrebbe essere in esecuzione nel contenitore nel servizio app.

    Per testare l'app, passare a `http://<app-name>.azurewebsites.net`, sostituendo `<app-name>` con il nome dell'app Web. Al primo accesso, la risposta dell'app potrebbe richiedere del tempo perché il servizio app deve eseguire il pull dell'intera immagine dal registro. Se il browser raggiunge il timeout, è sufficiente aggiornare la pagina. Una volta eseguito il pull dell'immagine iniziale, i test successivi saranno molto più veloci.

    ![Test riuscito dell'app Web in Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Modificare il codice dell'app e ridistribuirlo

In questa sezione si apportano modifiche al codice dell'app Web, si ricompila il contenitore e quindi si esegue il push del contenitore nel registro. Il servizio app quindi esegue automaticamente il pull dell'immagine aggiornata dal registro per aggiornare l'app Web in esecuzione.

1. Nella cartella *docker-django-webapp-linux* locale aprire il file *app/templates/app/index.html*.

1. Cambiare il primo elemento HTML in modo che corrisponda al codice seguente.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Salvare le modifiche.

1. Passare alla cartella *docker-django-webapp-linux* e ricreare l'immagine:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Aggiornare il numero di versione nel tag dell'immagine a v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Sostituire `<registry-name>` con il nome del registro.

1. Eseguire il push dell'immagine nel registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Riavviare l'app Web:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Sostituire `<app_name>` con il nome dell'app Web. Al riavvio, il servizio app esegue il pull dell'immagine aggiornata dal registro contenitori.

1. Per verificare che l'aggiornamento sia stato distribuito, passare a `http://<app-name>.azurewebsites.net`.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

1. Attivare la registrazione del contenitore:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Abilitare lo streaming dei log:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

    È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Per interrompere lo streaming dei log in qualsiasi momento, premere **CTRL**+**C**.

## <a name="connect-to-the-container-using-ssh"></a>Connettersi al contenitore con SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per abilitare la connessione SSH al contenitore, l'immagine personalizzata deve essere configurata appositamente. Quando il contenitore è in esecuzione, è possibile aprire la connessione SSH.

### <a name="configure-the-container-for-ssh"></a>Configurare il contenitore per SSH

L'app di esempio usata in questa esercitazione ha già la configurazione necessaria nel *Dockerfile*, che installa il server SSH e imposta anche le credenziali di accesso. Questa sezione è unicamente informativa. Per connettersi al contenitore, passare alla sezione successiva

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite il sito Kudu/SCM. Il sito Kudu/SCM viene autenticato con l'account Azure.

Il *Dockerfile* copia anche il file *sshd_config* nella cartella */etc/ssh/* ed espone la porta 2222 sul contenitore:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

La porta 2222 è una porta interna accessibile dai contenitori nella rete bridge di una rete virtuale privata. 

Infine, lo script di ingresso *init.sh* avvia il server SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Aprire una connessione SSH a un contenitore

1. Passare a `https://<app-name>.scm.azurewebsites.net/webssh/host` e accedere con l'account Azure. Sostituire `<app-name>` con il nome dell'app Web.

1. Una volta effettuato l'accesso, si viene reindirizzati a una pagina informativa per l'app Web. Selezionare **SSH** nella parte superiore della pagina per aprire la shell e usare i comandi.

    Ad esempio, è possibile esaminare i processi in esecuzione al suo interno usando il comando `top`.
    
## <a name="clean-up-resources"></a>Pulire le risorse

Le risorse create in questo articolo possono comportare costi ricorrenti. Per pulire le risorse, è necessario solo eliminare il gruppo di risorse che le contiene:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Distribuire un'immagine personalizzata in un registro contenitori privato
> * Distribuire l'immagine personalizzata nel servizio app
> * Aggiornare e ridistribuire l'immagine
> * Accedere ai log di diagnostica
> * Connettersi al contenitore con SSH

L'esercitazione successiva illustra come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare il contenitore personalizzato](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)

::: zone-end