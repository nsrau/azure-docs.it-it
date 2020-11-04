---
title: Domande frequenti
description: Risposte alle domande frequenti sul servizio Registro Azure Container
author: sajayantony
ms.topic: article
ms.date: 09/18/2020
ms.author: sajaya
ms.openlocfilehash: a2cddc9bbe868a2d18ee8111aabf6db7dc8643cf
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346996"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Domande frequenti su Registro Azure Container

Questo articolo include le risposte alle domande frequenti e i problemi noti relativi a Registro Azure Container.

Per informazioni aggiuntive sulla risoluzione dei problemi del registro di sistema, vedere:
* [Risolvere i problemi di accesso al registro](container-registry-troubleshoot-login.md)
* [Risolvere i problemi di rete con il registro di sistema](container-registry-troubleshoot-access.md)
* [Risolvere i problemi relativi alle prestazioni del registro](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Resource management

- [È possibile creare un registro contenitori di Azure usando un modello di Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [È disponibile l'analisi delle vulnerabilità di sicurezza per le immagini in Registro Azure Container?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Come si configura Kubernetes con Registro Azure Container?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Come si ottengono le credenziali di amministratore per un registro contenitori?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Come si ottengono le credenziali di amministratore in un modello di Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [L'eliminazione della replica ha esito negativo con stato Non consentito, anche se la replica viene eliminata usando l'interfaccia della riga di comando di Azure o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Le regole del firewall vengono aggiornate, ma non vengono applicate](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>È possibile creare un registro contenitori di Azure usando un modello di Resource Manager?

Sì. È possibile usare questo [modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) per creare un registro.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>È disponibile l'analisi delle vulnerabilità di sicurezza per le immagini in Registro Azure Container?

Sì. Vedere la documentazione di [Centro sicurezza di Azure](../security-center/defender-for-container-registries-introduction.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Come si configura Kubernetes con Registro Azure Container?

Vedere la documentazione per [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e i passaggi per il [servizio Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Come si ottengono le credenziali di amministratore per un registro contenitori?

> [!IMPORTANT]
> L'account utente amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere la [Panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del registro sia abilitato.

Per ottenere le credenziali tramite l'interfaccia della riga di comando di Azure:

```azurecli
az acr credential show -n myRegistry
```

Usando Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Come si ottengono le credenziali di amministratore in un modello di Resource Manager?

> [!IMPORTANT]
> L'account utente amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità. Vedere la [Panoramica dell'autenticazione](container-registry-authentication.md).

Prima di ottenere le credenziali di amministratore, assicurarsi che l'utente amministratore del registro sia abilitato.

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>L'eliminazione della replica ha esito negativo con stato Non consentito, anche se la replica viene eliminata usando l'interfaccia della riga di comando di Azure o Azure PowerShell

Questo errore si verifica quando l'utente ha le autorizzazioni per un registro, ma non le autorizzazioni a livello di lettura per la sottoscrizione. Per risolvere il problema, assegnare all'utente le autorizzazioni di lettura per la sottoscrizione:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Le regole del firewall vengono aggiornate, ma non vengono applicate

La propagazione delle modifiche alle regole del firewall richiede tempo; dopo aver modificato le impostazioni del firewall, attendere alcuni minuti prima di verificare la modifica.


## <a name="registry-operations"></a>Operazioni sui registri

- [Come si accede all'API HTTP V2 del registro Docker?](#how-do-i-access-docker-registry-http-api-v2)
- [Come si eliminano tutti i manifesti a cui non viene fatto riferimento da alcun tag in un repository?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Perché l'utilizzo della quota del registro non si riduce dopo l'eliminazione di immagini?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Come si convalidano le modifiche della quota di archiviazione?](#how-do-i-validate-storage-quota-changes)
- [Come si esegue l'autenticazione nel registro quando si esegue l'interfaccia della riga di comando in un contenitore?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Come si abilita TLS 1.2?](#how-to-enable-tls-12)
- [Registro Azure Container supporta l'attendibilità contenuto?](#does-azure-container-registry-support-content-trust)
- [Come si concede l'accesso per eseguire il pull o il push delle immagini senza autorizzazione per la gestione della risorsa del registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Come si abilita la quarantena automatica delle immagini per un registro?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Come si abilita l'accesso pull anonimo?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Come si accede all'API HTTP V2 del registro Docker?

Registro Azure Container supporta l'API HTTP V2 del registro Docker. È possibile accedervi in `https://<your registry login server>/v2/`. Esempio: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Come si eliminano tutti i manifesti a cui non viene fatto riferimento da alcun tag in un repository?

Se si è in Bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Per PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: È possibile aggiungere `-y` nel comando di eliminazione per ignorare la conferma.

Per altre informazioni, vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Perché l'utilizzo della quota del registro non si riduce dopo l'eliminazione di immagini?

Questa situazione può verificarsi se le altre immagini del contenitore fanno ancora riferimento ai livelli sottostanti. Se si elimina un'immagine senza riferimenti, l'utilizzo del registro viene aggiornato in pochi minuti.

### <a name="how-do-i-validate-storage-quota-changes"></a>Come si convalidano le modifiche della quota di archiviazione?

Creare un'immagine con un livello da 1 GB usando il file Docker seguente. In questo modo si garantisce che l'immagine abbia un livello non condiviso da nessun'altra immagine nel registro.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Compilare l'immagine ed eseguirne il push nel registro usando l'interfaccia della riga di comando di Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Si dovrebbe poter verificare che l'utilizzo dell'archiviazione è aumentato nel portale di Azure oppure è possibile eseguire una query sull'utilizzo usando l'interfaccia della riga di comando.

```azurecli
az acr show-usage -n myregistry
```

Eliminare l'immagine usando l'interfaccia della riga di comando di Azure o il portale e controllare che l'utilizzo si aggiorni in pochi minuti.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Come si esegue l'autenticazione nel registro quando si esegue l'interfaccia della riga di comando in un contenitore?

È necessario eseguire il contenitore dell'interfaccia della riga di comando di Azure montando il socket di Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installare `docker` nel contenitore:

```bash
apk --update add docker
```

In seguito eseguire l'autenticazione nel registro:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Come si abilita TLS 1.2?

Abilitare TLS 1.2 usando qualsiasi client Docker recente (versione 18.03.0 o successiva). 

> [!IMPORTANT]
> A partire dal 13 gennaio 2020, Registro Azure Container richiederà l'uso di TLS 1.2 in tutte le connessioni sicure da server e applicazioni. Il supporto per TLS 1.0 e 1.1 verrà ritirato.

### <a name="does-azure-container-registry-support-content-trust"></a>Registro Azure Container supporta l'attendibilità contenuto?

Sì, è possibile usare immagini attendibili in Registro Azure Container perché [Docker Notary](https://docs.docker.com/notary/getting_started/) è stato integrato e può essere abilitato. Per informazioni dettagliate, vedere [Attendibilità dei contenuti in Registro Azure Container](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Dove si trova il file per l'identificazione personale?

Si trova in `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Le chiavi pubbliche e i certificati di tutti i ruoli, eccetto i ruoli di delega, vengono archiviati in `root.json`.
* Le chiavi pubbliche e i certificati del ruolo di delega vengono archiviati nel file JSON del relativo ruolo padre, ad esempio `targets.json` per il ruolo `targets/releases`.

È consigliabile verificare le chiavi pubbliche e i certificati dopo la verifica complessiva di TUF eseguita da Docker e dal client Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Come si concede l'accesso per eseguire il pull o il push delle immagini senza autorizzazione per la gestione della risorsa del registro?

Registro Azure Container supporta [ruoli personalizzati](container-registry-roles.md) che offrono diversi livelli di autorizzazioni. In particolare, i ruoli `AcrPull` e `AcrPush` consentono agli utenti di eseguire il pull e/o il push di immagini senza l'autorizzazione per gestire la risorsa del registro in Azure.

* Portale di Azure: Registro -> Controllo di accesso (IAM) -> Aggiungi (selezionare `AcrPull` o `AcrPush` per il ruolo).
* Interfaccia della riga di comando di Azure: Individuare l'ID risorse del registro eseguendo il comando seguente:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  È quindi possibile assegnare il ruolo `AcrPull` o `AcrPush` a un utente. Nell'esempio seguente viene usato `AcrPull`:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  In alternativa, assegnare il ruolo a un'entità servizio identificata dal relativo ID applicazione:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

L'assegnatario può quindi autenticarsi e accedere alle immagini nel registro.

* Per eseguire l'autenticazione in un registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Per elencare repository:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Per eseguire il pull di un'immagine:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Usando solo il ruolo `AcrPull` o `AcrPush`, l'assegnatario non ha l'autorizzazione per gestire la risorsa del registro in Azure. Ad esempio, `az acr list` o `az acr show -n myRegistry` non visualizzerà il registro.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Come si abilita la quarantena automatica delle immagini per un registro?

La quarantena delle immagini è attualmente una funzionalità di anteprima di Registro Azure Container. È possibile abilitare la modalità di quarantena di un registro in modo che solo le immagini che hanno superato correttamente l'analisi di sicurezza siano visibili agli utenti normali. Per informazioni dettagliate, vedere il [repository GitHub di Registro Azure Container](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Come si abilita l'accesso pull anonimo?

La configurazione di un registro contenitori di Azure per l'accesso pull anonimo (pubblico) è attualmente una funzionalità di anteprima. Se nel registro di sistema sono presenti [risorse della mappa dell'ambito (utente) o del token](./container-registry-repository-scoped-permissions.md) , eliminarle prima di generare un ticket di supporto (le mappe dell'ambito del sistema possono essere ignorate). Per abilitare l'accesso pubblico, aprire un ticket di supporto all'indirizzo https://aka.ms/acr/support/create-ticket. Per informazioni dettagliate, vedere il [forum di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries).

> [!NOTE]
> * È possibile accedere in modo anonimo solo alle API richieste per eseguire il pull di un'immagine nota. Nessun'altra API per operazioni come l'elenco di tag o l'elenco di repository è accessibile in modo anonimo.
> * Prima di provare un'operazione pull anonima, eseguire `docker logout` per assicurarsi di cancellare eventuali credenziali Docker esistenti.

## <a name="diagnostics-and-health-checks"></a>Controlli di diagnostica e integrità

- [Controllo integrità con `az acr check-health`](#check-health-with-az-acr-check-health)
- [L'esecuzione di docker pull ha esito negativo con errore: net/http: richiesta annullata durante l'attesa della connessione (Client.timeout ha superato le intestazioni in attesa)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [L'esecuzione di docker push ha esito positivo, ma docker pull ha esito negativo con errore: non autorizzato: autenticazione obbligatoria](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` ha esito positivo, ma i comandi di Docker hanno esito negativo con errore: non autorizzato: autenticazione obbligatoria](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Abilitare e ottenere i log di debug del daemon di Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Le nuove autorizzazioni utente possono non essere valide immediatamente dopo l'aggiornamento](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Le informazioni di autenticazione non sono specificate nel formato corretto nelle chiamate API REST dirette](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Perché il portale di Azure non elenca tutti i repository o i tag dell'utente?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Perché il portale di Azure non riesce a recuperare i repository o i tag?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Perché la richiesta pull o push ha esito negativo con errore di operazione non consentita?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Il formato del repository non è valido o non è supportato](#repository-format-is-invalid-or-unsupported)
- [Come si raccolgono le tracce http in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Controllo integrità con `az acr check-health`

Per risolvere i problemi comuni relativi all'ambiente e al registro, vedere [Verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>L'esecuzione di docker pull ha esito negativo con errore: net/http: richiesta annullata durante l'attesa della connessione (Client.timeout ha superato le intestazioni in attesa)

 - Se questo errore è un problema temporaneo, un secondo tentativo avrà esito positivo.
 - Se `docker pull` ha esito negativo in modo continuo, potrebbe essersi verificato un problema con il daemon di Docker. Il problema può in genere essere risolto riavviando il daemon di Docker. 
 - Se si continua a visualizzare questo problema dopo il riavvio del daemon di Docker, il problema potrebbe essere dovuto alla connettività di rete con il computer. Per verificare l'integrità della rete generale nel computer, eseguire il comando seguente per testare la connettività degli endpoint. La versione minima di `az acr` che contiene questo comando di controllo della connettività è la 2.2.9. Aggiornare l'interfaccia della riga di comando di Azure se si sta usando una versione precedente.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - È necessario avere sempre un meccanismo di ripetizione dei tentativi per tutte le operazioni del client Docker.

### <a name="docker-pull-is-slow"></a>Il pull di Docker è lento
Usare [questo strumento](http://www.azurespeed.com/Azure/Download) per testare la velocità di download della rete del computer. Se la rete del computer è lenta, provare a usare una VM di Azure nella stessa area del registro. Questo consente in genere di ottimizzare la velocità di rete.

### <a name="docker-push-is-slow"></a>Il push di Docker è lento
Usare [questo strumento](http://www.azurespeed.com/Azure/Upload) per testare la velocità di caricamento della rete del computer. Se la rete del computer è lenta, provare a usare una VM di Azure nella stessa area del registro. Questo consente in genere di ottimizzare la velocità di rete.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>L'esecuzione di docker push ha esito positivo, ma docker pull ha esito negativo con errore: non autorizzato: autenticazione obbligatoria

Questo errore può verificarsi con la versione Red Hat del daemon di Docker, in cui `--signature-verification` è abilitato per impostazione predefinita. È possibile controllare le opzioni del daemon di Docker per Red Hat Enterprise Linux (RHEL) o Fedora eseguendo il comando seguente:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Ad esempio, Fedora 28 Server ha le opzioni del daemon di Docker seguenti:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Se manca `--signature-verification=false`, `docker pull` ha esito negativo e restituisce un errore simile al seguente:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Per risolvere l'errore:
1. Aggiungere l'opzione `--signature-verification=false` al file di configurazione del daemon di Docker `/etc/sysconfig/docker`. Ad esempio:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Riavviare il sevizio del daemon di Docker eseguendo il comando seguente:
   
   ```bash
   sudo systemctl restart docker.service
   ```

I dettagli di `--signature-verification` sono reperibili eseguendo `man dockerd`.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login ha esito positivo, ma i comandi di Docker hanno esito negativo con errore: non autorizzato: autenticazione obbligatoria

Assicurarsi di usare un URL del server composto interamente da minuscole, ad esempio `docker push myregistry.azurecr.io/myimage:latest`, anche se il nome della risorsa del registro è maiuscolo o misto, come ad esempio `myRegistry`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Abilitare e ottenere i log di debug del daemon di Docker    

Avviare `dockerd` con l'opzione `debug`. Per prima cosa, creare il file di configurazione del daemon di Docker (`/etc/docker/daemon.json`) se non esiste e aggiungere l'opzione `debug`:

```json
{    
    "debug": true    
}
```

In seguito riavviare il daemon. Ad esempio, con Ubuntu 14.04:

```bash
sudo service docker restart
```

Altri dettagli sono disponibili nella [documentazione di Docker](https://docs.docker.com/engine/admin/#enable-debugging).    

 * I log possono essere generati in posizioni diverse, a seconda del sistema. Ad esempio, per Ubuntu 14.04 possono trovarsi in `/var/log/upstart/docker.log`.    
Per informazioni dettagliate, vedere la [documentazione di Docker](https://docs.docker.com/engine/admin/#read-the-logs).    

 * Per Docker per Windows, i log vengono generati in%LOCALAPPDATA%/docker/. Tuttavia, è possibile che non siano presenti ancora tutte le informazioni di debug.    

   Per accedere al log del daemon completo, potrebbero essere necessari alcuni passaggi aggiuntivi:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    A questo punto è possibile accedere a tutti i file della VM che esegue `dockerd`. Il log si trova in `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Le nuove autorizzazioni utente possono non essere valide immediatamente dopo l'aggiornamento

Quando si concedono nuove autorizzazioni, ovvero nuovi ruoli, a un'entità servizio, la modifica potrebbe non avere effetto immediato. Le ragioni possono essere due:

* Ritardo dell'assegnazione di ruoli in Azure Active Directory. Normalmente è un'operazione veloce, ma potrebbero essere necessari alcuni minuti a causa del ritardo della propagazione.
* Ritardo delle autorizzazioni nel server del token di Registro Azure Container. Per completare questa operazione possono essere necessari fino a 10 minuti. Per attenuare questo problema, è possibile eseguire `docker logout` e quindi eseguire di nuovo l'autenticazione con lo stesso utente dopo un minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Attualmente Registro Azure Container non supporta l'eliminazione della replica della pagina iniziale da parte degli utenti. La soluzione alternativa consiste nell'includere la replica della pagina iniziale creata nel modello, ma ignorarne la creazione aggiungendo `"condition": false` come illustrato di seguito:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Le informazioni di autenticazione non sono specificate nel formato corretto nelle chiamate API REST dirette

È possibile che si verifichi un errore `InvalidAuthenticationInfo`, in particolare quando si usa lo strumento `curl` con l'opzione `-L`, `--location` per seguire i reindirizzamenti.
Ad esempio, se si recupera il BLOB usando `curl` con l'opzione `-L` e l'autenticazione di base:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

è possibile che venga generata la risposta seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La causa radice è che alcune implementazioni di `curl` seguono i reindirizzamenti con le intestazioni della richiesta originale.

Per risolvere il problema, è necessario seguire manualmente i reindirizzamenti senza le intestazioni. Stampare le intestazioni della risposta con l'opzione `-D -` di `curl` ed estrarre l'intestazione `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Perché il portale di Azure non elenca tutti i repository o i tag dell'utente? 

Se si usa il browser Microsoft Edge/IE, è possibile visualizzare al massimo 100 repository o tag. Se il registro contiene più di 100 repository o tag, è consigliabile usare il browser Firefox o Chrome per elencarli tutti.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Perché il portale di Azure non riesce a recuperare i repository o i tag?

Il browser potrebbe non essere in grado di inviare la richiesta di recupero dei repository o dei tag al server. I motivi possono essere diversi, ad esempio:

* Mancanza di connettività di rete
* Firewall
* Blocchi degli annunci
* Errori DNS

Contattare l'amministratore di rete o controllare la configurazione e la connettività di rete. Provare a eseguire `az acr check-health -n yourRegistry` usando l'interfaccia della riga di comando di Azure per verificare se l'ambiente in uso può connettersi al Registro Container. È anche possibile provare ad aprire una sessione in incognito o privata nel browser per evitare la cache o i cookie del browser obsoleti.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Perché la richiesta pull o push ha esito negativo con errore di operazione non consentita?

Di seguito sono riportati alcuni scenari in cui è possibile che le operazioni non siano consentite:
* I registri classici non sono più supportati. Eseguire l'aggiornamento a un [livello di servizio](./container-registry-skus.md) supportato usando [az acr update](/cli/azure/acr#az-acr-update) o il portale di Azure.
* L'immagine o il repository possono essere bloccati in modo che non possano essere eliminati o aggiornati. È possibile usare il comando [az acr show repository](./container-registry-image-lock.md) per visualizzare gli attributi correnti.
* Alcune operazioni non sono consentite se l'immagine è in quarantena. Altre informazioni sulla [quarantena](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Il registro potrebbe avere raggiunto il [limite di archiviazione](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Il formato del repository non è valido o non è supportato

Se viene visualizzato un errore, ad esempio "formato di repository non supportato", "formato non valido" o "i dati richiesti non esistono" quando si specifica un nome di repository nelle operazioni del repository, controllare l'ortografia e la combinazione di maiuscole e minuscole del nome. I nomi di repository validi possono includere solo caratteri alfanumerici minuscoli, punti, trattini, caratteri di sottolineatura e barre. 

Per le regole di denominazione complete del repository, vedere la [specifica Open Container Initiative Distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Come si raccolgono le tracce http in Windows?

#### <a name="prerequisites"></a>Prerequisiti

- Abilitare la decrittografia di HTTPS in Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Abilitare Docker per l'uso di un proxy tramite l'interfaccia utente di Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Assicurarsi di eseguire il ripristino al termine dell'operazione.  Docker non funziona con queste opzioni abilitate e Fiddler non in esecuzione.

#### <a name="windows-containers"></a>Contenitori Windows

Configurare il proxy di Docker su 127.0.0.1:8888

#### <a name="linux-containers"></a>Contenitori Linux

Trovare l'indirizzo IP del commutatore virtuale della VM Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configurare il proxy di Docker per restituire l'output del comando precedente e la porta 8888 (ad esempio, 10.0.75.1:8888)

## <a name="tasks"></a>Attività

- [Come eseguire l'annullamento di operazioni in batch?](#how-do-i-batch-cancel-runs)
- [Come si include la cartella .git nel comando az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Attività del Registro Azure Container supporta GitLab per i trigger di origine?](#does-tasks-support-gitlab-for-source-triggers)
- [Quale servizio di gestione del repository Git è supportato da Attività del Registro Azure Container?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Come eseguire l'annullamento di operazioni in batch?

I comandi seguenti annullano tutte le attività in esecuzione nel registro specificato.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Come si include la cartella .git nel comando az acr build?

Se si passa una cartella di origine locale al comando `az acr build`, per impostazione predefinita la cartella `.git` viene esclusa dal pacchetto caricato. È possibile creare un file `.dockerignore` con l'impostazione seguente. Indica al comando di ripristinare tutti i file in `.git` nel pacchetto caricato. 

`!.git/**`

Questa impostazione si applica anche al comando `az acr run`.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Attività del Registro Azure Container supporta GitLab per i trigger di origine?

Attualmente GitLab per i trigger di origine non è supportato.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Quale servizio di gestione del repository Git è supportato da Attività del Registro Azure Container?

| Servizio Git | Contesto di origine | Compilazione manuale | Compilazione automatica tramite trigger di commit |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Sì | Sì |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Sì | Sì |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Sì | No |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Sì | No |

## <a name="run-error-message-troubleshooting"></a>Eseguire la risoluzione dei problemi dei messaggi di errore

| Messaggio di errore | Guida per la risoluzione dei problemi |
|---|---|
|No access was configured for the VM, hence no subscriptions were found (Nessun accesso è stato configurato per la VM, pertanto non è stata trovata alcuna sottoscrizione)|Questo problema può verificarsi se si usa `az login --identity` in Attività del Registro Azure Container. Si tratta di un errore temporaneo che si verifica quando l'assegnazione di ruolo dell'identità gestita non è stata propagata. Il problema si risolve attendendo alcuni secondi prima di riprovare.|

## <a name="cicd-integration"></a>Integrazione continua/distribuzione continua (CI/CD)

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-registry-intro.md) su Registro Azure Container.