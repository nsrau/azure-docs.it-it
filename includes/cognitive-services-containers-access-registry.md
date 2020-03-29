---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704275"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Usare l'interfaccia della riga di comando Docker per l'autenticazione del registro contenitori privato

È possibile eseguire l'autenticazione con il registro contenitori privato per i contenitori di Servizi cognitivi in diversi modi, ma il metodo da riga di comando consigliato consiste nell'usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilizzare [ `docker login` ](https://docs.docker.com/engine/reference/commandline/login/)il comando , come illustrato nell'esempio `containerpreview.azurecr.io`seguente, per accedere a , il Registro di sistema del contenitore privato per i contenitori di servizi cognitivi. Sostituire * \<\> username* con il nome utente e * \<\> * la password con la password fornita nelle credenziali ricevute dal team di Servizi cognitivi di Azure.Replace username with the user name and password with the password that's fornito in the credentials you received from the Azure Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono protette in un file di testo, è possibile concatenare il contenuto del file al comando `docker login` usando il comando `cat`, come mostrato nell'esempio seguente. Sostituire * \<\> passwordFile* con il percorso e il nome del file di testo che contiene la password e * \<\> * il nome utente con il nome utente fornito nelle credenziali.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
