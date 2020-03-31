---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179849"
---
Analogamente ai token di accesso, se non è impostato alcun token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired per il protocollo delegato.

È possibile gestire l'evento in modo sincrono impostando la proprietà sugli argomenti dell'evento.
