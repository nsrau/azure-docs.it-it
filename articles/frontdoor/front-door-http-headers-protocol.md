---
title: Supporto del protocollo per le intestazioni HTTP in Azure Front Door Documenti Microsoft
description: In questo articolo vengono descritti i protocolli di intestazione HTTP supportati da Front Door.
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471677"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Supporto del protocollo per le intestazioni HTTP in Azure Front DoorProtocol support for HTTP headers in Azure Front Door
In questo articolo viene descritto il protocollo supportato da Front Door con parti del percorso di chiamata (vedere l'immagine). Nelle sezioni seguenti vengono fornite ulteriori informazioni sulle intestazioni HTTP supportate da Front Door.

![Protocollo delle intestazioni HTTP della porta frontale di AzureAzure Front Door HTTP headers protocol][1]

>[!IMPORTANT]
>Front Door non certifica tutte le intestazioni HTTP che non sono documentate qui.

## <a name="client-to-front-door"></a>Da client a Frontdoor
Front Door accetta la maggior parte delle intestazioni dalla richiesta in arrivo senza modificarle. Alcune intestazioni riservate vengono rimosse dalla richiesta in ingresso se inviate, incluse le intestazioni con il prefisso X-FD-.

## <a name="front-door-to-backend"></a>Da Frontdoor a back-end

Front Door include intestazioni da una richiesta in arrivo, a meno che non vengano rimosse a causa di restrizioni. Front Door aggiunge anche le seguenti intestazioni:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door aggiunge la versione HTTP del client seguita da *Azure* come valore per l'intestazione Via. Questa intestazione indica la versione HTTP del client e che Front Door era un destinatario intermedio per la richiesta tra il client e il back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1X-Azure-ClientIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP del client associato alla richiesta in fase di elaborazione. Ad esempio, una richiesta proveniente da un proxy potrebbe aggiungere l'intestazione X-Forwarded-For per indicare l'indirizzo IP del chiamante originale. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1X-Azure-SocketIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP socket associato alla connessione TCP da cui ha avuto origine la richiesta corrente. L'indirizzo IP del client di una richiesta potrebbe non essere uguale al relativo indirizzo IP socket perché può essere sovrascritto arbitrariamente da un utente.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV-XAAAAABKMMOjBv2NT4TY6SQVJC0zV1NURURURHRTA2MTKANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNNN-MNzA3NQ3Nzgz </br> Stringa di riferimento univoca che identifica una richiesta servita da Front Door. Viene utilizzato per la ricerca nei log di accesso e critico per la risoluzione dei problemi.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops 1 </br> Un'intestazione utilizzata da Front Door per rilevare i cicli di richiesta e gli utenti non devono prendere una dipendenza da essa. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Il campo di intestazione HTTP X-Forwarded-For (XFF) spesso identifica l'indirizzo IP di origine di un client che si connette a un server Web tramite un proxy HTTP o un servizio di bilanciamento del carico. Se è presente un'intestazione XFF esistente, Front Door aggiunge l'IP socket client o aggiunge l'intestazione XFF con l'IP del socket client. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Il campo di intestazione HTTP X-Forwarded-Host è un metodo comune utilizzato per identificare l'host originale richiesto dal client nell'intestazione della richiesta HTTP Host. Ciò è dovuto al fatto che il nome host da Front Door può essere diverso per il server back-end che gestisce la richiesta. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Il campo di intestazione HTTP X-Forwarded-Proto viene spesso utilizzato per identificare il protocollo di origine di una richiesta HTTP perché Front Door, in base alla configurazione, potrebbe comunicare con il back-end utilizzando HTTPS. Questo vale anche se la richiesta al proxy inverso è HTTP. |
| X-FD-HealthProbe | Il campo di intestazione HTTP X-FD-HealthProbe viene utilizzato per identificare il probe di integrità da Front Door. Se questa intestazione è impostata su 1, la richiesta è il probe di integrità. È possibile utilizzare quando si desidera un accesso rigoroso da un particolare porta frontale con campo di intestazione X-Forwarded-Host. |

## <a name="front-door-to-client"></a>Da Frontdoor a client

Tutte le intestazioni inviate a Front Door dal back-end vengono passate anche al client. Di seguito sono riportate le intestazioni inviate da Front Door ai client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV-XAAAAABKMMOjBv2NT4TY6SQVJC0zV1NURURURHRTA2MTKANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNNN-MNzA3NQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Frontdoor. Questo è fondamentale per la risoluzione dei problemi in quanto viene utilizzato per la ricerca nei log di accesso.|

## <a name="next-steps"></a>Passaggi successivi

- [Creare una frontdoor](quickstart-create-front-door.md)
- [Come funziona Porta anteriore](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
