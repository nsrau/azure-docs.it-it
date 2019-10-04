---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179849"
---
Ad esempio con i token di accesso, se non viene impostato un token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired sul protocollo delegato.

È possibile gestire in modo sincrono l'evento impostando la proprietà negli argomenti dell'evento.
