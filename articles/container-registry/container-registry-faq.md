---
title: Domande frequenti
description: Risposte per le domande frequenti relative al servizio Registro contenitori di AzureAnswers for frequently asked questions related to the Azure Container Registry service
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403219"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Domande frequenti sul Registro di sistema dei contenitori di AzureFrequently asked questions about Azure Container Registry

Questo articolo risolve le domande frequenti e i problemi noti relativi al Registro di sistema dei contenitori di Azure.This article addresses frequently asked questions and known issues about Azure Container Registry.

## <a name="resource-management"></a>Resource management

- [È possibile creare un registro contenitori di Azure usando un modello di Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Esiste la scansione delle vulnerabilità di sicurezza per le immagini in ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Come si configurano le Kubernetes con il Registro di sistema del contenitore di Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Come si ottengono le credenziali di amministratore per un registro contenitori?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Come si ottengono le credenziali di amministratore in un modello di Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [L'eliminazione della replica ha esito negativo con stato non consentito anche se la replica viene eliminata tramite l'interfaccia della riga della riga di comando di Azure o Azure PowerShellDelete of replication fails with Forbidden status although the replication gets deleted using the Azure CLI or Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Le regole del firewall vengono aggiornate correttamente ma non hanno effetto](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>È possibile creare un Registro di sistema del contenitore di Azure usando un modello di Resource Manager?

Sì. Di seguito è [riportato un modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) che è possibile utilizzare per creare un Registro di sistema.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Esiste la scansione delle vulnerabilità di sicurezza per le immagini in ACR?

Sì. Vedere la documentazione del [Centro sicurezza di Azure,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Come si configurano le Kubernetes con il Registro di sistema del contenitore di Azure?

Vedere la documentazione di [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e la procedura per il [servizio Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Come si ottengono le credenziali di amministratore per un registro contenitori?

> [!IMPORTANT]
> L'account utente amministratore è progettato per un singolo utente per accedere al Registro di sistema, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere [Panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del Registro di sistema sia abilitato.

Per ottenere le credenziali usando l'interfaccia della riga di comando di Azure:To get credentials using the Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Uso di Azure Powershell:Using Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Come si ottengono le credenziali di amministratore in un modello di Resource Manager?

> [!IMPORTANT]
> L'account utente amministratore è progettato per un singolo utente per accedere al Registro di sistema, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere [Panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del Registro di sistema sia abilitato.

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>L'eliminazione della replica ha esito negativo con stato non consentito anche se la replica viene eliminata tramite l'interfaccia della riga della riga di comando di Azure o Azure PowerShellDelete of replication fails with Forbidden status although the replication gets deleted using the Azure CLI or Azure PowerShell

L'errore si verifica quando l'utente dispone delle autorizzazioni per un Registro di sistema ma non dispone di autorizzazioni a livello di lettore per la sottoscrizione. Per risolvere questo problema, assegnare le autorizzazioni di lettura per la sottoscrizione all'utente:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Le regole del firewall vengono aggiornate correttamente ma non hanno effetto

La propagazione delle modifiche alle regole del firewall richiede del tempo. Dopo aver modificato le impostazioni del firewall, attendere alcuni minuti prima di verificare questa modifica.


## <a name="registry-operations"></a>Operazioni sui registri

- [Come si accede all'API HTTP del Registro di sistema Docker V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Come si eliminano tutti i manifesti a cui non fa riferimento alcun tag in un repository?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Perché l'utilizzo della quota del Registro di sistema non si riduce dopo l'eliminazione delle immagini?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Come si convalidano le modifiche alle quote di archiviazione?](#how-do-i-validate-storage-quota-changes)
- [Come si esegue l'autenticazione con il Registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Come abilitare TLS 1.2?](#how-to-enable-tls-12)
- [Azure Container Registry supporta Content Trust?](#does-azure-container-registry-support-content-trust)
- [Come si concede l'accesso alle immagini pull o push senza l'autorizzazione per gestire la risorsa del Registro di sistema?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Come abilitare la quarantena automatica delle immagini per un Registro di sistema](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Come si accede all'API HTTP del Registro di sistema Docker V2?

ACR supporta Docker Registry HTTP API V2. È possibile accedere alle `https://<your registry login server>/v2/`API in . Esempio: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Come si eliminano tutti i manifesti a cui non fa riferimento alcun tag in un repository?

Se sei su bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Per Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: È `-y` possibile aggiungere il comando delete per ignorare la conferma.

Per altre informazioni, vedere [Eliminare immagini del contenitore nel Registro](container-registry-delete.md)di sistema del contenitore di Azure.For more information, see Delete container images in Azure Container Registry.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Perché l'utilizzo della quota del Registro di sistema non si riduce dopo l'eliminazione delle immagini?

Questa situazione può verificarsi se i livelli sottostanti sono ancora referenziati da altre immagini contenitore. Se si elimina un'immagine senza riferimenti, l'utilizzo del Registro di sistema viene aggiornato in pochi minuti.

### <a name="how-do-i-validate-storage-quota-changes"></a>Come si convalidano le modifiche alle quote di archiviazione?

Creare un'immagine con un livello da 1 GB utilizzando il file docker seguente. Ciò garantisce che l'immagine abbia un livello non condiviso da altre immagini nel Registro di sistema.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Compilare e eseguire il push dell'immagine nel Registro di sistema utilizzando l'interfaccia della riga di comando della finestra mobile.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Dovrebbe essere possibile verificare che l'utilizzo dello spazio di archiviazione sia aumentato nel portale di Azure oppure che sia possibile eseguire query sull'utilizzo tramite l'interfaccia della riga di comando.

```azurecli
az acr show-usage -n myregistry
```

Eliminare l'immagine usando l'interfaccia della riga di comando o il portale di Azure e controllare l'utilizzo aggiornato in pochi minuti.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Come si esegue l'autenticazione con il Registro di sistema quando si esegue l'interfaccia della riga di comando in un contenitore?

È necessario eseguire il contenitore dell'interfaccia della riga di comando di Azure montando il socket Docker:You need to run the Azure CLI container by mounting the Docker socket:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Nel contenitore, `docker`installare :

```bash
apk --update add docker
```

Quindi eseguire l'autenticazione con il Registro di sistema:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Come abilitare TLS 1.2?

Abilitare TLS 1.2 utilizzando qualsiasi client docker recente (versione 18.03.0 e successive). 

> [!IMPORTANT]
> A partire dal 13 gennaio 2020, Registro Azure Container richiederà l'uso di TLS 1.2 in tutte le connessioni sicure da server e applicazioni. Il supporto per TLS 1.0 e 1.1 verrà ritirato.

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry supporta Content Trust?

Sì, è possibile usare immagini attendibili nel Registro di sistema del contenitore di Azure, poiché il [Notaio Docker](https://docs.docker.com/notary/getting_started/) è stato integrato e può essere abilitato. Per informazioni dettagliate, vedere [Attendibilità del contenuto nel Registro](container-registry-content-trust.md)di sistema del contenitore di Azure.For details, see Content Trust in Azure Container Registry .


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Dove si trova il file per l'identificazione personale?

In `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Le chiavi pubbliche e i certificati di tutti `root.json`i ruoli,ad eccezione dei ruoli di delega, vengono archiviati nella .
* Le chiavi pubbliche e i certificati del ruolo di delega vengono `targets.json` archiviati nel file JSON del ruolo padre, ad esempio per il `targets/releases` ruolo.

Si consiglia di verificare le chiavi pubbliche e i certificati dopo la verifica TUF complessiva eseguita dal client Docker e Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Come si concede l'accesso alle immagini pull o push senza l'autorizzazione per gestire la risorsa del Registro di sistema?

ACR supporta [ruoli personalizzati](container-registry-roles.md) che forniscono diversi livelli di autorizzazioni. In `AcrPull` particolare, i `AcrPush` ruoli consentono agli utenti di estrarre e/o eseguire il push delle immagini senza l'autorizzazione per gestire la risorsa del Registro di sistema in Azure.Specifically, and roles allow users to pull and/or push images without the permission to manage the registry resource in Azure.

* Portale di Azure: il registro -> Access Control `AcrPull` `AcrPush` (IAM) -> Aggiungi (Selezionare o per il ruolo).
* Interfaccia della riga di comando di Azure: trovare l'ID risorsa del Registro di sistema eseguendo il comando seguente:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  È quindi possibile `AcrPull` `AcrPush` assegnare il ruolo o `AcrPull`a un utente (l'esempio seguente usa ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  In alternativa, assegnare il ruolo a un principio di servizio identificato dal relativo ID applicazione:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

L'assegnatario è quindi in grado di autenticare e accedere alle immagini nel Registro di sistema.

* Per eseguire l'autenticazione in un Registro di sistema:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Per elencare i repository:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Per estrarre un'immagine:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Con l'uso `AcrPull` solo `AcrPush` del ruolo o , l'assegnatario non dispone dell'autorizzazione per gestire la risorsa del Registro di sistema in Azure.With the use of only the or role, the assignee doesn't have the permission to manage the registry resource in Azure. Ad `az acr list` esempio, `az acr show -n myRegistry` o non mostrerà il Registro di sistema.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Come si attiva la quarantena automatica delle immagini per un Registro di sistema?

La quarantena delle immagini è attualmente una funzionalità di anteprima di ACR. È possibile attivare la modalità di quarantena di un Registro di sistema in modo che solo le immagini che hanno superato correttamente la scansione di sicurezza siano visibili agli utenti normali. Per informazioni dettagliate, vedere il [repository ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Diagnostica e controlli di integrità

- [Controllare l'integrità con`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull ha esito negativo con errore: net/http: richiesta annullata durante l'attesa della connessione (Client.Timeout superato durante l'attesa di intestazioni)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push ha esito positivo ma docker pull non riesce con errore: non autorizzato: autenticazione richiesta](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`ha esito positivo, ma i comandi docker non riesce con errore: non autorizzato: autenticazione necessaria](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Abilitare e ottenere i registri di debug del daemon docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Le nuove autorizzazioni utente potrebbero non essere effettive immediatamente dopo l'aggiornamento](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Le informazioni di autenticazione non vengono fornite nel formato corretto nelle chiamate all'API REST diretteAuthentication information is not given in the correct format on direct REST API calls](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Perché il portale di Azure non elenca tutti i repository o i tag?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Perché il portale di Azure non riesce a recuperare repository o tag?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Perché la richiesta pull o push ha esito negativo con l'operazione non consentita?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Come si raccolgono le tracce http in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Controllare l'integrità con`az acr check-health`

Per risolvere i problemi comuni relativi all'ambiente e al Registro di sistema, vedere [Controllare l'integrità di un registro contenitori](container-registry-check-health.md)di Azure.To troubleshoot common environment and registry issues, see Check the health of an Azure container registry .

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull ha esito negativo con errore: net/http: richiesta annullata durante l'attesa della connessione (Client.Timeout superato durante l'attesa di intestazioni)

 - Se questo errore è un problema temporaneo, quindi riprovare avrà esito positivo.
 - Se `docker pull` non riesce continuamente, allora potrebbe esserci un problema con il daemon Docker. Il problema può generalmente essere attenuato riavviando il daemon Docker. 
 - Se il problema persiste dopo il riavvio di Docker daemon, il problema potrebbe essere dovuto ad alcuni problemi di connettività di rete con il computer. Per verificare se la rete generale del computer è integro, eseguire il comando seguente per verificare la connettività dell'endpoint. La `az acr` versione minima che contiene questo comando di controllo della connettività è 2.2.9.The minimum version that contains this connectivity check command is 2.2.9. Aggiornare l'interfaccia della riga di comando di Azure se si usa una versione precedente.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - È sempre necessario disporre di un meccanismo di ripetizione dei tentativi in tutte le operazioni client Docker.You should always have a retry mechanism on all Docker client operations.

### <a name="docker-pull-is-slow"></a>Docker tirare è lento
Utilizzare [questo](http://www.azurespeed.com/Azure/Download) strumento per testare la velocità di download della rete del computer. Se la rete del computer è lenta, è consigliabile usare la macchina virtuale di Azure nella stessa area del Registro di sistema. Questo di solito ti dà una velocità di rete più veloce.

### <a name="docker-push-is-slow"></a>Docker push è lento
Utilizzare [questo](http://www.azurespeed.com/Azure/Upload) strumento per testare la velocità di caricamento della rete della macchina. Se la rete del computer è lenta, è consigliabile usare la macchina virtuale di Azure nella stessa area del Registro di sistema. Questo di solito ti dà una velocità di rete più veloce.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push ha esito positivo ma docker pull ha esito negativo con errore: non autorizzato: autenticazione necessariaDocker push succeeds but docker pull fails with error: unauthorized: authentication required

Questo errore può verificarsi con la versione Red Hat `--signature-verification` del daemon Docker, dove è abilitato per impostazione predefinita. È possibile controllare le opzioni di daemon Docker per Red Hat Enterprise Linux (RHEL) o Fedora eseguendo il comando seguente:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Ad esempio, Fedora 28 Server ha le seguenti opzioni docker daemon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Con `--signature-verification=false` mancante, `docker pull` ha esito negativo con un errore simile al:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Per risolvere l'errore:
1. Aggiungere l'opzione `--signature-verification=false` al file `/etc/sysconfig/docker`di configurazione Docker daemon . Ad esempio:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Riavviare il servizio daemon Docker eseguendo il comando seguente:
   
   ```bash
   sudo systemctl restart docker.service
   ```

I `--signature-verification` dettagli di possono `man dockerd`essere trovati eseguendo .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login ha esito positivo ma docker ha esito negativo con errore: non autorizzato: autenticazione necessaria

Assicurarsi di utilizzare un URL del server `docker push myregistry.azurecr.io/myimage:latest`tutto minuscolo, ad esempio , anche `myRegistry`se il nome della risorsa del Registro di sistema è maiuscolo o misto, ad esempio .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Abilitare e ottenere i registri di debug del daemon Docker  

Iniziare `dockerd` con `debug` l'opzione. Creare innanzitutto il file di configurazione`/etc/docker/daemon.json`Docker daemon ( ) `debug` se non esiste e aggiungere l'opzione:

```json
{   
    "debug": true   
}
```

Quindi, riavviare il daemon. Ad esempio, con Ubuntu 14.04:

```bash
sudo service docker restart
```

I dettagli sono disponibili nella documentazione di [Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * I registri possono essere generati in posizioni diverse, a seconda del sistema. Ad esempio, per Ubuntu 14.04, è `/var/log/upstart/docker.log`.   
Per informazioni dettagliate, vedere la documentazione di [Docker.See Docker documentation](https://docs.docker.com/engine/admin/#read-the-logs) for details.    

 * Per Docker per Windows, i log vengono generati in %LOCALAPPDATA%/docker/. Tuttavia potrebbe non contenere ancora tutte le informazioni di debug.   

   Per accedere al registro completo dei daemon, potrebbero essere necessari alcuni passaggi aggiuntivi:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    A questo punto si ha accesso `dockerd`a tutti i file della macchina virtuale in esecuzione. Il registro `/var/log/docker.log`è in .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Le nuove autorizzazioni utente potrebbero non essere effettive immediatamente dopo l'aggiornamento

Quando si concedono nuove autorizzazioni (nuovi ruoli) a un'entità servizio, la modifica potrebbe non avere effetto immediato. Le ragioni possibili sono due:

* Ritardo dell'assegnazione dei ruoli di Azure Active Directory.Azure Active Directory role assignment delay. Normalmente è veloce, ma potrebbero volerci minuti a causa del ritardo di propagazione.
* Ritardo di autorizzazione nel server di token ACR. L'operazione potrebbe richiedere fino a 10 minuti. Per attenuare, è `docker logout` possibile e quindi eseguire nuovamente l'autenticazione con lo stesso utente dopo 1 minuto:To mitigate, you can and then authenticate again with the same user after 1 minute:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Attualmente ACR non supporta l'eliminazione della replica domestica da parte degli utenti. La soluzione consiste nell'includere la replica principale creata `"condition": false` nel modello, ma ignorare la creazione aggiungendo come illustrato di seguito:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Le informazioni di autenticazione non vengono fornite nel formato corretto nelle chiamate all'API REST diretteAuthentication information is not given in the correct format on direct REST API calls

È possibile `InvalidAuthenticationInfo` riscontrare un `curl` errore, in `-L` `--location` particolare utilizzando lo strumento con l'opzione , (per seguire i reindirizzamenti).
Ad esempio, il recupero `curl` `-L` del BLOB usando l'opzione con l'opzione e l'autenticazione di base:For example, fetching the blob using with option and basic authentication:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

può provocare la seguente risposta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La causa principale `curl` è che alcune implementazioni seguono reindirizzamenti con intestazioni dalla richiesta originale.

Per risolvere il problema, è necessario seguire i reindirizzamenti manualmente senza le intestazioni. Stampare le intestazioni `-D -` di `curl` risposta con `Location` l'opzione di estrai: l'intestazione:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Perché il portale di Azure non elenca tutti i repository o i tag? 

Se si utilizza il browser Microsoft Edge/IE, è possibile visualizzare al massimo 100 repository o tag. Se il registro ha più di 100 repository o tag, si consiglia di utilizzare il browser Firefox o Chrome per elencarli tutti.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Perché il portale di Azure non riesce a recuperare repository o tag?

Il browser potrebbe non essere in grado di inviare la richiesta di recupero di repository o tag al server. Ci potrebbero essere vari motivi come:

* Mancanza di connettività di rete
* Firewall
* Blocchi degli annunci
* Errori DNS

Contattare l'amministratore di rete o verificare la configurazione e la connettività della rete. Provare `az acr check-health -n yourRegistry` a eseguire usando l'interfaccia della riga di comando di Azure per verificare se l'ambiente è in grado di connettersi al Registro di sistema del contenitore. Inoltre, si potrebbe anche provare una sessione in incognito o privato nel browser per evitare qualsiasi cache del browser non aggiornato o cookie.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Perché la richiesta pull o push ha esito negativo con l'operazione non consentita?

Di seguito sono riportati alcuni scenari in cui le operazioni potrebbero non essere consentite:Here are some scenarios where operations maybe disallowed:
* I registri classici non sono più supportati. Eseguire l'aggiornamento a uno SKU supportato usando [az acr update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) o il portale di Azure.Please upgrade to a supported [SKUs](https://aka.ms/acr/skus) using az acr update or the Azure portal.
* L'immagine o il repository potrebbe essere bloccato in modo che non possa essere eliminato o aggiornato. È possibile utilizzare il comando [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) per visualizzare gli attributi correnti.
* Alcune operazioni non sono consentite se l'immagine è in quarantena. Ulteriori informazioni sulla [quarantena](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Come si raccolgono le tracce http in Windows?

#### <a name="prerequisites"></a>Prerequisiti

- Abilitare la decrittografia di https in fiddler:Enable decrypting https in fiddler:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Abilitare Docker per l'utilizzo di un proxy tramite l'interfaccia utente di Docker:Enable Docker to use a proxy through the Docker ui:<https://docs.docker.com/docker-for-windows/#proxies>
- Assicurarsi di ripristinare al termine.  Docker non funziona con questo abilitato e violinista non in esecuzione.

#### <a name="windows-containers"></a>Contenitori Windows

Configurare il proxy Docker su 127.0.0.1:8888

#### <a name="linux-containers"></a>Contenitori Linux

Trovare l'IP del commutatore virtuale vm vm Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configurare il proxy Docker per l'output del comando precedente e la porta 8888 (ad esempio 10.0.75.1:8888)

## <a name="tasks"></a>Attività

- [Come si esegue l'annullamento in batch?](#how-do-i-batch-cancel-runs)
- [Come si include la cartella .git nel comando az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Le attività supportano gitLab per i trigger di origine?](#does-tasks-support-gitlab-for-source-triggers)
- [Quale servizio di gestione del repository git supporta le attività?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Come si esegue l'annullamento in batch?

I comandi seguenti annullano tutte le attività in esecuzione nel Registro di sistema specificato.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Come si include la cartella .git nel comando az acr build?

Se si passa una cartella `az acr build` di `.git` origine locale al comando, la cartella viene esclusa dal pacchetto caricato per impostazione predefinita. È possibile `.dockerignore` creare un file con l'impostazione seguente. Indica al comando di ripristinare `.git` tutti i file nel pacchetto caricato. 

`!.git/**`

Questa impostazione si `az acr run` applica anche al comando.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Le attività supportano gitLab per i trigger di origine?

Attualmente non è supportato GitLab per i trigger di origine.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Quale servizio di gestione del repository git supporta le attività?

| Servizio Git | Contesto di origine | Compilazione manuale | Compilazione automatica tramite trigger di commitAuto build through commit trigger |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Sì | Sì |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Sì | Sì |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Sì | No |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Sì | No |

## <a name="run-error-message-troubleshooting"></a>Risoluzione dei problemi relativi ai messaggi di errore

| Messaggio di errore | Guida per la risoluzione dei problemi |
|---|---|
|Non è stato configurato alcun accesso per la macchina virtuale, pertanto non sono state trovate sottoscrizioni|Questo potrebbe accadere `az login --identity` se si utilizza nella vostra attività ACR. Si tratta di un errore temporaneo che si verifica quando l'assegnazione di ruolo dell'identità gestita non è stata propagata. Attendere alcuni secondi prima di riprovare funziona.|

## <a name="cicd-integration"></a>Integrazione CI/CD

- [CerchioCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Azioni GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-registry-intro.md) sul Registro di sistema del contenitore di Azure.Learn more about Azure Container Registry.
