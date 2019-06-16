---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110692"
---
Ad esempio con i token di accesso, se non viene impostato un token di Azure AD, è necessario gestire l'evento TokenRequired o implementare il metodo tokenRequired sul protocollo delegato.

È possibile gestire in modo sincrono l'evento impostando la proprietà negli argomenti dell'evento.
