---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014780"
---
Dopo la creazione del Watcher, l'evento `AnchorLocated` viene attivato per ogni ancoraggio richiesto. Questo evento viene generato quando viene individuato un ancoraggio o se non è possibile individuarlo. Se si verifica questa situazione, il motivo verrà indicato nello stato. Dopo che tutti gli ancoraggi per un Watcher vengono elaborati, che siano stati trovati o no, viene generato l'evento `LocateAnchorsCompleted`. È previsto un limite di 35 identificatori per Watcher. 
