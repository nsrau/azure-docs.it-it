---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179849"
---
Analogamente ai token di accesso, se non è impostato alcun token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired per il protocollo delegato.

È possibile gestire l'evento in modo sincrono impostando la proprietà sugli argomenti dell'evento.
