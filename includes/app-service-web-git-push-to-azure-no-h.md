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
ms.openlocfilehash: 026540290710d039dbc06c394ab538ebe2d7c12f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344670"
---
Nella _finestra del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _&lt;deploymentLocalGitUrl-from-create-step>_ con l'URL del Git remoto salvato in [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager chiede le credenziali, assicurarsi di immettere quelle create in [Configurare un utente della distribuzione](#configure-a-deployment-user) al posto di quelle usate per accedere al portale di Azure.

```bash
git push azure master
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:
