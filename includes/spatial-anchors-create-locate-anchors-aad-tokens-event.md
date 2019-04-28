---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232452"
---
Ad esempio con i token di accesso, se non viene impostato un token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired sul protocollo delegato.

È possibile gestire in modo sincrono l'evento impostando la proprietà negli argomenti dell'evento.
