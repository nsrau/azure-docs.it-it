---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4f061dc66829c49fba6722215dc36ed2ce6464ee
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919271"
---
In Azure Cloud Shell configurare le credenziali di distribuzione con il comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Questo utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Nome utente e password sono a livello di account. _Sono quindi diversi dalle credenziali della sottoscrizione di Azure._

Nell'esempio seguente sostituire *\<username>* e *\<password>* (incluse le parentesi) con un nuovo nome utente e una nuova password. Il nome utente deve essere univoco in Azure. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Viene visualizzato un output JSON, con la password visualizzata come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa. Il nome utente della distribuzione non può contenere il simbolo '@' per i push Git locali.

L'utente della distribuzione deve essere configurato una sola volta. È possibile usarlo per tutte le distribuzioni di Azure.

> [!NOTE]
> Prendere nota del nome utente e della password. Saranno necessari per distribuire l'app Web in un secondo momento.
>
>
