---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092997"
---
## <a name="configure-the-cloud-anchor-session"></a>Configurare la sessione di ancoraggio cloud

Verrà ora configurata la sessione di ancoraggio cloud. Nella prima riga viene impostato il provider di sensori nella sessione. Da questo momento in poi, qualsiasi ancoraggio creato durante la sessione verrà associato a un set di letture del sensore. Viene quindi creata un'istanza dei criteri di localizzazione di dispositivi nelle vicinanze, che verrà inizializzata in base ai requisiti dell'applicazione. Infine, viene indicato alla sessione di usare i dati del sensore quando si individuano gli ancoraggi creando un watcher dai criteri di localizzazione dei dispositivi nelle vicinanze.