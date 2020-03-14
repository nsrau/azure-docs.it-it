---
title: Opzioni di autenticazione del registro
description: Opzioni di autenticazione per un registro contenitori di Azure privato, incluso l'accesso con un'identità di Azure Active Directory, l'uso di entità servizio e l'uso di credenziali di amministratore facoltative.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247046"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Eseguire l'autenticazione con un Registro Azure Container

Esistono diversi modi per eseguire l'autenticazione con un Registro Azure Container, ognuno dei quali è applicabile a uno o più scenari di utilizzo del registro.

Le modalità consigliate includono l'autenticazione a un registro direttamente tramite un [singolo account di accesso](#individual-login-with-azure-ad), oppure le applicazioni e gli agenti di orchestrazione dei contenitori possono eseguire l'autenticazione automatica o senza test tramite un' [entità servizio](#service-principal)Azure Active Directory (Azure ad).

## <a name="authentication-options"></a>Opzioni di autenticazione

Nella tabella seguente sono elencati i metodi di autenticazione disponibili e gli scenari consigliati. Per informazioni dettagliate, vedere contenuti collegati.

| Metodo                               | Come eseguire l'autenticazione                                           | Scenari                                                            | Controllo degli accessi in base al ruolo                             | Limitazioni                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Singola identità di Active directory](#individual-login-with-azure-ad)                | `az acr login` nell'interfaccia della riga di comando di Azure                             | Push/pull interattivo da parte di sviluppatori e tester                                    | Sì                              | Il token AD deve essere rinnovato ogni 3 ore     |
|   [entità servizio Active Directory](#service-principal)                 | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure<br/><br/> Impostazioni di accesso del registro di sistema nelle API o negli strumenti<br/><br/>      [segreto di pull Kubernetes](container-registry-auth-kubernetes.md)                                       | Push automatico dalla pipeline CI/CD<br/><br/> Pull automatico in Azure o servizi esterni  | Sì                              | La scadenza predefinita della password SP è 1 anno       |                                                           
| [Integrazione con AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Connetti il registro di sistema durante la creazione o l'aggiornamento del cluster AKS  | Pull automatico nel cluster AKS                                                  | No, solo accesso pull             | Disponibile solo con il cluster AKS            |
| [Identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` nell'interfaccia della riga di comando di Azure                                       | Push automatico dalla pipeline CI/CD di Azure<br/><br/> Pull automatico nei servizi di Azure<br/><br/>   | Sì                              | Usare solo da servizi di Azure che [supportano identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
|   [utente amministratore](#admin-account)                           | `docker login`                                          | Push/pull interattivo per singolo sviluppatore o tester                           | No, sempre pull e Push Access  | Account singolo per registro di sistema, non consigliato per più utenti         |
| [Token di accesso con ambito Repository](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure   | Push/pull interattivo nel repository per singolo sviluppatore o tester<br/><br/> Push/Pull automatico nel repository da un singolo sistema o dispositivo esterno                  | Sì                              | Attualmente non integrato con identità di Active Directory  |

## <a name="individual-login-with-azure-ad"></a>Accesso individuale con Azure AD

Quando si usa direttamente il registro, ad esempio per eseguire il pull o il push delle immagini da una workstation di sviluppo, eseguire l'autenticazione usando il comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) nell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando si accede con `az acr login`, l'interfaccia della riga di comando usa il token creato quando si è eseguito [az login](/cli/azure/reference-index#az-login) per l'autenticazione semplificata della sessione al registro. Per completare il flusso di autenticazione, è necessario che Docker sia installato e in esecuzione nell'ambiente in uso. `az acr login` usa il client Docker per impostare un token di Azure Active Directory nel file `docker.config`. Dopo aver effettuato l'accesso in questo modo, le credenziali vengono memorizzate nella cache e i successivi comandi `docker` nella sessione non richiedono il nome utente o la password.

> [!TIP]
> Usare anche `az acr login` per autenticare una singola identità quando si desidera effettuare il push o il pull di elementi diversi dalle immagini Docker nel registro di sistema, ad esempio gli [artefatti OCI](container-registry-oci-artifacts.md).  


Per l'accesso al registro di sistema, il token usato da `az acr login` è valido per **3 ore**, quindi è consigliabile accedere sempre al registro di sistema prima di eseguire un comando di `docker`. In caso di scadenza del token, è possibile aggiornarlo usando di nuovo il comando `az acr login` per eseguire nuovamente l'autenticazione. 

L'uso di `az acr login` con le identità di Azure offre l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md). Per alcuni scenari, potrebbe essere necessario accedere a un registro con la propria identità personale in Azure AD. Per gli scenari tra servizi o per gestire le esigenze di un gruppo di lavoro o di un flusso di lavoro di sviluppo in cui non si vuole gestire l'accesso singolo, è anche possibile accedere con un' [identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entità servizio

Se si assegna un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) al registro, l'applicazione o il servizio può usarla per eseguire l'autenticazione headless. Le entità servizio consentono di eseguire l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md) a un registro. È possibile assegnare più entità servizio a un registro. L'uso di più entità servizio consente di definire un accesso diverso per applicazioni diverse.

I ruoli disponibili per un registro contenitori includono:

* **AcrPull**: pull

* **AcrPush**: pull e push

* **Proprietario**: pull, push e assegnazione di ruoli ad altri utenti

Per un elenco completo dei ruoli, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

Per gli script dell'interfaccia della riga di comando per creare un'entità servizio per l'autenticazione con un registro contenitori di Azure e altre linee guida, vedere [autenticazione container Registry di Azure con entità servizio](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Account amministratore

Ogni registro contenitori include un account utente amministratore che, per impostazione predefinita, è disabilitato. È possibile abilitare l'utente amministratore e gestirne le credenziali nel portale di Azure oppure tramite l'interfaccia della riga di comando o altri strumenti di Azure.

> [!IMPORTANT]
> L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore tra più utenti. Tutti gli utenti che si autenticano con l'account amministratore vengono visualizzati come un unico utente con accesso di tipo push e pull al registro. Se si modifica o si disattiva questo account, tutti gli utenti che ne usano le credenziali non potranno più accedere al registro. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità.
>

L'account amministratore è dotato di due password, entrambe rigenerabili. L'uso di due password consente di mantenere la connessione al registro usando una password mentre si rigenera l'altra. Se l'account amministratore è abilitato, è possibile passare il nome utente e una password al comando `docker login` quando richiesto, per eseguire l'autenticazione di base al registro. Ad esempio:

```
docker login myregistry.azurecr.io 
```

Per le procedure consigliate per gestire le credenziali di accesso, vedere le informazioni di riferimento sul comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Per consentire a un utente amministratore di accedere a un registro esistente, è possibile usare il parametro `--admin-enabled` del comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) nell'interfaccia della riga di comando di Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Per abilitare l'utente amministratore nel portale di Azure, passare al registro interessato, selezionare **Chiavi di accesso** in **IMPOSTAZIONI**, quindi selezionare **Abilita** in **Utente amministratore**.

![Abilitare l'interfaccia utente dell'utente amministratore nel portale di Azure][auth-portal-01]

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
