---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698544"
---
Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di servizi cognitivi di Azure le esamina per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato a un account Microsoft (MSA) o a un account Azure Active Directory (Azure AD) nel modulo.

Se la richiesta viene approvata, si riceverà un messaggio di posta elettronica con le istruzioni che descrivono come ottenere le credenziali e accedere al registro contenitori privato.

## <a name="log-in-to-the-private-container-registry"></a>Accedere al registro contenitori privato

Esistono diversi modi per eseguire l'autenticazione con il registro contenitori privato per i contenitori di servizi cognitivi. Si consiglia di usare il metodo della riga di comando usando l'interfaccia della riga di comando di [Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Usare il comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io` , ovvero al registro contenitori privato per i contenitori di servizi cognitivi. Sostituire * \< username \> * con il nome utente e la * \< password \> * con la password specificata nelle credenziali ricevute dal team di servizi cognitivi di Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono state protette in un file di testo, è possibile concatenare il contenuto del file di testo al `docker login` comando. Utilizzare il `cat` comando, come illustrato nell'esempio seguente. Sostituire * \< passwordFile \> * con il percorso e il nome del file di testo contenente la password. Sostituire * \< username \> * con il nome utente specificato nelle credenziali.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

