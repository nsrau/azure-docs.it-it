---
title: Eseguire l'autenticazione a un registro contenitori di Azure
description: Opzioni di autenticazione per un registro contenitori di Azure, inclusi l'accesso con un'identità di Azure Active Directory, l'uso di entità servizio e l'uso di credenziali di amministratore facoltative.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a68e4f70dac7aace9d49a41ecf282525ce6b1fd6
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752878"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Eseguire l'autenticazione a un registro contenitori Docker privato

Esistono diversi modi per eseguire l'autenticazione a un registro contenitori di Azure, ognuno dei quali è applicabile a uno o più scenari di utilizzo del registro.

È possibile accedere a un registro direttamente tramite l'[accesso individuale](#individual-login-with-azure-ad). In alternativa, le applicazioni e gli agenti di orchestrazione dei contenitori possono eseguire l'autenticazione automatica o "headless", usando un'[entità servizio](#service-principal) di Azure Active Directory (Azure AD).

Registro Azure Container non supporta le operazioni di Docker non autenticate o l'accesso anonimo. Per le immagini pubbliche, è possibile usare l'[Hub Docker](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Accesso individuale con Azure AD

Quando si usa direttamente il registro, ad esempio per eseguire il pull o il push delle immagini dalla workstation di sviluppo, eseguire l'autenticazione usando il comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) nell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando si accede con `az acr login`, l'interfaccia della riga di comando usa il token creato quando si è eseguito [az login](/cli/azure/reference-index#az-login) per l'autenticazione semplificata della sessione al registro. Dopo avere effettuato l'accesso in questo modo, le credenziali vengono memorizzate nella cache e i successivi comandi `docker` non richiedono il nome utente o la password. In caso di scadenza del token, è possibile aggiornarlo usando di nuovo il comando `az acr login` per eseguire nuovamente l'autenticazione. L'uso di `az acr login` con le identità di Azure offre l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Entità servizio

Se si assegna un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) al registro, l'applicazione o il servizio può usarla per eseguire l'autenticazione headless. Le entità servizio consentono di eseguire l'[accesso in base al ruolo](../role-based-access-control/role-assignments-portal.md) a un registro. È possibile assegnare più entità servizio a un registro. L'uso di più entità servizio consente di definire un accesso diverso per applicazioni diverse.

I ruoli disponibili per un registro contenitori includono:

* **AcrPull**: pull

* **AcrPush**: pull e push

* **Proprietario**: pull, push e assegnazione di ruoli ad altri utenti

Per un elenco completo dei ruoli, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

Per l'uso di script dell'interfaccia della riga di comando per creare un ID e una password di app dell'entità servizio per l'autenticazione a un registro contenitori di Azure o per usare un'entità servizio esistente, vedere [Autenticazione a Registro Azure Container con entità servizio](container-registry-auth-service-principal.md).

Le entità servizio consentono la connettività headless a un registro in entrambi gli scenari di pull e push, come i seguenti:

  * *Pull*: distribuire contenitori da un registro a sistemi di orchestrazione, inclusi Kubernetes, DC/OS e Docker Swarm. È anche possibile eseguire il pull da registri contenitori a servizi di Azure correlati, ad esempio il [servizio Azure Kubernetes](container-registry-auth-aks.md), [Istanze di Azure Container](container-registry-auth-aci.md), il [servizio app](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e altri ancora.

  * *Push*: creare immagini dei contenitori ed eseguirne il push in un registro usando soluzioni di integrazione e distribuzione continua, come Azure Pipelines o Jenkins.

È inoltre possibile accedere direttamente con un'entità servizio. Specificare l'ID app e la password dell'entità servizio nel comando `docker login`:

```
docker login myregistry.azurecr.io -u <SP_APP_ID> -p <SP_PASSWD>
```

Dopo aver effettuato l'accesso, Docker memorizza nella cache le credenziali, così da non doversi ricordare l'ID app.

A seconda della versione di Docker installata, è possibile che venga visualizzato un avviso di sicurezza che consiglia di usare il parametro `--password-stdin`. Sebbene il suo utilizzo non rientri nell'ambito di questo articolo, si raccomanda di seguire questa procedura consigliata. Per altre informazioni, vedere la guida comandi di [accesso di Docker](https://docs.docker.com/engine/reference/commandline/login/).

> [!TIP]
> È possibile rigenerare la password di un'entità servizio eseguendo il comando [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials).
>

## <a name="admin-account"></a>Account amministratore

Ogni registro contenitori include un account utente amministratore che, per impostazione predefinita, è disabilitato. È possibile abilitare l'utente amministratore e gestirne le credenziali nel [portale di Azure](container-registry-get-started-portal.md#create-a-container-registry) oppure tramite l'interfaccia della riga di comando o altri strumenti di Azure.

> [!IMPORTANT]
> L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore con più utenti. Tutti gli utenti che si autenticano con l'account amministratore vengono visualizzati come un unico utente con accesso di tipo push e pull al registro. Se si modifica o si disattiva questo account, tutti gli utenti che ne usano le credenziali non potranno più accedere al registro. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità.
>

L'account amministratore è dotato di due password, entrambe rigenerabili. L'uso di due password consente di mantenere la connessione al registro usando una password mentre si rigenera l'altra. Se l'account amministratore è abilitato, è possibile passare il nome utente e una password al comando `docker login` per eseguire l'autenticazione di base al registro. Ad esempio: 

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Anche in questo caso, in Docker è consigliabile usare il parametro `--password-stdin` invece di specificarlo nella riga di comando, per una maggiore sicurezza. È inoltre possibile specificare solo il nome utente, senza `-p`, e immettere la password quando viene richiesto.

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
