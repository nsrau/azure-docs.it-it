---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179842"
---
### <a name="access-tokens"></a>Token di accesso

I token di accesso sono un metodo più affidabile per l'autenticazione con Azure spaziali ancoraggi. In particolare durante la preparazione dell'applicazione per una distribuzione di produzione. Il riepilogo di questo approccio consiste nel configurare un servizio back-end che l'applicazione client può autenticare in modo sicuro con. Le interfacce del servizio back-end con AAD in fase di esecuzione e con Azure spaziali Anchor Secure Token Service per richiedere un Token di accesso. Questo token viene quindi recapitato all'applicazione client e utilizzato nel SDK per l'autenticazione con Azure spaziali ancoraggi.
