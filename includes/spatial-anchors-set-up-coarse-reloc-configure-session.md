---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999748"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurare la sessione di ancoraggi nello spazio nel cloud

Verrà ora configurata la sessione di ancoraggi nello spazio nel cloud. Nella prima riga viene impostato il provider di sensori nella sessione. Da questo momento in poi, qualsiasi ancoraggio creato durante la sessione verrà associato a un set di letture del sensore. Viene quindi creata un'istanza dei criteri di localizzazione di dispositivi nelle vicinanze, che verrà inizializzata in base ai requisiti dell'applicazione. Infine, viene indicato alla sessione di usare i dati del sensore quando si individuano gli ancoraggi creando un watcher dai criteri di localizzazione dei dispositivi nelle vicinanze.