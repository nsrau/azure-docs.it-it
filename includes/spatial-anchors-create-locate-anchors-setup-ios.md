---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006495"
---
## <a name="setting-up-the-library"></a>Configurazione della libreria

Richiamare `Start()` per consentire alla sessione di elaborare i dati dell'ambiente.

Per gestire gli eventi generati dalla sessione, impostare la proprietà `delegate` della sessione su un oggetto, ad esempio la visualizzazione. L'oggetto deve implementare il protocollo `SSCCloudSpatialAnchorSessionDelegate`.
