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
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344687"
---
In Cloud Shell configurare le credenziali di distribuzione con il comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Questo utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Nome utente e password sono a livello di account. _Sono quindi diversi dalle credenziali della sottoscrizione di Azure._

Nell'esempio seguente sostituire *\<username>* e *\<password>* (incluse le parentesi) con un nuovo nome utente e una nuova password. Il nome utente deve essere univoco in Azure. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dovrebbe essere visualizzato un output JSON, con la password visualizzata come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

L'utente di distribuzione deve essere configurato una sola volta e può essere usato per tutte le distribuzioni di Azure.

> [!NOTE]
> Registrare il nome utente e la password. Saranno necessari per distribuire l'app Web in un secondo momento.
>
>
