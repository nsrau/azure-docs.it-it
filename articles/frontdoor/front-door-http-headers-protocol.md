---
title: Supporto per le intestazioni HTTP nel servizio Azure porta d'ingresso del protocollo | Microsoft Docs
description: Questo articolo descrive i protocolli di intestazione HTTP supportati dal servizio di ingresso principale.
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736642"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Supporto del protocollo per le intestazioni HTTP nel servizio di ingresso principale di Azure
Questo articolo illustra il protocollo supportati dal servizio di ingresso principale con le parti del percorso di chiamate (vedere la figura). Le sezioni seguenti forniscono altre informazioni sulle intestazioni HTTP supportati dal servizio di ingresso principale.

![Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP][1]

>[!IMPORTANT]
>Servizio di ingresso principale non certificare eventuali intestazioni HTTP che non sono documentate qui.

## <a name="client-to-front-door-service"></a>Client servizio di ingresso principale
Servizio di ingresso principale accetta la maggior parte delle intestazioni della richiesta in ingresso senza modificarli. Alcune intestazioni riservate vengono rimossi dalla richiesta in ingresso se inviato, incluse le intestazioni con X - FD-* prefisso.

## <a name="front-door-service-to-backend"></a>Servizio di ingresso principale al back-end

Servizio di ingresso principale include le intestazioni da una richiesta in ingresso a meno che non rimossi a causa di restrizioni. Porta d'ingresso aggiunge anche le intestazioni seguenti:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| Via |  Via: 1.1 azure </br> Porta d'ingresso aggiunge la versione del client HTTP aggiungendo *Azure* come valore dell'intestazione Via. Ciò indica la versione del client HTTP e tale porta principale era un destinatario intermedio per la richiesta tra client e il back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP client associato alla richiesta in fase di elaborazione. Ad esempio, una richiesta proveniente da un proxy può aggiungere l'intestazione X-Forwarded-For per indicare l'indirizzo IP del chiamante originale. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Rappresenta l'indirizzo IP del socket associato alla connessione TCP da cui ha origine la richiesta corrente. Indirizzo IP del client della richiesta potrebbe non essere uguale al relativo indirizzo IP socket poiché può essere sovrascritto in modo arbitrario da un utente.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Una stringa di riferimento univoco che identifica delle richieste servite da porta principale. Viene usato per eseguire ricerche nei log di accesso e fondamentali per la risoluzione dei problemi.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hop = 1 </br> Un'intestazione che usa una porta principale per rilevare i cicli di richiesta e gli utenti non richiederà una dipendenza su di esso. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Il campo di intestazione X-Forwarded-For (XFF) HTTP spesso identifica l'indirizzo IP di origine di un client che si connette a un server web tramite un bilanciamento del carico o proxy HTTP. Se è presente un'intestazione XFF esistente, quindi porta d'ingresso aggiunge l'indirizzo IP socket client ad esso o aggiunge l'intestazione XFF con l'indirizzo IP socket client. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Il campo dell'intestazione HTTP X-inoltrati-Host è un metodo comune utilizzato per identificare l'host originale richiesto dal client nell'intestazione della richiesta HTTP di Host. Questo avviene perché il nome host da porta principale può essere diverso per il server back-end gestisce la richiesta. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Il campo dell'intestazione HTTP X-Forwarded-Proto viene spesso usato per identificare il protocollo di origine di una richiesta HTTP in quanto porta d'ingresso, in base alla configurazione, può comunicare con il back-end tramite HTTPS. Questo vale anche se la richiesta per il proxy inverso è il protocollo HTTP. |

## <a name="front-door-service-to-client"></a>Servizio di ingresso principale per client

Anche qualsiasi intestazione inviata alla porta d'ingresso del back-end vengono passati al client. Di seguito sono le intestazioni inviate dalla porta di ingresso ai client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Frontdoor. Questo è fondamentale per la risoluzione dei problemi relativi a come viene usato per eseguire ricerche nei log di accesso.|

## <a name="next-steps"></a>Passaggi successivi

- [Creare una porta d'ingresso](quickstart-create-front-door.md)
- [Funzionamento di ingresso principale](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png