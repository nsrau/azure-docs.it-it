---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632354"
---
Ad esempio con i token di accesso, se non viene impostato un token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired sul protocollo delegato.

È possibile gestire in modo sincrono l'evento impostando la proprietà negli argomenti dell'evento.
