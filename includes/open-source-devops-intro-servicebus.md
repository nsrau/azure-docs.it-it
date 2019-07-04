---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180837"
---
Il [bus di servizio di Azure](/azure/service-bus-messaging/service-bus-messaging-overview) è un broker messaggi di [integrazione](https://azure.microsoft.com/product-categories/integration/) aziendale. Il bus di servizio supporta due tipi di comunicazione, code e argomenti. 

Le code supportano le comunicazioni asincrone tra applicazioni. Un'applicazione invia messaggi a una coda, che archivia i messaggi. L'applicazione ricevente si connette a e legge i messaggi dalla coda.

Gli argomenti supportano il modello di pubblicazione-sottoscrizione, che permette una relazione uno-a-molti tra il mittente del messaggio e uno o più destinatari del messaggio.