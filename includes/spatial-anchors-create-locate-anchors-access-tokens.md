---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993085"
---
### <a name="access-tokens"></a>Token di accesso

I token di accesso sono un metodo più affidabile per l'autenticazione con gli ancoraggi nello spazio di Azure, soprattutto quando si prepara l'applicazione per una distribuzione di produzione. Il riepilogo di questo approccio consiste nel configurare un servizio back-end con cui l'applicazione client può eseguire l'autenticazione in modo sicuro. Il servizio back-end si interfaccia con AAD in fase di esecuzione e con il servizio token sicuro degli ancoraggi nello spazio di Azure per richiedere un token di accesso. Questo token viene quindi distribuito all'applicazione client e usato nell'SDK per eseguire l'autenticazione con gli ancoraggi nello spazio di Azure.
