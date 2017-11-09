---
title: Effettuare il push di un'immagine Docker in un registro di Azure privato | Microsoft Docs
description: Effettuare il push e il pull di immagini Docker in un registro di contenitori privati in Azure tramite l'interfaccia della riga di comando di Docker
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10f01e4e8c86bbbfa17cf2559caca645ff13bdcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Effettuare il push della prima immagine in un registro per contenitori Docker privati tramite l'interfaccia della riga di comando di Docker
Un registro di contenitori di Azure archivia e gestisce le immagini dei contenitori [Docker](http://hub.docker.com) private, in modo analogo a come [Docker Hub](https://hub.docker.com/) archivia le immagini Docker pubbliche. Usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/) per eseguire l'[accesso](https://docs.docker.com/engine/reference/commandline/login/), il [push](https://docs.docker.com/engine/reference/commandline/push/), il [pull](https://docs.docker.com/engine/reference/commandline/pull/) e altre operazioni sul registro di contenitori.

Per altre informazioni di base e concetti, vedere la [panoramica](container-registry-intro.md)



## <a name="prerequisites"></a>Prerequisiti
* **Registro di contenitori di Azure**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio, usare il [Portale di Azure](container-registry-get-started-portal.md) o l'[interfaccia della riga di comando di Azure 2.0](container-registry-get-started-azure-cli.md).
* **Interfaccia della riga di comando di Docker**: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Accedere a un registro
Eseguire `docker login` per accedere al registro di contenitori con le [credenziali del registro](container-registry-authentication.md).

L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Assicurarsi di specificare il nome del registro completo (tutto in lettere minuscole). In questo esempio si tratta di `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Passaggi per effettuare il pull e il push di un'immagine
L'esempio seguente scarica l'immagine di Nginx dal registro pubblico di Docker Hub, contrassegna l'immagine per il registro di contenitori di Azure privato, effettua il push dell'immagine nel registro e quindi ne effettua nuovamente il pull.

**1. Effettuare il pull dell'immagine ufficiale di Docker per Nginx**

Innanzitutto effettuare il pull dell'immagine pubblica di Nginx nel computer locale.

```
docker pull nginx
```
**2. Avviare il contenitore Nginx**

Il comando seguente avvia il contenitore Nginx locale in modo interattivo sulla porta 8080, consentendo di visualizzare l'output da Nginx, e quindi rimuove il contenitore in esecuzione dopo l'arresto.

```
docker run -it --rm -p 8080:80 nginx
```

Passare a [http://localhost:8080/](http://localhost:8080) per visualizzare il contenitore in esecuzione. Verrà visualizzata una schermata simile alla seguente.

![Nginx sul computer locale](./media/container-registry-get-started-docker-cli/nginx.png)

Per arrestare il contenitore in esecuzione, premere [CTRL]+[C].

**3. Creare un alias dell'immagine nel registro**

Il comando seguente crea un alias dell'immagine, con un percorso completo del registro. Questo esempio specifica lo spazio dei nomi `samples` per evitare confusione nella radice del registro.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. Effettuare il push dell'immagine nel registro**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. Effettuare il pull dell'immagine dal registro**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. Avviare il contenitore Nginx dal registro**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Passare a [http://localhost:8080/](http://localhost:8080) per visualizzare il contenitore in esecuzione.

Per arrestare il contenitore in esecuzione, premere [CTRL]+[C].

**7. (Facoltativo) Rimuovere l'immagine**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Limiti delle chiamate simultanee
In alcuni scenari, l'esecuzione di chiamate simultanee può provocare errori. La tabella seguente contiene i limiti delle chiamate simultanee con operazioni "Push" e "Pull" nel registro contenitori di Azure:

| Operazione  | Limite                                  |
| ---------- | -------------------------------------- |
| PULL       | Fino a 10 pull simultanei per registro |
| PUSH       | Fino a 5 push simultanei per registro |

## <a name="next-steps"></a>Passaggi successivi
Una volta apprese le nozioni di base, si è pronti per iniziare a usare il proprio registro. È ad esempio possibile iniziare a distribuire immagini di contenitore in un cluster del [servizio contenitore di Azure](https://azure.microsoft.com/documentation/services/container-service/).
