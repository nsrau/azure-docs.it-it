---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841505"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurare una variabile di ambiente per l'autenticazione

Le applicazioni devono accedere a servizi cognitivi usano l'autenticazione. Per eseguire l'autenticazione, è consigliabile creare una variabile di ambiente per archiviare le chiavi per le risorse di Azure. 

Dopo aver ottenuto la chiave, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `your-key` con una delle chiavi per la risorsa.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Dopo l'aggiunta della variabile di ambiente potrebbe essere necessario riavviare eventuali programmi in esecuzione che necessitano di leggere la variabile di ambiente, inclusa la finestra della console. Se ad esempio si usa Visual Studio come editor, riavviare Visual Studio prima di eseguire l'esempio.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.
    
* macOS
    
    Modificare il file con estensione bash_profile e aggiungere la variabile di ambiente:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.
