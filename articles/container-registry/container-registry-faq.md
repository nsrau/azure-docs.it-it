---
title: Azure Container Registry-Domande frequenti
description: Risposte alle domande frequenti relative al servizio Azure Container Registry
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: cfa8efe0b73811474b1e50a7d2fb1e9abe9045c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286504"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Domande frequenti su Azure Container Registry

Questo articolo descrive le domande frequenti e i problemi noti relativi a Container Registry di Azure.

## <a name="resource-management"></a>Resource management

- [È possibile creare un registro contenitori di Azure usando un modello di Gestione risorse?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [L'analisi delle vulnerabilità di sicurezza per le immagini in ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Ricerca per categorie configurare Kubernetes con Container Registry di Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Ricerca per categorie ottenere le credenziali di amministratore per un registro contenitori?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Ricerca per categorie ottenere le credenziali di amministratore in un modello di Gestione risorse?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [L'eliminazione della replica ha esito negativo con stato non consentito, anche se la replica viene eliminata usando l'Azure PowerShell interfaccia della riga di comando](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Le regole del firewall sono state aggiornate, ma non sono state applicate](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>È possibile creare un Container Registry di Azure usando un modello di Gestione risorse?

Sì. Di seguito è riportato [un modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) che è possibile usare per creare un registro di sistema.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>L'analisi delle vulnerabilità di sicurezza per le immagini in ACR?

Sì. Vedere la documentazione di [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Ricerca per categorie configurare Kubernetes con Container Registry di Azure?

Vedere la documentazione per [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e i passaggi per il [servizio Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Ricerca per categorie ottenere le credenziali di amministratore per un registro contenitori?

> [!IMPORTANT]
> L'account utente amministratore è progettato per consentire a un singolo utente di accedere al registro di sistema, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere [Cenni preliminari sull'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del registro di sistema sia abilitato.

Per ottenere le credenziali tramite l'interfaccia della riga di comando di Azure:

```azurecli
az acr credential show -n myRegistry
```

Uso di Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Ricerca per categorie ottenere le credenziali di amministratore in un modello di Gestione risorse?

> [!IMPORTANT]
> L'account utente amministratore è progettato per consentire a un singolo utente di accedere al registro di sistema, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere [Cenni preliminari sull'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del registro di sistema sia abilitato.

Per ottenere la prima password:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Per ottenere la seconda password:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>L'eliminazione della replica ha esito negativo con stato non consentito, anche se la replica viene eliminata usando l'Azure PowerShell interfaccia della riga di comando

Questo errore si verifica quando l'utente dispone di autorizzazioni per un registro di sistema, ma non dispone delle autorizzazioni a livello di lettura per la sottoscrizione. Per risolvere questo problema, assegnare all'utente le autorizzazioni di lettura per la sottoscrizione:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Le regole del firewall sono state aggiornate, ma non sono state applicate

La propagazione delle modifiche alle regole del firewall richiede tempo. Dopo aver modificato le impostazioni del firewall, attendere alcuni minuti prima di verificare la modifica.


## <a name="registry-operations"></a>Operazioni sui registri

- [Ricerca per categorie accedere all'API HTTP V2 del registro di sistema Docker?](#how-do-i-access-docker-registry-http-api-v2)
- [Ricerca per categorie eliminare tutti i manifesti a cui non viene fatto riferimento da alcun tag in un repository?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Perché l'utilizzo della quota del registro di sistema non viene ridotto dopo l'eliminazione di immagini?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Ricerca per categorie convalidare le modifiche della quota di archiviazione?](#how-do-i-validate-storage-quota-changes)
- [Ricerca per categorie eseguire l'autenticazione con il registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry offre la configurazione solo TLS v 1.2 e come abilitare TLS v 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry supporta l'attendibilità del contenuto?](#does-azure-container-registry-support-content-trust)
- [Ricerca per categorie concedere l'accesso alle immagini pull o push senza autorizzazione per la gestione della risorsa del registro di sistema?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Ricerca per categorie abilitare la quarantena delle immagini automatica per un registro](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Ricerca per categorie accedere all'API HTTP V2 del registro di sistema Docker?

ACR supporta l'API HTTP V2 del registro docker. È possibile accedere alle API a `https://<your registry login server>/v2/`. Esempio: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Ricerca per categorie eliminare tutti i manifesti a cui non viene fatto riferimento da alcun tag in un repository?

Se si è in bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Per PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: È possibile aggiungere `-y` nel comando Delete per ignorare la conferma.

Per altre informazioni, vedere [eliminare immagini del contenitore in Azure container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Perché l'utilizzo della quota del registro di sistema non viene ridotto dopo l'eliminazione di immagini?

Questa situazione può verificarsi se le altre immagini del contenitore fanno ancora riferimento ai livelli sottostanti. Se si elimina un'immagine senza riferimenti, l'utilizzo del registro di sistema viene aggiornato in pochi minuti.

### <a name="how-do-i-validate-storage-quota-changes"></a>Ricerca per categorie convalidare le modifiche della quota di archiviazione?

Creare un'immagine con un livello da 1 GB usando il file Docker seguente. In questo modo si garantisce che l'immagine disponga di un livello non condiviso da nessun'altra immagine nel registro di sistema.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Compilare ed eseguire il push dell'immagine nel registro usando l'interfaccia della riga di comando di Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Si dovrebbe essere in grado di verificare che l'utilizzo dell'archiviazione sia aumentato nel portale di Azure oppure è possibile eseguire query sull'utilizzo usando l'interfaccia della riga di comando.

```bash
az acr show-usage -n myregistry
```

Eliminare l'immagine usando l'interfaccia della riga di comando di Azure o il portale e controllare l'utilizzo aggiornato in pochi minuti.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Ricerca per categorie eseguire l'autenticazione con il registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?

È necessario eseguire il contenitore dell'interfaccia della riga di comando di Azure montando il socket docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Nel contenitore installare `docker`:

```bash
apk --update add docker
```

Eseguire quindi l'autenticazione con il registro di sistema:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry offre la configurazione solo TLS v 1.2 e come abilitare TLS v 1.2?

Sì. Abilitare TLS usando un client Docker recente (versione 18.03.0 e successive). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry supporta l'attendibilità del contenuto?

Sì, è possibile usare immagini attendibili in Azure Container Registry, perché [il notatore Docker](https://docs.docker.com/notary/getting_started/) è stato integrato e può essere abilitato. Per informazioni dettagliate, vedere [attendibilità del contenuto in Azure container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Dove si trova il file per l'identificazione personale?

In `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Le chiavi pubbliche e i certificati di tutti i ruoli (eccetto i ruoli di delega) vengono archiviati nel `root.json`.
* Le chiavi pubbliche e i certificati del ruolo di delega vengono archiviati nel file JSON del relativo ruolo padre, ad esempio `targets.json` per il ruolo `targets/releases`.

Si consiglia di verificare le chiavi pubbliche e i certificati dopo la verifica complessiva del TUF eseguita da Docker e da notary client.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Ricerca per categorie concedere l'accesso alle immagini pull o push senza autorizzazione per la gestione della risorsa del registro di sistema?

ACR supporta [ruoli personalizzati](container-registry-roles.md) che forniscono diversi livelli di autorizzazioni. In particolare, i ruoli `AcrPull` e `AcrPush` consentono agli utenti di eseguire il pull e/o il push di immagini senza l'autorizzazione a gestire la risorsa del registro di sistema in Azure.

* Portale di Azure: Il controllo di accesso (IAM) del registro di sistema > > aggiungere (selezionare `AcrPull` o `AcrPush` per il ruolo).
* Interfaccia della riga di comando di Azure: Trovare l'ID risorsa del registro di sistema eseguendo il comando seguente:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  È quindi possibile assegnare il ruolo `AcrPull` o `AcrPush` a un utente (l'esempio seguente usa `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  In alternativa, assegnare il ruolo a un principio di servizio identificato dal relativo ID applicazione:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Il cessionario è quindi in grado di autenticare e accedere alle immagini nel registro di sistema.

* Per eseguire l'autenticazione a un registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Per elencare i repository:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Per eseguire il pull di un'immagine:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Con l'uso solo del ruolo `AcrPull` o `AcrPush`, l'assegnatario non dispone dell'autorizzazione per gestire la risorsa del registro di sistema in Azure. Ad esempio, `az acr list` o `az acr show -n myRegistry` non visualizzerà il registro di sistema.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Ricerca per categorie abilitare la quarantena delle immagini automatica per un registro?

La quarantena delle immagini è attualmente una funzionalità di anteprima di ACR. È possibile abilitare la modalità di quarantena di un registro di sistema in modo che solo le immagini che hanno superato correttamente l'analisi di sicurezza siano visibili agli utenti normali. Per informazioni dettagliate, vedere il [repository di GitHub ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Controlli di diagnostica e integrità

- [Controllare l'integrità con `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull ha esito negativo con errore: NET/http: richiesta annullata durante l'attesa della connessione (il client. timeout supera le intestazioni in attesa)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [il push di Docker ha esito positivo, ma docker pull ha esito negativo con errore: non autorizzato: autenticazione obbligatoria](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Abilitare e ottenere i log di debug del daemon Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Le nuove autorizzazioni utente potrebbero non essere valide immediatamente dopo l'aggiornamento](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Le informazioni di autenticazione non sono specificate nel formato corretto sulle chiamate API REST dirette](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Perché il portale di Azure non elenca tutti i repository o i tag?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Perché il portale di Azure non riesce a recuperare i repository o i tag?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Ricerca per categorie raccogliere tracce http in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Controllare l'integrità con `az acr check-health`

Per risolvere i problemi comuni relativi all'ambiente e al registro di sistema, vedere [verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull ha esito negativo con errore: NET/http: richiesta annullata durante l'attesa della connessione (il client. timeout supera le intestazioni in attesa)

 - Se questo errore è un problema temporaneo, il tentativo avrà esito positivo.
 - Se `docker pull` ha esito negativo, potrebbe essersi verificato un problema con il daemon docker. Il problema può in genere essere mitigato riavviando il daemon docker. 
 - Se si continua a visualizzare questo problema dopo il riavvio del daemon Docker, il problema potrebbe essere dovuto ad alcuni problemi di connettività di rete con il computer. Per verificare se la rete generale nel computer è integro, eseguire il comando seguente per testare la connettività dell'endpoint. La versione minima di `az acr` che contiene questo comando di verifica della connettività è 2.2.9. Aggiornare l'interfaccia della riga di comando di Azure se si usa una versione precedente.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - È necessario disporre sempre di un meccanismo di ripetizione dei tentativi per tutte le operazioni del client docker.

### <a name="docker-pull-is-slow"></a>Il pull di Docker è lento
Utilizzare [questo](http://www.azurespeed.com/Azure/Download) strumento per testare la velocità di download della rete del computer. Se la rete del computer è lenta, provare a usare una macchina virtuale di Azure nella stessa area del registro. Questo consente in genere di velocizzare la velocità di rete.

### <a name="docker-push-is-slow"></a>Il push di Docker è lento
Utilizzare [questo](http://www.azurespeed.com/Azure/Upload) strumento per testare la velocità di caricamento della rete del computer. Se la rete del computer è lenta, provare a usare una macchina virtuale di Azure nella stessa area del registro. Questo consente in genere di velocizzare la velocità di rete.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Il push di Docker ha esito positivo, ma docker pull ha esito negativo con errore: non autorizzato: autenticazione obbligatoria

Questo errore può verificarsi con la versione Red Hat del daemon Docker, in cui `--signature-verification` è abilitato per impostazione predefinita. È possibile controllare le opzioni del daemon Docker per Red Hat Enterprise Linux (RHEL) o Fedora eseguendo il comando seguente:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Ad esempio, Fedora 28 server dispone delle seguenti opzioni del daemon docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Con `--signature-verification=false` mancante, `docker pull` ha esito negativo e restituisce un errore simile al seguente:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Per risolvere l'errore:
1. Aggiungere l'opzione `--signature-verification=false` al file di configurazione del daemon Docker `/etc/sysconfig/docker`. Ad esempio:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Riavviare il servizio Docker daemon eseguendo il comando seguente:

  ```bash
  sudo systemctl restart docker.service
  ```

È possibile trovare i dettagli di `--signature-verification` eseguendo `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Abilitare e ottenere i log di debug del daemon Docker  

Avviare `dockerd` con l'opzione `debug`. Prima di tutto, creare il file di configurazione del daemon Docker (`/etc/docker/daemon.json`) se non esiste e aggiungere l'opzione `debug`:

```json
{   
    "debug": true   
}
```

Quindi, riavviare il daemon. Ad esempio, con Ubuntu 14,04:

```bash
sudo service docker restart
```

Informazioni dettagliate sono disponibili nella [documentazione di Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * I log possono essere generati in posizioni diverse, a seconda del sistema. Per Ubuntu 14,04, ad esempio, è `/var/log/upstart/docker.log`.   
Per informazioni dettagliate, vedere la [documentazione di Docker](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * Per Docker per Windows, i log vengono generati in% LOCALAPPDATA%/docker/. Tuttavia, non può contenere ancora tutte le informazioni di debug.   

   Per accedere al log del daemon completo, potrebbero essere necessari alcuni passaggi aggiuntivi:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    A questo punto è possibile accedere a tutti i file della macchina virtuale che esegue `dockerd`. Il log si trova in `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Le nuove autorizzazioni utente potrebbero non essere valide immediatamente dopo l'aggiornamento

Quando si concedono nuove autorizzazioni (nuovi ruoli) a un'entità servizio, la modifica potrebbe non avere effetto immediato. Esistono due possibili motivi:

* Ritardo dell'assegnazione di ruolo Azure Active Directory. Normalmente è veloce, ma potrebbero essere necessari pochi minuti a causa del ritardo della propagazione.
* Ritardo delle autorizzazioni nel server del token ACR. Questa operazione potrebbe richiedere fino a 10 minuti. Per attenuare, è possibile `docker logout` e quindi eseguire di nuovo l'autenticazione con lo stesso utente dopo 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Attualmente ACR non supporta l'eliminazione della replica domestica da parte degli utenti. La soluzione alternativa consiste nell'includere la replica Home create nel modello, ma ignorarne la creazione aggiungendo `"condition": false`, come illustrato di seguito:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Le informazioni di autenticazione non sono specificate nel formato corretto sulle chiamate API REST dirette

È possibile che si verifichi un errore `InvalidAuthenticationInfo`, in particolare usando lo strumento `curl` con l'opzione `-L`, `--location` (per seguire i reindirizzamenti).
Ad esempio, il recupero del BLOB con `curl` con l'opzione `-L` e l'autenticazione di base:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

può produrre la risposta seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La causa principale è che alcune implementazioni `curl` seguono i reindirizzamenti con le intestazioni dalla richiesta originale.

Per risolvere il problema, è necessario seguire manualmente i reindirizzamenti senza le intestazioni. Stampare le intestazioni della risposta con l'opzione `-D -` di `curl` e quindi estrarre: l'intestazione `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Perché il portale di Azure non elenca tutti i repository o i tag? 

Se si usa il browser Microsoft Edge/IE, è possibile visualizzare al massimo 100 repository o tag. Se il registro di sistema contiene più di 100 repository o tag, è consigliabile usare il browser Firefox o Chrome per elencarli tutti.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Perché il portale di Azure non riesce a recuperare i repository o i tag?

Il browser potrebbe non essere in grado di inviare la richiesta di recupero dei repository o dei tag al server. Potrebbero essere presenti diversi motivi, ad esempio:

* Mancanza di connettività di rete
* Firewall
* Blocchi ad
* Errori DNS

Contattare l'amministratore di rete o verificare la configurazione e la connettività di rete. Provare a eseguire `az acr check-health -n yourRegistry` usando l'interfaccia della riga di comando di Azure per verificare se l'ambiente in uso è in grado di connettersi al Container Registry. Inoltre, è anche possibile provare una sessione in incognito o privata nel browser per evitare la cache o i cookie del browser obsoleti.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Ricerca per categorie raccogliere tracce http in Windows?

#### <a name="prerequisites"></a>Prerequisiti

- Abilitare la decrittografia di HTTPS in Fiddler: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Abilitare Docker per l'uso di un proxy tramite l'interfaccia utente di Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Assicurarsi di annullare il ripristino al termine dell'operazione.  Docker non funziona con questo abilitato e Fiddler non è in esecuzione.

#### <a name="windows-containers"></a>Contenitori Windows

Configurare il proxy Docker su 127.0.0.1:8888

#### <a name="linux-containers"></a>Contenitori Linux

Trovare l'indirizzo IP del Commuter virtuale della VM docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configurare il proxy Docker per l'output del comando precedente e la porta 8888 (ad esempio, 10.0.75.1:8888)

## <a name="tasks"></a>Attività

- [Ricerca per categorie viene eseguito l'annullamento del batch?](#how-do-i-batch-cancel-runs)
- [Ricerca per categorie includere la cartella. git nel comando AZ ACR Build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Ricerca per categorie viene eseguito l'annullamento del batch?

I comandi seguenti annullano tutte le attività in esecuzione nel registro di sistema specificato.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Ricerca per categorie includere la cartella. git nel comando AZ ACR Build?

Se si passa una cartella di origine locale al comando `az acr build`, per impostazione predefinita la cartella `.git` viene esclusa dal pacchetto caricato. È possibile creare un file `.dockerignore` con la seguente impostazione. Indica al comando di ripristinare tutti i file in `.git` nel pacchetto caricato. 

```
!.git/**
```

Questa impostazione si applica anche al comando `az acr run`.

## <a name="cicd-integration"></a>Integrazione CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Azioni di GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più](container-registry-intro.md) su Azure container Registry.
