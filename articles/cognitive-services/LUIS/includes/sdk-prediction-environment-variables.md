---
title: File di inclusione
description: File di inclusione
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772408"
---
### <a name="create-an-environment-variable"></a>Creare una variabile di ambiente

Usando la chiave di runtime e l'endpoint di runtime, creare le variabili di ambiente per l'autenticazione e l'accesso:

* `LUIS_RUNTIME_KEY`: la chiave della risorsa di runtime per l'autenticazione delle richieste.
* `LUIS_RUNTIME_ENDPOINT`: l'endpoint di runtime associato alla chiave.
* `LUIS_APP_ID`: l'ID app IoT LUIS è `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` oppure `staging`

Se si intende usare questa guida di avvio rapido per accedere alla propria app, è necessario eseguire alcuni passaggi aggiuntivi:
* Creare l'app e ottenere l'ID app
* Assegnare la chiave di runtime all'app nel portale LUIS
* Testare l'URL con il browser per assicurarsi che sia possibile accedere all'app

Usare le istruzioni per il sistema operativo in uso.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Dopo aver aggiunto le variabili di ambiente, riavviare la finestra della console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Dopo avere aggiunto le variabili di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Dopo avere aggiunto le variabili di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.

***
