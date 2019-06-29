---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461512"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurare una variabile di ambiente per l'autenticazione

Le applicazioni devono accedere a servizi cognitivi usano l'autenticazione. Per eseguire l'autenticazione, è consigliabile creare una variabile di ambiente per archiviare una chiave in risorse di Azure. 

Dopo aver ottenuto la chiave, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `your-key` con la chiave di accesso di rilevatore di anomalie:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Dopo l'aggiunta della variabile di ambiente potrebbe essere necessario riavviare eventuali programmi in esecuzione che necessitano di leggere la variabile di ambiente, inclusa la finestra della console. Ad esempio, se si usa Visual Studio come editor, riavviare Visual Studio prima di eseguire l'esempio.

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
