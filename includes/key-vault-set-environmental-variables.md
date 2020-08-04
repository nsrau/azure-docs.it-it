---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013113"
---
Il metodo DefaultAzureCredential usato nell'applicazione si basa sulle tre variabili di ambiente `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`. Impostare queste variabili sui valori clientId, clientSecret e tenantId restituiti nel passaggio "Creare un'entità servizio" usando il formato `export VARNAME=VALUE`. Questo metodo imposta solo le variabili per la shell corrente e per i processi creati dalla shell. Per aggiungere in modo permanente queste variabili all'ambiente, modificare il file `/etc/environment `. 

Sarà anche necessario salvare il nome dell'insieme di credenziali delle chiavi come una variabile di ambiente denominata `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
