---
title: Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP | Microsoft Docs
description: Questo articolo aiuta a comprendere i protocolli di intestazione HTTP supportati da Frontdoor
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038851"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP
Questo documento descrive il protocollo supportato dal servizio Frontdoor di Azure con le varie parti del percorso di chiamate come illustrato nell'immagine seguente. Le sezioni seguenti forniscono informazioni dettagliate sulle intestazioni HTTP supportate da Frontdoor.

![Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP][1]

>[!WARNING]
>Il servizio Frontdoor di Azure non offre garanzie su eventuali intestazioni HTTP non documentate qui.

## <a name="1-client-to-front-door"></a>1. Da client a Frontdoor
Frontdoor accetta la maggior parte delle intestazioni della richiesta in entrata (senza modificarle); tuttavia, esistono alcune intestazioni riservate che verranno rimosse dalla richiesta in ingresso se vengono inviate. Sono incluse le intestazioni con i prefissi seguenti:
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2. Da Frontdoor a back-end

Frontdoor includerà le intestazioni della richiesta in ingresso, a meno che non siano state rimosse a causa di restrizioni menzionate sopra. Frontdoor aggiungerà anche le intestazioni seguenti:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Si tratta di una stringa di riferimento univoco che identifica una richiesta servita da Frontdoor. È fondamentale per la risoluzione dei problemi relativi a come viene usato per eseguire ricerche nei log di accesso.|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> Si tratta di un'intestazione utilizzata da Frontdoor per rilevare i cicli di richiesta e gli utenti non dovranno dipendere da esso. |
| X-MS-Via |  *X-MS-Via: Azure* </br> Viene aggiunto dal Frontdoor per indicare che Azure/Frontdoor è un destinatario intermedio per la richiesta tra il client e il back-end. |

## <a name="3-front-door-to-client"></a>3. Da Frontdoor a client

Di seguito le intestazioni che vengono inviate da Frontdoor ai client. Qualsiasi intestazione inviata dal back-end al Frontdoor passa anche per il client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Frontdoor. È fondamentale per la risoluzione dei problemi relativi a come viene usato per eseguire ricerche nei log di accesso.|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come creare un Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png