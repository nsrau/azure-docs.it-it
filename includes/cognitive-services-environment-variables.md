---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85073311"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurare una variabile di ambiente per l'autenticazione

Le applicazioni devono autenticare l'accesso ai servizi cognitivi che usano. Per eseguire l'autenticazione, è consigliabile creare una variabile di ambiente per archiviare le chiavi per le risorse di Azure. 

Dopo aver completato la chiave, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `your-key` con una delle chiavi per la risorsa.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Dopo l'aggiunta della variabile di ambiente potrebbe essere necessario riavviare eventuali programmi in esecuzione che necessitano di leggere la variabile di ambiente, inclusa la finestra della console. Se ad esempio si usa Visual Studio come editor, riavviare Visual Studio prima di eseguire l'esempio.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macos"></a>[macOS](#tab/unix)

Modificare il file con estensione bash_profile e aggiungere la variabile di ambiente:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bash_profile` dalla finestra della console per rendere effettive le modifiche.

***
