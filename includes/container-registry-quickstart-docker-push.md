---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149025"
---
## <a name="push-image-to-registry"></a>Eseguire il push dell'immagine nel registro

Per eseguire il push di un'immagine in Registro Azure Container è necessario innanzitutto disporre di un'immagine. Se non sono ancora disponibili immagini del contenitore locale, eseguire il comando seguente [docker pull][docker-pull] per eseguire il pull di un'immagine esistente da Hub Docker. Per questo esempio, eseguire il pull dell'immagine `hello-world`.

```
docker pull hello-world
```

Prima di poter eseguire il push di un'immagine nel registro, è necessario contrassegnarla con il nome completo del server di accesso del record di controllo di accesso. Il nome del server di accesso è nel formato *\<registry-name\>.azurecr.io* (tutto in minuscolo), ad esempio *mycontainerregistry007.azurecr.io*.

Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record di controllo di accesso.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Infine, usare [docker push][docker-push] per eseguire il push dell'immagine nell'istanza del record di controllo di accesso. Sostituire `<acrLoginServer>` con il nome del server di accesso dell'istanza del record di controllo di accesso. Questo esempio crea il repository **hello-world** che contiene l'immagine `hello-world:v1`.

```
docker push <acrLoginServer>/hello-world:v1
```

Dopo il push dell'immagine nel registro contenitori, rimuovere l'immagine `hello-world:v1` dall'ambiente Docker locale. Si noti che questo comando [docker rmi][docker-rmi] non rimuove l'immagine dal repository **hello-world** nel registro contenitori di Azure.

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

