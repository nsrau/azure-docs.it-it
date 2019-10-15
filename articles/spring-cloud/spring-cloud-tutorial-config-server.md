---
title: Configurare il server di configurazione in Azure Spring Cloud | Microsoft Docs
description: Questa esercitazione illustra come configurare un server di configurazione Spring Cloud per Azure Spring Cloud nel portale di Azure
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038653"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Esercitazione: Configurare un server di configurazione Spring Cloud per il servizio

Questa esercitazione descrive come connettere un server di configurazione Spring Cloud al servizio Azure Spring Cloud.

Spring Cloud Config fornisce il supporto lato client e lato server per la configurazione esternalizzata in un sistema distribuito. Con il server di configurazione è possibile gestire in modo centralizzato le proprietà esterne per le applicazioni in tutti gli ambienti. Per altre informazioni, vedere le [informazioni di riferimento sul server di configurazione Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
* Un servizio Azure Spring Cloud in esecuzione di cui è già stato effettuato il provisioning.  Per effettuare il provisioning e avviare un servizio Azure Spring Cloud, completare questa [esercitazione di avvio rapido](spring-cloud-quickstart-launch-app-cli.md).


## <a name="restriction"></a>Restrizione

Quando si usa il __server di configurazione__ con un back-end git, sono previste alcune restrizioni. Alcune proprietà verranno inserite automaticamente nell'ambiente dell'applicazione per accedere al __server di configurazione__ e all'__individuazione dei servizi__. Se si configurano anche tali proprietà dai file del **server di configurazione**, è possibile che si verifichino conflitti e comportamenti imprevisti.  Le proprietà includono: 

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

Azure Spring cloud supporta Azure DevOps, GitHub, GitLab e Bitbucket per archiviare i file del server di configurazione.  Quando il repository è pronto, creare i file di configurazione seguendo le istruzioni riportate di seguito e archiviarli in tale posizione.

Inoltre, alcune proprietà configurabili sono disponibili solo per alcuni tipi. Le sottosezioni seguenti elencano le proprietà per ogni tipo di repository.


### <a name="public-repository"></a>Repository pubblico

Quando si usa un repository pubblico, le proprietà configurabili saranno più limitate.

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository `Git` pubblico.

> [!NOTE]
> Per il momento è supportata solo la convenzione di denominazione con parole separate dal trattino ("-"). Ad esempio, è possibile usare `default-label` ma non `defaultLabel`.

| Proprietà        | Obbligatoria | Funzionalità                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label` | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`  | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repository privato con autenticazione SSH

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository `Git` privato con `Ssh`.

> [!NOTE]
> Per il momento è supportata solo la convenzione di denominazione con parole separate dal trattino ("-"). Ad esempio, è possibile usare `default-label` ma non `defaultLabel`.

| Proprietà                   | Obbligatoria | Funzionalità                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label`            | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`             | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `private-key`              | `no`     | Chiave privata `Ssh` per accedere al repository `Git`, __obbligatoria__ quando l'`uri` inizia con `git@` o `ssh://`. |
| `host-key`                 | `no`     | Chiave host del server del repository Git. Non deve includere il prefisso dell'algoritmo perché incluso in `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Algoritmo della chiave host. Deve essere uno dei seguenti: `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` e `ecdsa-sha2-nistp521`. Obbligatorio solo se `host-key` esiste. |
| `strict-host-key-checking` | `no`     | Indica se l'avvio del server di configurazione non riuscirà quando si utilizza la proprietà `host-key` specificata. Deve essere `true` (valore predefinito) o `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repository privato con autenticazione di base

Di seguito sono elencate tutte le proprietà configurabili usate per configurare il repository Git privato con autenticazione di base.

> [!NOTE]
> Per il momento è supportata solo la convenzione di denominazione con parole separate dal trattino ("-"). Ad esempio, è possibile usare `default-label`, ma non `defaultLabel`.

| Proprietà        | Obbligatoria | Funzionalità                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `default-label` | `no`     | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `search-paths`  | `no`     | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `username`      | `no`     | `username` usato per accedere al server del repository `Git`, __obbligatorio__ se il server del repository `Git` supporta `Http Basic Authentication`. |
| `password`      | `no`     | Password usata per accedere al server del repository `Git`, __obbligatoria__ se il server del repository `Git` supporta `Http Basic Authentication`. |

> [!NOTE]
> Alcuni server del repository `Git`, come GitHub, supportano le proprietà "personal-token" o "access-token" come password per `HTTP Basic Authentication`. È possibile usare questa tipologia di token come password anche in questo caso e i valori di "personal-token" o "access-token" non scadranno. Tuttavia, per i server di repository Git come BitBucket e Azure DevOps, il token scade entro una o due ore rendendo questa opzione non idonea per l'uso con Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repository git con criteri

Di seguito sono elencate tutte le proprietà configurabili usate per configurare i repository Git con criteri.

> [!NOTE]
> Per il momento è supportata solo la convenzione di denominazione con parole separate dal trattino ("-"). Ad esempio, è possibile usare `default-label`, ma non `defaultLabel`.

| Proprietà                           | Obbligatoria         | Funzionalità                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Una mappa è costituita dalle impostazioni dei repository `Git` con il nome specificato. |
| `repos."uri"`                      | `yes` in `repos` | L'`uri` del repository `Git` usato come back-end del server di configurazione deve essere avviato con `http://`, `https://`, `git@` o `ssh://`. |
| `repos."name"`                     | `yes` in `repos` | Nome per identificare un repository `Git`, __obbligatorio__ solo se `repos` esiste. Ad esempio, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Matrice di stringhe usata per trovare la corrispondenza con il nome dell'applicazione. Ogni modello accetta il formato `{application}/{profile}` con caratteri jolly. |
| `repos."default-label"`            | `no`             | L'etichetta predefinita del repository `Git` deve essere costituita da `branch name`, `tag name` o `commit-id` del repository. |
| `repos."search-paths`"             | `no`             | Matrice di stringhe usata per la ricerca nelle sottodirectory del repository `Git`. |
| `repos."username"`                 | `no`             | `username` usato per accedere al server del repository `Git`, __obbligatorio__ se il server del repository `Git` supporta `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Password usata per accedere al server del repository `Git`, __obbligatoria__ se il server del repository `Git` supporta `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Chiave privata `Ssh` per accedere al repository `Git`, __obbligatoria__ quando l'`uri` inizia con `git@` o `ssh://`. |
| `repos."host-key"`                 | `no`             | Chiave host del server del repository Git. Non deve includere il prefisso dell'algoritmo perché incluso in `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Algoritmo della chiave host. Deve essere uno dei seguenti: `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` e `ecdsa-sha2-nistp521`. __Obbligatorio__ solo se `host-key` esiste. |
| `repos."strict-host-key-checking"` | `no`             | Indica se l'avvio del server di configurazione non riuscirà quando si utilizza la proprietà `host-key` specificata. Deve essere `true` (valore predefinito) o `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importare il file `application.yml` da Spring Cloud Config

È possibile importare alcune impostazioni predefinite del server di configurazione direttamente dal sito Web [Spring Cloud Config](https://spring.io/projects/spring-cloud-config). È possibile eseguire questa operazione direttamente dal portale di Azure, quindi non è necessario eseguire alcuna operazione per preparare i file del server di configurazione o il repository.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Collegamento del repository del server di configurazione ad Azure Spring Cloud

Ora che i file di configurazione sono stati salvati in un repository, è necessario connettere Azure Spring Cloud al repository.

1. Accedere al [Portale di Azure](https://portal.azure.com).

1. Passare alla pagina **Panoramica** di Azure Spring Cloud.

1. Passare alla scheda **Server di configurazione** sotto l'intestazione **Impostazioni** nel menu a sinistra.

### <a name="public-repository"></a>Repository pubblico

Se il repository è pubblico, è sufficiente fare clic sul pulsante **Pubblico** e incollare l'URL.

### <a name="private-repository"></a>Repository privato

Azure Spring Cloud supporta l'autenticazione SSH. Seguire le istruzioni visualizzate nel portale di Azure per aggiungere la chiave pubblica al repository. Assicurarsi quindi di includere la chiave privata nel file di configurazione.

Fare clic su **Applica** per completare la configurazione del server di configurazione.


## <a name="delete-your-app-configuration"></a>Eliminare la configurazione dell'app

Dopo aver salvato un file di configurazione, nella scheda **Configurazione** verrà visualizzato il pulsante **Delete app configuration** (Elimina configurazione app). Questa opzione cancellerà completamente le impostazioni esistenti. È necessario eseguire questa operazione se si desidera connettere il server di configurazione a un'altra origine, ad esempio se si vuole passare da GitHub ad Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare e configurare il server di configurazione. Per altre informazioni su come gestire l'applicazione, passare all'esercitazione sul ridimensionamento manuale dell'app.

> [!div class="nextstepaction"]
> [Come ridimensionare manualmente un'applicazione Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

