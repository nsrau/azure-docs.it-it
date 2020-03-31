---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179848"
---
## <a name="setting-up-the-library"></a>Configurazione della libreria

Richiamare Start() per consentire alla sessione di elaborare i dati dell'ambiente.

Per gestire gli eventi generati dalla sessione, impostare la proprietà `delegate` della sessione su un oggetto, ad esempio la visualizzazione. Questo oggetto deve implementare il protocollo SSCCloudSpatialAnchorSessionDelegate.
