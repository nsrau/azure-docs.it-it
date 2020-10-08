---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822794"
---
Per aprire una sessione SSH diretta con il contenitore, l'app deve essere in esecuzione.

Incollare l'URL seguente nel browser e sostituire `<app-name>` con il nome dell'app:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se non lo si è già fatto, per connettersi è necessario eseguire l'autenticazione con la sottoscrizione di Azure. Al termine dell'autenticazione viene visualizzata una shell nel browser, in cui è possibile eseguire i comandi all'interno del contenitore.

![Connessione SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
