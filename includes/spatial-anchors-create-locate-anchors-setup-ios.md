---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006495"
---
## <a name="setting-up-the-library"></a>Configurazione della libreria

Richiamare `Start()` per consentire alla sessione di elaborare i dati dell'ambiente.

Per gestire gli eventi generati dalla sessione, impostare la proprietà `delegate` della sessione su un oggetto, ad esempio la visualizzazione. L'oggetto deve implementare il protocollo `SSCCloudSpatialAnchorSessionDelegate`.
