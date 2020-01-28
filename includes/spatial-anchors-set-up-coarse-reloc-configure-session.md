---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545211"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurare la sessione di ancoraggi nello spazio nel cloud

Verrà ora configurata la sessione di ancoraggi nello spazio nel cloud. Nella prima riga viene impostato il provider di sensori nella sessione. Da questo momento in poi, qualsiasi ancoraggio creato durante la sessione verrà associato a un set di letture del sensore. Viene quindi creata un'istanza dei criteri di localizzazione di dispositivi nelle vicinanze, che verrà inizializzata in base ai requisiti dell'applicazione. Infine, viene indicato alla sessione di usare i dati del sensore quando si individuano gli ancoraggi creando un watcher dai criteri di localizzazione dei dispositivi nelle vicinanze.