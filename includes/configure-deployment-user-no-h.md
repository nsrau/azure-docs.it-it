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
ms.openlocfilehash: e8888a0505a3a38d2844f82c0f7fff255d05353d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261453"
---
In Azure Cloud Shell configurare le credenziali di distribuzione con il comando [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Questo utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Nome utente e password sono a livello di account. _Sono quindi diversi dalle credenziali della sottoscrizione di Azure._

Nell'esempio seguente sostituire *\<username>* e *\<password>* (incluse le parentesi) con un nuovo nome utente e una nuova password. Il nome utente deve essere univoco in Azure. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Viene visualizzato un output JSON, con la password visualizzata come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

L'utente della distribuzione deve essere configurato una sola volta. È possibile usarlo per tutte le distribuzioni di Azure.

> [!NOTE]
> Prendere nota del nome utente e della password. Saranno necessari per distribuire l'app Web in un secondo momento.
>
>
