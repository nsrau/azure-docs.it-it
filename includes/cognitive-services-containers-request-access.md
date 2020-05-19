---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593333"
---
Compilare e inviare il [modulo di richiesta di contenitori di servizi cognitivi](https://aka.ms/cognitivegate) per richiedere l'accesso al contenitore. Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di servizi cognitivi di Azure le esamina per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato a un account Microsoft (MSA) o a un account Azure Active Directory (Azure AD) nel modulo. Per ulteriori informazioni sui criteri di accettazione, vedere il processo di controllo [dei servizi cognitivi](../articles/cognitive-services/cognitive-services-gating-process.md).

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

