---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179842"
---
### <a name="access-tokens"></a>Token di accesso

I token di accesso sono un metodo più affidabile per l'autenticazione con gli ancoraggi nello spazio di Azure, soprattutto quando si prepara l'applicazione per una distribuzione di produzione. Il riepilogo di questo approccio consiste nel configurare un servizio back-end con cui l'applicazione client può eseguire l'autenticazione in modo sicuro. Il servizio back-end si interfaccia con AAD in fase di esecuzione e con il servizio token sicuro degli ancoraggi nello spazio di Azure per richiedere un token di accesso. Questo token viene quindi distribuito all'applicazione client e usato nell'SDK per eseguire l'autenticazione con gli ancoraggi nello spazio di Azure.
