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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681040"
---
Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Sostituire *\<deploymentLocalGitUrl-from-create-step>* con l'URL del Git remoto salvato in [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere quelle create in [Configurare un utente della distribuzione](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), non quelle usate per accedere al portale di Azure.

```bash
git push azure master
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:
