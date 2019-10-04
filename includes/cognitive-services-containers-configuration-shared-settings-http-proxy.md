---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320523"
---
Se è necessario configurare un proxy HTTP per eseguire le richieste in uscita, usare questi due argomenti:

| Name | Tipo di dati | DESCRIZIONE |
|--|--|--|
|HTTP_PROXY|string|Il proxy da usare, ad esempio, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Qualsiasi credenziale richiesta per autenticare il proxy, ad esempio, nome utente:password.|
|`<proxy-user>`|string|L'utente per il proxy.|
|`<proxy-password>`|string|La password associata a `<proxy-user>` per il proxy.|
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
