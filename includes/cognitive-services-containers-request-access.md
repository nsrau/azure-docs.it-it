---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229232"
---
Compilare e inviare il [modulo di richiesta di richieste di contenitori](https://aka.ms/VisionContainersPreview) di visione di Servizi cognitivi per richiedere l'accesso al contenitore. Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di Servizi cognitivi di Azure lo esamina per assicurarsi di soddisfare i criteri per l'accesso al Registro di sistema del contenitore privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato a un account Microsoft (MSA) o a un account Azure Active Directory (Azure AD) nel modulo.

Se la richiesta viene approvata, si riceve un messaggio di posta elettronica con istruzioni che descrivono come ottenere le credenziali e accedere al Registro di sistema del contenitore privato.

## <a name="log-in-to-the-private-container-registry"></a>Accedere al registro contenitori privato

Esistono diversi modi per eseguire l'autenticazione con il Registro di sistema del contenitore privato per i contenitori di Servizi cognitivi. Si consiglia di utilizzare il metodo della riga di comando utilizzando [l'interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilizzare il comando [docker login,](https://docs.docker.com/engine/reference/commandline/login/) come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io`, che è il Registro di sistema del contenitore privato per i contenitori di Servizi cognitivi. Sostituire * \<\> username* con il nome utente e * \<\> * la password con la password fornita nelle credenziali ricevute dal team di Servizi cognitivi di Azure.Replace username with the user name and password with the password provided in the credentials you received from the Azure Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono stati protetti in un file di testo, è `docker login` possibile concatenare il contenuto di tale file di testo al comando. Utilizzare `cat` il comando , come illustrato nell'esempio seguente. Sostituire * \<passwordFile\> * con il percorso e il nome del file di testo che contiene la password. Sostituire * \<\> username* con il nome utente fornito nelle credenziali.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

