---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116719"
---
Se è necessario configurare un proxy HTTP per eseguire le richieste in uscita, usare questi due argomenti:

| NOME | Tipo di dati | DESCRIZIONE |
|--|--|--|
|HTTP_PROXY|stringa|Il proxy da usare, ad esempio, `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|stringa|Qualsiasi credenziale richiesta per autenticare il proxy, ad esempio, nome utente:password.|
|`<proxy-user>`|stringa|L'utente per il proxy.|
|`proxy-password`|stringa|La password associata a `<proxy-user>` per il proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```