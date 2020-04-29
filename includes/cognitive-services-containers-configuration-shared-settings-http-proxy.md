---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320523"
---
Se è necessario configurare un proxy HTTP per eseguire le richieste in uscita, usare questi due argomenti:

| Name | Tipo di dati | Descrizione |
|--|--|--|
|HTTP_PROXY|stringa|Il proxy da usare, ad esempio, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|stringa|Qualsiasi credenziale richiesta per autenticare il proxy, ad esempio, nome utente:password.|
|`<proxy-user>`|stringa|L'utente per il proxy.|
|`<proxy-password>`|stringa|La password associata a `<proxy-user>` per il proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
