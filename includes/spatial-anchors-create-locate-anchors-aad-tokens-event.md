---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179849"
---
Analogamente ai token di accesso, se non è impostato alcun token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired per il protocollo delegato.

È possibile gestire l'evento in modo sincrono impostando la proprietà sugli argomenti dell'evento.
