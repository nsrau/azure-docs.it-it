---
title: Configurare un contenitore personalizzato
description: Informazioni su come configurare un contenitore personalizzato nel servizio app Azure. Questo articolo illustra le attività di configurazione più comuni.
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5b1bf9b205fc1eb90c6eeae3a101def764381213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264576"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Configurare un contenitore personalizzato per il servizio app Azure

Questo articolo illustra come configurare un contenitore personalizzato da eseguire nel servizio app Azure.

::: zone pivot="container-windows"

Questa guida fornisce i concetti chiave e le istruzioni per la contenitori delle app di Windows nel servizio app. Se non si è mai usato app Azure servizio, seguire prima la [Guida introduttiva ai contenitori personalizzati](quickstart-custom-container.md) e l' [esercitazione](tutorial-custom-container.md) .

::: zone-end

::: zone pivot="container-linux"

Questa guida fornisce concetti chiave e istruzioni per la contenitori di app Linux nel servizio app. Se non si è mai usato app Azure servizio, seguire prima la [Guida introduttiva ai contenitori personalizzati](quickstart-custom-container.md) e l' [esercitazione](tutorial-custom-container.md) . Sono inoltre disponibili un' [esercitazione](tutorial-multi-container-app.md)e una [Guida introduttiva per l'app multicontenitore](quickstart-multi-container.md) .

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Immagini padre supportate

Per l'immagine di Windows personalizzata, è necessario scegliere l' [immagine padre (immagine di base)](https://docs.docker.com/develop/develop-images/baseimages/) corretta per il framework desiderato:

- Per distribuire app .NET Framework, usare un'immagine padre basata sulla versione di [Long-Term Servicing Channel (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) di Windows Server Core. 
- Per distribuire le app .NET Core, usare un'immagine padre basata sulla versione del [canale di manutenzione semestrale](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) di Windows Server nano. 

Il download di un'immagine padre durante l'avvio dell'app richiede tempo. È tuttavia possibile ridurre i tempi di avvio usando una delle immagini padre seguenti, già memorizzate nella cache nel servizio app di Azure.

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Modificare l'immagine Docker di un contenitore personalizzato

Per modificare un'app contenitore personalizzata esistente dall'immagine Docker corrente a una nuova immagine, usare il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Usare un'immagine da un registro privato

Per usare un'immagine da un registro privato, ad esempio Azure Container Registry, eseguire il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Per *\<username>* e *\<password>* , specificare le credenziali di accesso per l'account del registro di sistema privato.

## <a name="i-dont-see-the-updated-container"></a>Il contenitore aggiornato non è visibile

Se si modificano le impostazioni del contenitore Docker in modo che puntino a un nuovo contenitore, potrebbero essere necessari alcuni minuti prima che l'app soddisfi le richieste HTTP dal nuovo contenitore. Durante il pull e l'avvio del nuovo contenitore, il servizio app continua a gestire le richieste del contenitore precedente. Solo quando il nuovo contenitore viene avviato e pronto a ricevere le richieste, il servizio app inizia a inviare richieste.

## <a name="how-container-images-are-stored"></a>Modalità di archiviazione delle immagini del contenitore

La prima volta che si esegue un'immagine Docker personalizzata nel servizio app, il servizio app esegue un'operazione `docker pull` ed estrae tutti i livelli di immagine. Questi livelli vengono archiviati su disco, ad esempio se si usa Docker locale. Ogni volta che l'app viene riavviata, il servizio app esegue un `docker pull` , ma estrae solo i livelli che sono stati modificati. Se non sono state apportate modifiche, il servizio app usa i livelli esistenti sul disco locale.

Se l'app modifica le istanze di calcolo per qualsiasi motivo, ad esempio la scalabilità verticale e verticale dei piani tariffari, il servizio app deve eseguire di nuovo il pull di tutti i livelli. Lo stesso accade se si aumenta la scalabilità orizzontale per aggiungere altre istanze. Esistono anche rari casi in cui le istanze dell'app possono cambiare senza un'operazione di ridimensionamento.

## <a name="configure-port-number"></a>Configurare il numero di porta

Per impostazione predefinita, il servizio app presuppone che il contenitore personalizzato sia in ascolto sulla porta 80. Se il contenitore è in ascolto su una porta diversa, impostare l' `WEBSITES_PORT` impostazione dell'app nell'app del servizio app. È possibile impostarlo tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

Il servizio app consente attualmente al contenitore di esporre solo una porta per le richieste HTTP. 

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Il contenitore personalizzato può usare variabili di ambiente che devono essere fornite esternamente. È possibile passarli tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Quando viene eseguita l'app, le impostazioni dell'app del servizio app vengono inserite automaticamente nel processo come variabili di ambiente. 

::: zone pivot="container-windows"
Per i contenitori basati su IIS o .NET Framework (4,0 o versione successiva), vengono inseriti in `System.ConfigurationManager` come impostazioni dell'app .NET e stringhe di connessione automaticamente dal servizio app. Per tutti gli altri linguaggi o Framework, vengono forniti come variabili di ambiente per il processo, con uno dei prefissi corrispondenti seguenti:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Questo metodo funziona sia per le app a contenitore singolo che per le app a più contenitori, in cui le variabili di ambiente sono specificate nel file *Docker-compose. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Usa archiviazione condivisa persistente

::: zone pivot="container-windows"

È possibile usare la directory *C:\HOME* nel file System dell'app per salvare in modo permanente i file tra i riavvii e condividerli tra le istanze. Il `C:\home` nell'app viene fornito per consentire all'app contenitore di accedere a un archivio permanente.

Quando l'archiviazione persistente è disabilitata, le Scritture nella `C:\home` Directory non sono persistenti. I [log dell'host Docker e i log del contenitore](#access-diagnostic-logs) vengono salvati in una risorsa di archiviazione condivisa persistente predefinita non collegata al contenitore. Quando è abilitata l'archiviazione persistente, tutte le Scritture nella `C:\home` Directory sono persistenti e possono essere accessibili da tutte le istanze di un'app con scalabilità orizzontale e i log sono accessibili in `C:\home\LogFiles` .

Per impostazione predefinita, l'archiviazione persistente è *disabilitata* e l'impostazione non è esposta nelle impostazioni dell'applicazione. Per abilitarla, impostare l' `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

::: zone-end

::: zone pivot="container-linux"

È possibile usare la directory */Home* nell'file System dell'app per salvare in modo permanente i file tra i riavvii e condividerli tra le istanze. Il `/home` nell'app viene fornito per consentire all'app contenitore di accedere a un archivio permanente.

Quando l'archiviazione persistente è disabilitata, le Scritture nella `/home` Directory non vengono rese persistenti tra i riavvii dell'app o tra più istanze. L'unica eccezione è la `/home/LogFiles` Directory, che viene usata per archiviare i log di Docker e del contenitore. Quando è abilitata l'archiviazione persistente, tutte le Scritture nella `/home` Directory sono persistenti ed è possibile accedervi da tutte le istanze di un'app con scalabilità orizzontale.

Per impostazione predefinita, l'archiviazione persistente è *abilitata* e l'impostazione non è esposta nelle impostazioni dell'applicazione. Per disabilitarlo, impostare l' `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=false}
```

::: zone-end

> [!NOTE]
> È anche possibile [configurare una risorsa di archiviazione permanente](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Il servizio app termina TLS/SSL in corrispondenza dei front-end. Ciò significa che le richieste TLS/SSL non vengono mai inviate all'app. Non è necessario e non deve implementare alcun supporto per TLS/SSL nell'app. 

I front-end si trovano all'interno dei Data Center di Azure. Se si usa TLS/SSL con l'app, il traffico in Internet verrà sempre crittografato in modo sicuro.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Personalizzare l'inserimento della chiave del computer ASP.NET

 Durante l'avvio del contenitore, le chiavi generate automaticamente vengono inserite nel contenitore come chiavi del computer per le routine di crittografia ASP.NET. È possibile [trovare queste chiavi nel contenitore](#connect-to-the-container) cercando le variabili di ambiente seguenti:,, `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

Le nuove chiavi a ogni riavvio possono reimpostare l'autenticazione basata su form ASP.NET e lo stato di visualizzazione, se l'app dipende da esse. Per evitare la rigenerazione automatica delle chiavi, [impostarle manualmente come impostazioni dell'app del servizio app](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Connettersi al contenitore

È possibile connettersi direttamente al contenitore di Windows per le attività di diagnostica passando a `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Il funzionamento è il seguente:

- La console di debug consente di eseguire comandi interattivi, ad esempio avviare sessioni di PowerShell, controllare le chiavi del registro di sistema e spostarsi nell'intero contenitore file system.
- Funziona separatamente dal browser grafico sopra di esso, che mostra solo i file presenti nella risorsa di [archiviazione condivisa](#use-persistent-shared-storage).
- In un'app con scalabilità orizzontale, la console di debug è connessa a una delle istanze del contenitore. È possibile selezionare un'istanza diversa dall'elenco a discesa **istanza** nel menu superiore.
- Tutte le modifiche apportate al contenitore dalla *console non vengono mantenute quando* l'app viene riavviata (ad eccezione delle modifiche nell'archiviazione condivisa), perché non fa parte dell'immagine docker. Per rendere permanente le modifiche, ad esempio le impostazioni del registro di sistema e l'installazione del software, fanno parte del Dockerfile.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

Il servizio app registra le azioni dall'host Docker e le attività all'interno del contenitore. Per impostazione predefinita, i log dall'host Docker (log della piattaforma) sono disponibili, ma i registri applicazioni o i log del server Web dall'interno del contenitore devono essere abilitati manualmente. Per altre informazioni, vedere [abilitare la registrazione delle applicazioni](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) e [abilitare la registrazione del server Web](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Sono disponibili diversi modi per accedere ai log di Docker:

- [Nel portale di Azure](#in-azure-portal)
- [Dalla console Kudu](#from-the-kudu-console)
- [Con l'API Kudu](#with-the-kudu-api)
- [Inviare i log a Monitoraggio di Azure](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Nel portale di Azure

I log di Docker vengono visualizzati nel portale nella pagina delle **impostazioni del contenitore** dell'app. I log vengono troncati, ma è possibile scaricare tutti i log facendo clic su **download**. 

### <a name="from-the-kudu-console"></a>Dalla console Kudu

Passare a `https://<app-name>.scm.azurewebsites.net/DebugConsole` e fare clic sulla cartella **LogFiles** per visualizzare i singoli file di log. Per scaricare l'intera directory **LogFiles** , fare clic sull'icona di **download** a sinistra del nome della directory. È anche possibile accedere a questa cartella usando un client FTP.

Nel terminale della console non è possibile accedere alla `C:\home\LogFiles` cartella per impostazione predefinita perché lo spazio di archiviazione condiviso persistente non è abilitato. Per abilitare questo comportamento nel terminale della console, [abilitare l'archiviazione condivisa permanente](#use-persistent-shared-storage).

Se si prova a scaricare il log Docker attualmente in uso usando un client FTP, è possibile che venga ricevuto un errore a causa di un blocco di file.

### <a name="with-the-kudu-api"></a>Con l'API Kudu

Passare direttamente a `https://<app-name>.scm.azurewebsites.net/api/logs/docker` per visualizzare i metadati per i log di Docker. È possibile che venga visualizzato più di un file di log e `href` che la proprietà consenta di scaricare direttamente il file di log. 

Per scaricare tutti i log insieme in un file ZIP, accedere a `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Personalizzare la memoria del contenitore

Per impostazione predefinita, tutti i contenitori di Windows distribuiti nel servizio app Azure sono limitati a 1 GB di RAM. È possibile modificare questo valore specificando l' `WEBSITE_MEMORY_LIMIT_MB` impostazione dell'app tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Il valore è definito in MB e deve essere minore e uguale alla memoria fisica totale dell'host. Ad esempio, in un piano di servizio app con 8 GB di RAM, il totale cumulativo di `WEBSITE_MEMORY_LIMIT_MB` per tutte le app non deve essere superiore a 8 GB. Per informazioni sulla quantità di memoria disponibile per ogni piano tariffario, vedere i [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/)nella sezione del **piano contenitore Premium (Windows)** .

## <a name="customize-the-number-of-compute-cores"></a>Personalizzare il numero di core di calcolo

Per impostazione predefinita, un contenitore di Windows viene eseguito con tutti i core disponibili per il piano tariffario scelto. È possibile, ad esempio, ridurre il numero di core usati dallo slot di staging. Per ridurre il numero di core usati da un contenitore, impostare l' `WEBSITE_CPU_CORES_LIMIT` impostazione dell'app sul numero preferito di core. È possibile impostarlo tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> L'aggiornamento dell'impostazione dell'app attiva il riavvio automatico, causando tempi di inattività minimi. Per un'app di produzione, è consigliabile scambiarla in uno slot di staging, modificare l'impostazione dell'app nello slot di staging e quindi scambiarla nuovamente nell'ambiente di produzione.

Verificare il numero regolato passando alla console Kudu ( `https://<app-name>.scm.azurewebsites.net` ) e digitando i comandi seguenti usando PowerShell. Ogni comando restituisce un numero.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

I processori possono essere processori multicore o Hyper-Threading. Per informazioni sul numero di core disponibili per ogni piano tariffario, vedere i [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/)nella sezione del **piano contenitore Premium (Windows)** .

## <a name="customize-health-ping-behavior"></a>Personalizzare il comportamento del ping di integrità

Il servizio app considera un contenitore da avviare correttamente quando il contenitore viene avviato e risponde a un ping HTTP. L'intestazione della richiesta ping di integrità è contenitore `User-Agent= "App Service Hyper-V Container Availability Check"` . Se il contenitore viene avviato ma non risponde a un ping dopo un determinato periodo di tempo, il servizio app registra un evento nel registro Docker, indicando che il contenitore non è stato avviato. 

Se l'applicazione richiede un utilizzo intensivo delle risorse, il contenitore potrebbe non rispondere al ping HTTP nel tempo. Per controllare le azioni quando i ping HTTP hanno esito negativo, impostare l' `CONTAINER_AVAILABILITY_CHECK_MODE` impostazione dell'app. È possibile impostarlo tramite il [cloud Shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

Nella tabella seguente sono indicati i valori possibili:

| Valore | Descrizioni |
| - | - |
| **Ripristinare** | Riavviare il contenitore dopo tre verifiche di disponibilità consecutive |
| **ReportOnly** | Il valore predefinito. Non riavviare il contenitore ma creare un report nei log di Docker per il contenitore dopo tre controlli di disponibilità consecutivi. |
| **Disattivato** | Non verificare la disponibilità. |

## <a name="support-for-group-managed-service-accounts"></a>Supporto per gli account del servizio gestiti del gruppo

Gli account del servizio gestito del gruppo (servizi gestiti) non sono attualmente supportati nei contenitori di Windows nel servizio app.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Abilitare SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per consentire a un contenitore personalizzato di supportare SSH, è necessario aggiungerlo alla Dockerfile stessa.

> [!TIP]
> Tutti i contenitori Linux predefiniti hanno aggiunto le istruzioni SSH nei repository di immagini. È possibile seguire le istruzioni seguenti con il [ repositoryNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) per vedere come è abilitato.

- Usare l'istruzione [Run](https://docs.docker.com/engine/reference/builder/#run) per installare il server SSH e impostare la password per l'account radice su `"Docker!"` . Per un'immagine basata su [Alpine Linux](https://hub.docker.com/_/alpine), ad esempio, sono necessari i comandi seguenti:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite `https://<app-name>.scm.azurewebsites.net` e autenticato con le credenziali di pubblicazione.

- Aggiungere [questo file di sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) all'archivio immagini e usare l'istruzione [Copy](https://docs.docker.com/engine/reference/builder/#copy) per copiare il file nella directory *nella/etc/ssh/* Per ulteriori informazioni sui file di *sshd_config* , vedere la [documentazione di OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti:
    > - `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

- Usare l'istruzione [Expose](https://docs.docker.com/engine/reference/builder/#expose) per aprire la porta 2222 nel contenitore. Sebbene la password radice sia nota, la porta 2222 non è accessibile da Internet. È accessibile solo dai contenitori all'interno della rete Bridge di una rete virtuale privata.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Nello script di avvio per il contenitore avviare il server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Per un esempio, vedere come il [ contenitore predefinitoNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) avvia il server SSH.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurare app multicontenitore

- [Usare l'archiviazione persistente in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limiti di anteprima](#preview-limitations)
- [Opzioni di Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usare l'archiviazione persistente in Docker Compose

Per il corretto funzionamento di app a più contenitori come WordPress è necessario disporre di un archivio permanente. Per abilitarla, la configurazione del Docker Compose deve puntare a una posizione di archiviazione *all'esterno* del contenitore. Le posizioni di archiviazione all'interno del contenitore non rendono permanente le modifiche oltre il riavvio dell'app.

Abilitare l'archiviazione permanente impostando l' `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app usando il comando [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in [cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nel file *Docker-compose. yml* mappare l' `volumes` opzione a `${WEBAPP_STORAGE_HOME}` . 

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

Il multicontenitore è attualmente in fase di anteprima. Le funzionalità della piattaforma del servizio app seguenti non sono supportate:

- Autenticazione/Autorizzazione
- Identità gestite
- CORS

### <a name="docker-compose-options"></a>Opzioni di Docker Compose

Negli elenchi seguenti sono illustrate le opzioni di configurazione Docker Compose supportate e non supportate:

#### <a name="supported-options"></a>Opzioni supportate

- .
- entrypoint
- ambiente
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
> Eventuali altre opzioni non esplicitamente richiamate verranno ignorate in anteprima pubblica.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: eseguire la migrazione di software personalizzato al servizio app Azure usando un contenitore personalizzato](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)

::: zone-end

In alternativa, vedere risorse aggiuntive:

[Caricare il certificato nei contenitori Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)