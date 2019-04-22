---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886833"
---
Se è necessario configurare un proxy HTTP per eseguire le richieste in uscita, usare questi due argomenti:

| NOME | Tipo di dati | DESCRIZIONE |
|--|--|--|
|HTTP_PROXY|stringa|il proxy da usare, ad esempio, `http://proxy:8888`|
|HTTP_PROXY_CREDS|stringa|qualsiasi credenziale richiesta per autenticare il proxy, ad esempio, nome utente:password.|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```