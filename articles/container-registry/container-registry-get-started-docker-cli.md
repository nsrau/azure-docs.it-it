---
title: Operazioni Docker su un registro di contenitori | Documentazione Microsoft
description: Effettuare il push e il pull di immagini Docker in un registro di contenitori di Azure tramite l&quot;interfaccia della riga di comando di Docker
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: 923e1a045062a817dd6726dfce94485be7211ca2

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Effettuare il push della prima immagine in un registro di contenitori tramite l'interfaccia della riga di comando di Docker
Un registro di contenitori di Azure archivia e gestisce le immagini dei contenitori [Docker](http://hub.docker.com) private, in modo analogo a come [Docker Hub](https://hub.docker.com/) archivia le immagini Docker pubbliche. Usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/) per eseguire l'[accesso](https://docs.docker.com/engine/reference/commandline/login/), il [push](https://docs.docker.com/engine/reference/commandline/push/), il [pull](https://docs.docker.com/engine/reference/commandline/pull/) e altre operazioni sul registro di contenitori. 

Per altri concetti e informazioni di base, vedere [What is Azure Container Registry?](container-registry-intro.md) (Che cos'è Azure Container Registry?).


> [!NOTE]
> Il servizio Container Registry è attualmente disponibile in anteprima.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* **Registro di contenitori di Azure**: creare un registro di contenitori nella sottoscrizione di Azure. Ad esempio, usare il [portale di Azure](container-registry-get-started-portal.md) o l'[interfaccia della riga di comando di Azure 2.0 (anteprima)](container-registry-get-started-azure-cli.md).
* **Interfaccia della riga di comando di Docker**: per configurare il computer locale come host Docker e accedere ai comandi della riga di comando di Docker, installare [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Accedere a un registro
Eseguire `docker login` per accedere al registro di contenitori con le [credenziali del registro](container-registry-authentication.md).

L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione. 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Assicurarsi di specificare il nome del registro completo (tutto in lettere minuscole). In questo esempio si tratta di `myregistry-contoso.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Passaggi per effettuare il pull e il push di un'immagine
L'esempio seguente scarica un'immagine di Nginx dal registro pubblico di Docker Hub, contrassegna l'immagine per il registro di contenitori di Azure privato, effettua il push dell'immagine nel registro e quindi ne effettua nuovamente il pull.

**1. Effettuare il pull dell'immagine ufficiale di Docker per Nginx**

Innanzitutto effettuare il pull dell'immagine pubblica di Nginx nel computer locale.

```
docker pull nginx
```
**2. Avviare il contenitore Nginx**

Il comando seguente avvia il contenitore Nginx locale in modo interattivo (si può così visualizzare l'output da Nginx), rimanendo in ascolto sulla porta 8080, e quindi rimuove il contenitore in esecuzione dopo l'arresto.

```
docker run -it --rm -p 8080:80 nginx
```

Passare a [http://localhost:8080/](http://localhost:8080) per visualizzare il contenitore in esecuzione. Verrà visualizzata una schermata simile alla seguente.

![Nginx sul computer locale](./media/container-registry-get-started-docker-cli/nginx.png)

Per arrestare il contenitore in esecuzione, premere [CTRL]+[C].

**3. Creare un alias dell'immagine nel registro**

Il comando seguente crea un alias dell'immagine, con un percorso completo del registro. Questo esempio specifica lo spazio dei nomi `samples` per evitare confusione nella radice del registro.

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4. Effettuare il push dell'immagine nel registro**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5. Effettuare il pull dell'immagine dal registro**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6. Avviare il contenitore Nginx dal registro**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

Passare a [http://localhost:8080/](http://localhost:8080) per visualizzare il contenitore in esecuzione.

Per arrestare il contenitore in esecuzione, premere [CTRL]+[C].

**6. Rimuovere l'immagine**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>Passaggi successivi
Una volta apprese le nozioni di base, si è pronti per iniziare a usare il proprio registro. È ad esempio possibile iniziare a distribuire immagini di contenitore in un cluster del [servizio contenitore di Azure](https://azure.microsoft.com/documentation/services/container-service/).






<!--HONumber=Nov16_HO3-->


