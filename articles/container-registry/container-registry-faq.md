---
title: Registro contenitori di Azure - domande frequenti
description: Risposte alle domande frequenti relative al servizio Registro contenitori di Azure
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: beeb4986750e398071e3afb6c1f04663f858cec1
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303582"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Domande frequenti su registro contenitori di Azure

Questo articolo affronta domande frequenti e problemi noti sul registro contenitori di Azure.

## <a name="resource-management"></a>Resource management

- [È possibile creare un registro contenitori di Azure usando un modello di Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [È presente una vulnerabilità di sicurezza l'analisi per le immagini nel registro contenitori di AZURE?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Come si configura Kubernetes con registro contenitori di Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Come ottenere le credenziali di amministratore per un registro contenitori?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Come ottenere le credenziali di amministratore in un modello di Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Operazione di eliminazione della replica ha esito negativo con stato non consentito anche se la replica viene eliminata usando il comando di Azure o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>È possibile creare un registro contenitori di Azure usando un modello di Resource Manager?

Sì. Di seguito è riportato [un modello](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) che è possibile usare per creare un registro di sistema.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>È presente una vulnerabilità di sicurezza l'analisi per le immagini nel registro contenitori di AZURE?

Sì. Vedere la documentazione fornita dal [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Come si configura Kubernetes con registro contenitori di Azure?

Vedere la documentazione relativa [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e i passaggi relativi [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Come ottenere le credenziali di amministratore per un registro contenitori?

> [!IMPORTANT]
> L'account utente amministratore è progettato per un singolo utente di accedere al registro, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Visualizzare [panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del Registro di sistema è abilitato.

Per ottenere le credenziali tramite la CLI di Azure:

```azurecli
az acr credential show -n myRegistry
```

Uso di Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Come ottenere le credenziali di amministratore in un modello di Resource Manager?

> [!IMPORTANT]
> L'account utente amministratore è progettato per un singolo utente di accedere al registro, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Visualizzare [panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del Registro di sistema è abilitato.

Per ottenere la prima password:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Per ottenere la password secondo:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Operazione di eliminazione della replica ha esito negativo con stato non consentito anche se la replica viene eliminata usando il comando di Azure o Azure PowerShell

L'errore si verifica quando l'utente dispone delle autorizzazioni per un registro di sistema ma non dispone delle autorizzazioni di lettura a livello della sottoscrizione. Per risolvere questo problema, assegnare autorizzazioni di lettura per la sottoscrizione all'utente:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Operazioni sui registri

- [Come posso accedere API HTTP di Docker Registry V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Come si eliminano tutti i manifesti che non sono contraddistinti da qualsiasi tag in un repository?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Il motivo per cui l'utilizzo della quota del Registro di sistema non consente di ridurre dopo l'eliminazione di immagini?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Come è possibile convalidare le modifiche di quota di archiviazione?](#how-do-i-validate-storage-quota-changes)
- [Come si autenticano con il Registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Registro contenitori di Azure offerte da configurazione che prevede solo TLS versione 1.2 e su come abilitare TLS 1.2](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Registro contenitori di Azure supporta il Trust contenuto?](#does-azure-container-registry-support-content-trust)
- [Come concedere l'accesso a immagini pull o push senza l'autorizzazione per gestire le risorse del Registro di sistema](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Come si abilita quarantena automatica di immagini per un registro di sistema](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Come posso accedere API HTTP di Docker Registry V2?

Registro contenitori di AZURE supporta l'API HTTP di Docker Registry V2. Le API è possibile accedere in `https://<your registry login server>/v2/`. Esempio: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Come si eliminano tutti i manifesti che non sono contraddistinti da qualsiasi tag in un repository?

Se si usa bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Per Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Note: È possibile aggiungere `-y` nel comando delete per ignorare conferma.

Per altre informazioni, vedere [eliminare le immagini del contenitore in Registro contenitori di Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Il motivo per cui l'utilizzo della quota del Registro di sistema non consente di ridurre dopo l'eliminazione di immagini?

Questa situazione può verificarsi se i livelli sottostanti sono ancora presenti riferimenti da altre immagini del contenitore. Se si elimina un'immagine senza riferimenti, l'utilizzo del Registro di sistema aggiorna in pochi minuti.

### <a name="how-do-i-validate-storage-quota-changes"></a>Come è possibile convalidare le modifiche di quota di archiviazione?

Creare un'immagine con un livello di 1GB utilizzando il seguente file di docker. Ciò garantisce che l'immagine è un livello in cui non è condiviso da qualsiasi altra immagine nel Registro di sistema.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Compilare ed eseguire il push dell'immagine nel Registro di sistema tramite il CLI di docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Dovrebbe essere possibile verificare che l'utilizzo della memoria è aumentato nel portale di Azure oppure è possibile eseguire una query dell'utilizzo con l'interfaccia della riga di comando.

```bash
az acr show-usage -n myregistry
```

Eliminare l'immagine usando il comando di Azure o il portale e controllare l'utilizzo aggiornato tra qualche minuto.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Come si autenticano con il Registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?

È necessario eseguire il contenitore della riga di comando di Azure eseguendo il mounting del socket di Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Nel contenitore, installa `docker`:

```bash
apk --update add docker
```

Quindi eseguire l'autenticazione con il Registro di sistema:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Registro contenitori di Azure offerte da configurazione che prevede solo TLS versione 1.2 e su come abilitare TLS 1.2

Sì. Abilitare TLS usando qualsiasi client docker recente (versione 18.03.0 e versioni successive). 

### <a name="does-azure-container-registry-support-content-trust"></a>Registro contenitori di Azure supporta il Trust contenuto?

Sì, è possibile usare immagini attendibile nel registro contenitori di Azure, poiché il [Notary Docker](https://docs.docker.com/notary/getting_started/) è stato integrato e può essere abilitata. Per informazioni dettagliate, vedere [considerare attendibile contenuto nel registro contenitori di Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Dove si trova il file per l'identificazione personale che si trova?

In `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Le chiavi pubbliche e i certificati di tutti i ruoli (ad eccezione dei ruoli di delega) vengono archiviati nel `root.json`.
* Le chiavi pubbliche e i certificati del ruolo di delega sono archiviati nel file JSON del rispettivo ruolo padre (ad esempio `targets.json` per il `targets/releases` ruolo).

È consigliabile per verificare i certificati e chiavi pubbliche dopo la verifica TUF complessiva eseguita dal client Docker e Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Come concedere l'accesso a immagini pull o push senza l'autorizzazione per gestire le risorse del Registro di sistema

Registro contenitori di AZURE supporta [ruoli personalizzati](container-registry-roles.md) che offrono livelli diversi di autorizzazioni. In particolare `AcrPull` e `AcrPush` ruoli consentono agli utenti di pull e/o push di immagini senza l'autorizzazione per gestire la risorsa del Registro di sistema in Azure.

* Portale di Azure: Il Registro di sistema -> controllo di accesso (IAM) -> Aggiungi (selezionare `AcrPull` o `AcrPush` per il ruolo).
* Interfaccia della riga di comando di Azure: Trovare l'ID risorsa del Registro di sistema eseguendo il comando seguente:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  È possibile assegnare il `AcrPull` oppure `AcrPush` ruolo a un utente (l'esempio seguente usa `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  In alternativa, è possibile assegnare il ruolo a un'entità servizio identificata dal relativo ID applicazione:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

L'assegnatario è quindi in grado di eseguire l'autenticazione e accedere alle immagini nel Registro di sistema.

* Eseguire l'autenticazione a un registro di sistema:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Per elencare i repository:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Eseguire il pull di un'immagine:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Con l'uso di soli i `AcrPull` o `AcrPush` ruolo, l'assegnatario non ha l'autorizzazione per gestire la risorsa del Registro di sistema in Azure. Ad esempio, `az acr list` o `az acr show -n myRegistry` non vengono visualizzati nel Registro di sistema.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Come si abilita quarantena automatica di immagini per un registro di sistema?

Quarantena immagine è attualmente una funzionalità di anteprima del registro contenitori di AZURE. È possibile abilitare la modalità di quarantena di un registro di sistema in modo che solo le immagini che sono stati superati analisi di sicurezza sono visibili agli utenti normali. Per informazioni dettagliate, vedere la [repository GitHub di ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnostica

- [pull docker non riesce con errore: net/http: richiesta annullata durante l'attesa di connessione (Client.Timeout superato durante l'attesa di intestazioni)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [push di docker ha esito positivo ma pull docker non riesce con errore: non autorizzato: autenticazione obbligatoria](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Abilitare e ottenere i log di debug del daemon docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nuove autorizzazioni utente potrebbero non essere valide immediatamente dopo l'aggiornamento](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informazioni di autenticazione non sono specificate in formato corretto in chiamate API REST dirette](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Il motivo per cui il portale di Azure non è elencate tutte il repository o i tag?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Come si raccolgono le tracce http su Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>pull docker non riesce con errore: net/http: richiesta annullata durante l'attesa di connessione (Client.Timeout superato durante l'attesa di intestazioni)

 - Se questo errore è un problema temporaneo, ripetizione dei tentativi avrà esito positivo.
 - Se `docker pull` ha esito negativo in modo continuo, potrebbe essersi verificato un problema con il daemon docker. Il problema può essere ridotto a livello generale, riavviare il daemon docker. 
 - Se si continua a visualizzare questo problema dopo il riavvio del daemon docker, il problema potrebbe essere alcuni problemi di connettività di rete con la macchina. Per verificare se generale di rete nel computer è integro, provare a eseguire un comando, ad esempio `ping www.bing.com`.
 - È sempre un meccanismo di ripetizione dei tentativi in tutte le operazioni client docker.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>push di docker ha esito positivo ma pull docker non riesce con errore: non autorizzato: autenticazione obbligatoria

Questo errore può verificarsi con la versione di Red Hat di daemon docker, in cui `--signature-verification` è abilitato per impostazione predefinita. È possibile controllare le opzioni di daemon docker per Red Hat Enterprise Linux (RHEL) o Fedora eseguendo il comando seguente:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Ad esempio, Fedora 28 Server presenta le seguenti opzioni daemon docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Con `--signature-verification=false` mancanti, `docker pull` ha esito negativo con un errore simile a:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Per risolvere l'errore:
1. Aggiungere l'opzione `--signature-verification=false` al file di configurazione del daemon docker `/etc/sysconfig/docker`. Ad esempio:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Riavviare il servizio daemon docker eseguendo il comando seguente:

  ```bash
  sudo systemctl restart docker.service
  ```

Dettagli della `--signature-verification` reperibili eseguendo `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Abilitare e ottenere i log di debug del daemon docker  

Avviare `dockerd` con il `debug` opzione. In primo luogo, creare il file di configurazione del daemon docker (`/etc/docker/daemon.json`) se non esiste e aggiungere il `debug` opzione:

```json
{   
    "debug": true   
}
```

Quindi, riavviare il daemon. Ad esempio, con Ubuntu 14.04:

```bash
sudo service docker restart
```

I dettagli sono reperibili nel [documentazione di Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * I log possono essere generati in posizioni diverse, a seconda del sistema. Ad esempio, per Ubuntu 14.04, ha `/var/log/upstart/docker.log`.   
Visualizzare [documentazione di Docker](https://docs.docker.com/engine/admin/#read-the-logs) per informazioni dettagliate.    

 * Per Docker per Windows, vengono generati i log in % LOCALAPPDATA%/docker/. Tuttavia non può contenere tutte le informazioni di debug ancora.   

   Per poter accedere al registro del daemon full, potrebbe essere necessario alcuni passaggi aggiuntivi:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    A questo punto si ha accesso a tutti i file delle VM che esegue `dockerd`. Il log si trova `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nuove autorizzazioni utente potrebbero non essere valide immediatamente dopo l'aggiornamento

Quando si concede a nuove autorizzazioni (nuovi ruoli) a un'entità servizio, la modifica potrebbero non vengono applicate immediatamente. Esistono due possibili motivi:

* Ritardo di assegnazione ruolo Azure Active Directory. In genere è veloce, ma potrebbero richiedere minuti a causa di un ritardo di propagazione.
* Ritardo di autorizzazione nel server di token di registro contenitori di AZURE. L'operazione potrebbe richiedere fino a 10 minuti. Per ridurre, è possibile `docker logout` e quindi nuovamente l'autenticazione con lo stesso utente dopo un minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Registro contenitori di AZURE non supporta attualmente l'eliminazione della replica iniziale dagli utenti. La soluzione alternativa consiste nell'includere la replica iniziale crea nel modello ma ignora la sua creazione aggiungendo `"condition": false` come illustrato di seguito:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informazioni di autenticazione non sono specificate in formato corretto in chiamate API REST dirette

È possibile riscontrare un `InvalidAuthenticationInfo` errori, soprattutto se si utilizzano il `curl` dello strumento con l'opzione `-L`, `--location` (per Segui reindirizzamenti).
Ad esempio, il recupero di blob utilizzando `curl` con `-L` opzione e l'autenticazione di base:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

può comportare la risposta seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La causa principale è che alcuni `curl` implementazioni seguono i reindirizzamenti con le intestazioni dalla richiesta originale.

Per risolvere il problema, è necessario seguire i reindirizzamenti manualmente senza le intestazioni. Stampa le intestazioni della risposta con il `-D -` opzione della `curl` e quindi estrarre: il `Location` intestazione:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Il motivo per cui il portale di Azure non è elencate tutte il repository o i tag? 

Se si usa il browser Microsoft Edge, è possibile visualizzare al massimo 100 repository o i tag elencati. Se il Registro di sistema ha più di 100 repository o i tag, è consigliabile utilizzare il browser Firefox o Chrome per elencarli tutti.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Come si raccolgono le tracce http su Windows?

#### <a name="prerequisites"></a>Prerequisiti

- Abilitare la decrittografia https ed eseguirla in fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Abilitare Docker per usare un proxy tramite l'interfaccia utente di Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Assicurarsi di ripristinare sempre al termine dell'esercitazione.  Docker non funzionerà con questa impostazione attivata e fiddler non in esecuzione.

#### <a name="windows-containers"></a>Contenitori Windows

Configurare il proxy di Docker per 127.0.0.1: 8888

#### <a name="linux-containers"></a>Contenitori Linux

Trovare l'indirizzo ip di Docker commutatore virtuale a macchina virtuale:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configurare il proxy di Docker per l'output del comando precedente e la porta 8888 (ad esempio 10.0.75.1:8888)

## <a name="tasks"></a>Attività

- [La modalità di batch di esecuzioni di annullamento?](#how-do-i-batch-cancel-runs)
- [Come includere la cartella. GIT nel comando di az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>La modalità di batch di esecuzioni di annullamento?

I comandi seguenti annullare tutte le attività in esecuzione nel Registro di sistema specificato.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Come includere la cartella. GIT nel comando di az acr build?

Se si passa una cartella di origine locale per il `az acr build` comando, il `.git` cartella è esclusa dal pacchetto caricato per impostazione predefinita. È possibile creare un `.dockerignore` file con l'impostazione seguente. Viene indicato il comando per ripristinare tutti i file in `.git` nel pacchetto caricato. 

```
!.git/**
```

Questa impostazione si applica anche al `az acr run` comando.

## <a name="cicd-integration"></a>Integrazione continua/distribuzione

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Azioni di GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="error-references-for-az-acr-check-health"></a>Riferimenti a errori per `az acr check-health`

### <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Questo errore indica che il client docker per CLI non è stata trovata, che preclude ricerca versione docker, la valutazione dello stato del daemon docker e assicurando comando docker pull può essere eseguito.

*Possibili soluzioni*: installazione del client docker; percorso docker aggiungendo le variabili di sistema.

### <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato del daemon docker non è disponibile o che non è raggiungibile tramite la CLI. Ciò significa che le operazioni di docker (ad esempio, account di accesso, pull) sarà disponibile tramite l'interfaccia della riga di comando.

*Possibili soluzioni*: Riavviare il daemon docker, o verificare che sia installato correttamente.

### <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Questo errore indica che della riga di comando non è riuscito a eseguire il comando `docker --version`.

*Possibili soluzioni*: provare a eseguire il comando manualmente, assicurarsi di avere la versione più recente dell'interfaccia della riga e provare a utilizzare il messaggio di errore.

### <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è in grado di estrarre un'immagine di esempio all'ambiente.

*Possibili soluzioni*: verificare che tutti i componenti necessari per il pull di un'immagine vengano eseguiti correttamente.

### <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Questo errore indica che il client helm non è stato trovato per l'interfaccia della riga di comando, che preclude altre operazioni di helm.

*Possibili soluzioni*: verificare che helm client viene installato e che il relativo percorso viene aggiunto alle variabili di ambiente di sistema.

### <a name="helmversionerror"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando è riuscito a determinare la versione di Helm installata. Questa situazione può verificarsi se la versione della riga di comando di Azure (o se la versione di helm) in uso è obsoleta.

*Possibili soluzioni*: aggiornamento alla versione più recente della riga di comando di Azure o per la versione di helm consigliato; eseguire il comando manualmente ed esaminare il messaggio di errore.

### <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il DNS per il server di accesso del Registro di sistema specificato è stato inviato un ping, ma non ha risposto, ovvero che è disponibile. Questo può indicare problemi di connettività. Questo può anche significare che il Registro di sistema non esiste, che l'utente non dispone di autorizzazioni nel Registro di sistema (per recuperare correttamente il server di accesso) o che nel Registro di sistema di destinazione in un cloud diverso rispetto a quello utilizzato nel comando di Azure.

*Possibili soluzioni*: convalidare la connettività: controllare l'ortografia del Registro di sistema e del Registro di sistema esiste, verificare che l'utente disponga delle autorizzazioni appropriate su di esso e che il cloud del Registro di sistema sia uguale a quello utilizzato nel comando di Azure.

### <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ciò significa che l'endpoint di richiesta per il Registro di sistema specificata ha risposto con uno stato 403 HTTP non è consentito. Ciò significa che gli utenti autorizzati ad accedere al Registro di sistema, probabilmente a causa di una configurazione di rete virtuale.

*Possibili soluzioni*: rimuovere le regole della rete virtuale oppure aggiungere l'indirizzo IP client corrente all'elenco consentiti.

### <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di richiesta di verifica del Registro di sistema di destinazione non ha emesso una richiesta di verifica.

*Possibili soluzioni*: provare più tardi. Se l'errore persiste, aprire problemi am nel https://aka.ms/acr/issues.

### <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di richiesta di verifica del Registro di sistema di destinazione rilasciato una sfida, ma il Registro di sistema non supporta account di accesso AAD.

*Possibili soluzioni*: provare un altro modo la registrazione in, ad esempio, le credenziali di amministratore. Nel caso in cui l'utente desidera accedere con il supporto AAD, Apri problema am https://aka.ms/acr/issues.

### <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ciò significa che il server di accesso del Registro di sistema non ha risposto con un token di aggiornamento, il che significa che è stato negato l'accesso al Registro di sistema di destinazione. Questa situazione può verificarsi se l'utente non ha le autorizzazioni appropriate nel Registro di sistema o se le credenziali dell'utente della riga di comando di Azure sono obsolete.

*Soluzioni potenziali*: verificare se l'utente disponga delle autorizzazioni appropriate nel Registro di sistema; eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

### <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ciò significa che il server di accesso del Registro di sistema non ha risposto con un token di accesso, il che significa che è stato negato l'accesso al Registro di sistema di destinazione. Questa situazione può verificarsi se l'utente non ha le autorizzazioni appropriate nel Registro di sistema o se le credenziali dell'utente della riga di comando di Azure sono obsolete.

*Soluzioni potenziali*: verificare se l'utente disponga delle autorizzazioni appropriate nel Registro di sistema; eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

### <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Ciò significa che l'interfaccia della riga di comando è riuscito a trovare il server di accesso del Registro di sistema specificato e per il cloud corrente è stato trovato alcun suffisso predefinito. Questa situazione può verificarsi se il Registro di sistema non esiste, se l'utente non dispone delle autorizzazioni appropriate nel Registro di sistema, se non corrispondono a cloud del Registro di sistema e il cloud di Azure dell'interfaccia della riga corrente o se la versione della riga di comando di Azure è obsoleta.

*Possibili soluzioni*: verificare che l'ortografia sia corretta e che il Registro di sistema esiste, verifica se l'utente ha le autorizzazioni appropriate nel Registro di sistema e che i cloud del Registro di sistema e dell'ambiente dell'interfaccia della riga corrispondano; della riga di comando di Azure l'aggiornamento alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-registry-intro.md) su registro contenitori di Azure.