---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751903"
---
### <a name="access-tokens"></a>Token di accesso

I token di accesso sono un metodo più affidabile per l'autenticazione con Azure spaziali ancoraggi. In particolare durante la preparazione dell'applicazione per una distribuzione di produzione. Il riepilogo di questo approccio consiste nel configurare un servizio back-end che l'applicazione client può autenticare in modo sicuro con. Le interfacce del servizio back-end con AAD in fase di esecuzione e con il servizio Azure spaziali Anchor STS per richiedere un Token di accesso. Questo token viene quindi recapitato all'applicazione client e utilizzato nel SDK per l'autenticazione con Azure spaziali ancoraggi.
