---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302328"
---
È necessario innanzitutto completare e inviare il [modulo di richiesta dei contenitori di Visione artificiale in Servizi cognitivi](https://aka.ms/VisionContainersPreview) per richiedere l'accesso al contenitore. Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio, il team di Servizi cognitivi di Azure esamina il modulo per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato un account Microsoft o Azure Active Directory (Azure AD) nel modulo.

Se la richiesta viene approvata, si riceverà un messaggio di posta elettronica con istruzioni su come ottenere le credenziali e accedere al registro contenitori privato.

## <a name="log-in-to-the-private-container-registry"></a>Accedere al registro contenitori privato

Esistono diversi modi per eseguire l'autenticazione con il registro contenitori privato per i contenitori di Servizi cognitivi, ma il metodo consigliato dalla riga di comando consiste nell'usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Usare il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io`, il registro contenitori privato per i contenitori di Servizi cognitivi. Sostituire *\<username\>* con il nome utente e *\<password\>* con la password specificata nelle credenziali ricevute dal team di Servizi cognitivi di Azure.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono protette in un file di testo, è possibile concatenare il contenuto del file mediante il comando `cat` al comando `docker login`, come mostrato nell'esempio seguente. Sostituire *\<passwordFile\>* con il percorso e il nome del file di testo contenente la password e *\<username\>* con il nome utente specificato nelle credenziali.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

