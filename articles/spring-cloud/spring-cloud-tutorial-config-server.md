---
title: Configurare il server di configurazione in Azure Spring Cloud | Microsoft Docs
description: Questa esercitazione illustra come configurare un server di configurazione Spring Cloud per Azure Spring Cloud nel portale di Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 6742e1a5924fdcd1fe00f49ac790209a907d1bac
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132785"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Esercitazione: Configurare un server di configurazione Spring Cloud per il servizio

Questa esercitazione descrive come connettere un server di configurazione Spring Cloud al servizio Azure Spring Cloud.

Spring Cloud Config fornisce il supporto lato client e lato server per la configurazione esternalizzata in un sistema distribuito. Con il server di configurazione è possibile gestire in modo centralizzato le proprietà esterne per le applicazioni in tutti gli ambienti. Per altre informazioni, vedere le [informazioni di riferimento sul server di configurazione Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
* Un servizio Azure Spring Cloud in esecuzione di cui è già stato effettuato il provisioning.  Per effettuare il provisioning e avviare un servizio Azure Spring Cloud, completare questa [esercitazione di avvio rapido](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Restrizione

Quando si usa il __server di configurazione__ con un back-end git, sono previste alcune restrizioni. Alcune proprietà verranno inserite automaticamente nell'ambiente dell'applicazione per accedere al __server di configurazione__ e all'__individuazione dei servizi__. Se si configurano anche tali proprietà dai file del **server di configurazione**, è possibile che si verifichino conflitti e comportamenti imprevisti. Le proprietà includono: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> È consigliabile __NON__ includere le proprietà sopra indicate nei file dell'applicazione del __server di configurazione__.

## <a name="create-your-config-server-files"></a>Creare i file del server di configurazione

Azure Spring cloud supporta Azure DevOps, GitHub, GitLab e Bitbucket per archiviare i file del server di configurazione. Quando il repository è pronto, creare i file di configurazione seguendo le istruzioni riportate di seguito e archiviarli in tale posizione.

Inoltre, alcune proprietà configurabili sono disponibili solo per alcuni tipi. Le sottosezioni seguenti elencano le proprietà per ogni tipo di repository.

### <a name="public-repository"></a>Repository pubblico

Quando si usa un repository pubblico, le proprietà configurabili saranno più limitate.

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository `Git` pubblico.

> [!NOTE]
> L'uso di un trattino ("-") per separare le parole è l'unica convenzione di denominazione attualmente supportata. Ad esempio, è possibile usare `default-label`, ma non `defaultLabel`.

| Proprietà        | Obbligatoria | Funzionalità                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label` | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`  | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repository privato con autenticazione SSH

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository `Git` privato con `Ssh`.

> [!NOTE]
> L'uso di un trattino ("-") per separare le parole è l'unica convenzione di denominazione attualmente supportata. Ad esempio, è possibile usare `default-label`, ma non `defaultLabel`.

| Proprietà                   | Obbligatoria | Funzionalità                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label`            | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`             | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `private-key`              | `no`     | Chiave privata `Ssh` per accedere al repository `Git`, __obbligatoria__ quando l'`uri` inizia con `git@` o `ssh://`. |
| `host-key`                 | `no`     | Chiave host del server del repository Git. Non deve includere il prefisso dell'algoritmo perché incluso in `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | L'algoritmo di chiave host deve essere `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` o `ecdsa-sha2-nistp521`. Obbligatorio solo se `host-key` esiste. |
| `strict-host-key-checking` | `no`     | Indica se l'avvio del server di configurazione non riuscirà quando si utilizza la proprietà `host-key` privata. Deve essere `true` (valore predefinito) o `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repository privato con autenticazione di base

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository Git privato con autenticazione di base.

> [!NOTE]
> L'uso di un trattino ("-") per separare le parole è l'unica convenzione di denominazione attualmente supportata. Ad esempio, usare `default-label` e non `defaultLabel`.

| Proprietà        | Obbligatoria | Funzionalità                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label` | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`  | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `username`      | `no`     | `username` usato per accedere al server del repository `Git`, __obbligatorio__ quando il server del repository `Git` supporta `Http Basic Authentication`. |
| `password`      | `no`     | Password usata per accedere al server del repository `Git`, __obbligatoria__ quando il server del repository `Git` supporta `Http Basic Authentication`. |

> [!NOTE]
> Molti server del repository `Git` supportano l'uso di token al posto delle password per `HTTP Basic Authentication`. Alcuni repository, ad esempio GitHub, consentono la persistenza illimitata dei token. Tuttavia, alcuni server del repository Git, tra cui Azure DevOps, forzano la scadenza dei token dopo qualche ora. I repository che provocano la scadenza dei token non dovrebbero usare l'autenticazione basata su token con Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repository git con criteri

Di seguito sono elencate tutte le proprietà configurabili usate per configurare i repository Git con criteri.

> [!NOTE]
> L'uso di un trattino ("-") per separare le parole è l'unica convenzione di denominazione attualmente supportata. Ad esempio, usare `default-label` e non `defaultLabel`.

| Proprietà                           | Obbligatoria         | Funzionalità                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Mappa costituita dalle impostazioni per un repository `Git` con un nome specificato. |
| `repos."uri"`                      | `yes` in `repos` | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `repos."name"`                     | `yes` in `repos` | Nome per identificare un repository `Git`, __obbligatorio__ solo se esiste `repos`. Ad esempio, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Matrice di stringhe utilizzata per trovare la corrispondenza con il nome di un'applicazione. Per ogni modello, usare il formato `{application}/{profile}` con caratteri jolly. |
| `repos."default-label"`            | `no`             | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `repos."search-paths`"             | `no`             | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `repos."username"`                 | `no`             | `username` usato per accedere al server del repository `Git`, __obbligatorio__ quando il server del repository `Git` supporta `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Password usata per accedere al server del repository `Git`, __obbligatoria__ quando il server del repository `Git` supporta `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Chiave privata `Ssh` per accedere al repository `Git`, __obbligatoria__ quando l'`uri` inizia con `git@` o `ssh://`. |
| `repos."host-key"`                 | `no`             | Chiave host del server del repository Git. Non deve includere il prefisso dell'algoritmo perché incluso in `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | L'algoritmo di chiave host deve essere `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` o `ecdsa-sha2-nistp521`. __Obbligatorio__ solo se `host-key` esiste. |
| `repos."strict-host-key-checking"` | `no`             | Indica se l'avvio del server di configurazione non riuscirà quando si utilizza la proprietà `host-key` privata. Deve essere `true` (valore predefinito) o `false`. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Collegamento del repository del server di configurazione ad Azure Spring Cloud

Ora che i file di configurazione sono stati salvati in un repository, è necessario connettere Azure Spring Cloud al repository.

1. Accedere al [Portale di Azure](https://portal.azure.com).

1. Passare alla pagina **Panoramica** di Azure Spring Cloud.

1. Selezionare il servizio da configurare.

1. Nella pagina del servizio selezionare la scheda **Server di configurazione** sotto l'intestazione **Impostazioni** nel menu a sinistra.

![screenshot della finestra](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Immettere le informazioni sul repository direttamente nel portale di Azure

#### <a name="default-repository"></a>Repository predefinito

* Repository pubblico: Nella sezione **Repository predefinito** incollare l'URI del repository nella sezione **URI**.  Impostare **Etichetta** su `config`. Assicurarsi che l'impostazione di **Autenticazione** sia **Pubblica**, quindi selezionare **Applica** per terminare. 

* Repository privato: Azure Spring Cloud supporta l'autenticazione basata su password/token di base e SSH.

    * Autenticazione di base: Nella sezione **Repository predefinito** incollare l'URI del repository nella sezione **URI**, quindi fare clic su **Autenticazione**. Selezionare **Di base** come **Tipo di autenticazione** e quindi immettere nome utente e password/token per concedere l'accesso ad Azure Spring Cloud. Fare clic su **OK** e su **Applica** per completare la configurazione del server di configurazione.

    ![screenshot della finestra](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alcuni server di repository Git come GitHub usano un `personal-token` o un `access-token` come una password per l'**Autenticazione di base**. È possibile usare questa tipologia di token come password in Azure Spring Cloud, in quanto non scadrà mai. Tuttavia, per altri server di repository Git, ad esempio BitBucket e Azure DevOps, `access-token` scade entro una o due ore. Ciò significa che questa opzione non è praticabile quando si usano server di repository con Azure Spring Cloud.

    * SSH: Nella sezione **Repository predefinito** incollare l'URI del repository nella sezione **URI**, quindi fare clic su **Autenticazione**. Selezionare **SSH** come **Tipo di autenticazione** e immettere la **Chiave privata**. Facoltativamente, è possibile specificare la **Chiave host** e l'**Algoritmo di chiave host**. Assicurarsi di includere la chiave pubblica nel repository del server di configurazione. Fare clic su **OK** e su **Applica** per completare la configurazione del server di configurazione.

    ![screenshot della finestra](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repository di modelli

Per usare un **Repository di modelli** facoltativo per configurare il servizio, specificare l'**URI** e l'**Authentication** seguendo la stessa procedura del **Repository predefinito**. Assicurarsi di assegnare un **Nome** al modello e quindi fare clic su **Applica** per collegarlo all'istanza. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Immettere le informazioni sul repository in un file YAML

Se è stato scritto un file YAML con le impostazioni del repository, è possibile importare il file YAML direttamente dal computer locale in Azure Spring Cloud. Un semplice file YAML per un repository privato con autenticazione di base avrà un aspetto simile al seguente:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Fare clic sul pulsante **Importa impostazioni**, quindi selezionare il file `.yml` nella directory del progetto. Fare clic su **Importa**, quindi verrà visualizzata un'operazione `async` dall'area **Notifiche**. Un messaggio di completamento dell'operazione verrà visualizzato entro 1 o 2 minuti.

![screenshot della finestra](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Le informazioni del file YAML verranno visualizzate nel portale di Azure. Fare clic su **Applica** per completare l'operazione. 


## <a name="delete-your-app-configuration"></a>Eliminare la configurazione dell'app

Dopo aver salvato un file di configurazione, nella scheda **Configurazione** verrà visualizzato il pulsante **Delete app configuration** (Elimina configurazione app). Questa opzione cancellerà completamente le impostazioni esistenti. È necessario eseguire questa operazione se si desidera connettere il server di configurazione a un'altra origine, ad esempio se si vuole passare da GitHub ad Azure DevOps.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare e configurare il server di configurazione. Per altre informazioni su come gestire l'applicazione, passare all'esercitazione sul ridimensionamento manuale dell'app.

> [!div class="nextstepaction"]
> [Come ridimensionare manualmente un'applicazione Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

