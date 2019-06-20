---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063962"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Usare l'interfaccia della riga di comando Docker per l'autenticazione del registro contenitori privato

È possibile eseguire l'autenticazione con il registro contenitori privato per i contenitori di Servizi cognitivi in diversi modi, ma il metodo da riga di comando consigliato consiste nell'usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Usare il [comando `docker login`](https://docs.docker.com/engine/reference/commandline/login/), come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io`, il registro contenitori privato per i contenitori di Servizi cognitivi. Sostituire *\<username\>* con il nome utente e *\<password\>* con la password specificata nelle credenziali ricevute dal team di Servizi cognitivi di Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono protette in un file di testo, è possibile concatenare il contenuto del file al comando `docker login` usando il comando `cat`, come mostrato nell'esempio seguente. Sostituire *\<passwordFile\>* con il percorso e il nome del file di testo contenente la password e *\<username\>* con il nome utente specificato nelle credenziali.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
