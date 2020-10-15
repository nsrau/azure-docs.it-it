---
title: Supporto del protocollo per le intestazioni HTTP nella porta anteriore di Azure | Microsoft Docs
description: Questo articolo descrive i protocolli di intestazione HTTP supportati dalla porta anteriore.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: cd721f13ffa128e83072819a20b17f305118b13c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626293"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Supporto del protocollo per le intestazioni HTTP nella porta anteriore di Azure
Questo articolo illustra il protocollo supportato da front-end con parti del percorso di chiamata (vedere l'immagine). Le sezioni seguenti forniscono altre informazioni sulle intestazioni HTTP supportate dalla porta anteriore.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protocollo intestazioni HTTP front door di Azure":::

>[!IMPORTANT]
>La porta anteriore non certifica le intestazioni HTTP che non sono documentate qui.

## <a name="client-to-front-door"></a>Da client a Frontdoor
La porta anteriore accetta la maggior parte delle intestazioni per la richiesta in ingresso senza modificarle. Alcune intestazioni riservate vengono rimosse dalla richiesta in ingresso, se inviate, incluse le intestazioni con il prefisso X-FD-*.

## <a name="front-door-to-backend"></a>Da Frontdoor a back-end

La porta anteriore include le intestazioni per una richiesta in ingresso, a meno che non vengano rimosse a causa di restrizioni. La porta anteriore aggiunge anche le intestazioni seguenti:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| Via |  Via: 1,1 Azure </br> La porta anteriore aggiunge la versione HTTP del client seguita da *Azure* come valore per l'intestazione via. Questa intestazione indica la versione HTTP del client e la porta anteriore è un destinatario intermedio per la richiesta tra il client e il back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP del client associato alla richiesta in fase di elaborazione. Ad esempio, una richiesta proveniente da un proxy potrebbe aggiungere l'intestazione X-inoltro-for per indicare l'indirizzo IP del chiamante originale. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP del socket associato alla connessione TCP da cui ha origine la richiesta corrente. L'indirizzo IP del client di una richiesta potrebbe non essere uguale all'indirizzo IP del socket perché può essere sovrascritto arbitrariamente da un utente.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Stringa di riferimento univoca che identifica una richiesta servita dalla porta anteriore. Viene usato per la ricerca nei log di accesso e per la risoluzione dei problemi.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hop = 1 </br> Un'intestazione usata dalla porta anteriore per rilevare i cicli di richiesta e gli utenti non devono assumere una dipendenza. |
| X-Azure-FDID | X-Azure-FDID: 55ce4ed1-4B06-4bf1-B40E-4638452104da<br/> Una stringa di riferimento che identifica la richiesta proviene da una risorsa front door specifica. Il valore può essere visualizzato nel portale di Azure o recuperato tramite l'API di gestione. È possibile usare questa intestazione in combinazione con ACL IP per bloccare l'endpoint in modo da accettare solo le richieste provenienti da una risorsa front door specifica. Per [altri dettagli](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) , vedere le domande frequenti |
| X-Forwarded-For | X-Inoltred-per: 127.0.0.1 </br> Il campo di intestazione HTTP X-inoltro-for (XFF) identifica spesso l'indirizzo IP di origine di un client che si connette a un server Web tramite un proxy HTTP o un servizio di bilanciamento del carico. Se è presente un'intestazione XFF esistente, la porta anteriore aggiunge l'indirizzo IP del socket client o aggiunge l'intestazione XFF con l'indirizzo IP del socket client. |
| X-Forwarded-Host | X-Inoltred-host: contoso.azurefd.net </br> Il campo dell'intestazione HTTP X-Inoltred-host è un metodo comune usato per identificare l'host originale richiesto dal client nell'intestazione della richiesta HTTP dell'host. Questo è dovuto al fatto che il nome host da sportello anteriore potrebbe differire per il server back-end che gestisce la richiesta. |
| X-Forwarded-Proto | X-Inoltred-proto: http </br> Il campo di intestazione HTTP X-Inoltred-proto viene spesso usato per identificare il protocollo di origine di una richiesta HTTP perché la porta anteriore, basata sulla configurazione, potrebbe comunicare con il back-end tramite HTTPS. Questo vale anche se la richiesta al proxy inverso è HTTP. |
| X-FD-HealthProbe | Il campo di intestazione HTTP X-FD-HealthProbe viene usato per identificare il probe di integrità dalla porta anteriore. Se questa intestazione è impostata su 1, la richiesta è un probe di integrità. È possibile usare quando si vuole limitare l'accesso da un particolare sportello anteriore con il campo di intestazione X-Inoltred-host. |
|X-Azure-FDID | X-Azure-FDID intestazione: 437c82cd-360A-4a54-94c3-5ff707647783 </br> Questo campo contiene frontdoorID che possono essere usati per identificare la porta anteriore da cui provengono le richieste in ingresso. Questo campo viene popolato dal servizio front door. | 


## <a name="front-door-to-client"></a>Da Frontdoor a client

Tutte le intestazioni inviate alla porta anteriore dal back-end vengono passate anche al client. Di seguito sono riportate le intestazioni inviate dalla porta anteriore ai client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da sportello anteriore, che è fondamentale per la risoluzione dei problemi, perché viene usata per la ricerca nei log di accesso.|

## <a name="next-steps"></a>Passaggi successivi

- [Creare una porta anteriore](quickstart-create-front-door.md)
- [Funzionamento della porta anteriore](front-door-routing-architecture.md)
