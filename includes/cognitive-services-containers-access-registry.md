---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704275"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Usare l'interfaccia della riga di comando Docker per l'autenticazione del registro contenitori privato

È possibile eseguire l'autenticazione con il registro contenitori privato per i contenitori di Servizi cognitivi in diversi modi, ma il metodo da riga di comando consigliato consiste nell'usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Usare il [ `docker login` comando](https://docs.docker.com/engine/reference/commandline/login/), come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io` , il registro contenitori privato per i contenitori di servizi cognitivi. Sostituire * \< username \> * con il nome utente e la * \< password \> * con la password fornita nelle credenziali ricevute dal team di servizi cognitivi di Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono protette in un file di testo, è possibile concatenare il contenuto del file al comando `docker login` usando il comando `cat`, come mostrato nell'esempio seguente. Sostituire * \< passwordFile \> * con il percorso e il nome del file di testo che contiene la * \< \> password e il nome utente con* il nome utente specificato nelle credenziali.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
