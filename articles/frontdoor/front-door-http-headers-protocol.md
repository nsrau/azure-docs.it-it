---
title: Il supporto di ingresso principale di Azure - intestazioni HTTP | Microsoft Docs
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
ms.openlocfilehash: 40bfdfc3837da12f62864433508482a65def291c
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407318"
---
# <a name="azure-front-door-service---http-headers-support"></a>Il supporto di ingresso principale di Azure - intestazioni HTTP
Questo documento descrive il protocollo supportato dal servizio Frontdoor di Azure con le varie parti del percorso di chiamate come illustrato nell'immagine seguente. Le sezioni seguenti forniscono informazioni dettagliate sulle intestazioni HTTP supportate da Frontdoor.

![Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP][1]

>[!WARNING]
>Il servizio Frontdoor di Azure non offre garanzie su eventuali intestazioni HTTP non documentate qui.

## <a name="1-client-to-front-door"></a>1. Da client a Frontdoor
Frontdoor accetta la maggior parte delle intestazioni della richiesta in entrata (senza modificarle); tuttavia, esistono alcune intestazioni riservate che verranno rimosse dalla richiesta in ingresso se vengono inviate. Sono incluse le intestazioni con i prefissi seguenti:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Da Frontdoor a back-end

Frontdoor includerà le intestazioni della richiesta in ingresso, a meno che non siano state rimosse a causa di restrizioni menzionate sopra. Frontdoor aggiungerà anche le intestazioni seguenti:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| Via |  *Via: 1.1 azure* </br> Porta d'ingresso aggiunge la versione del client HTTP seguita da 'Azure' come valore dell'intestazione Via. Viene aggiunto per indicare la versione del client HTTP e tale porta principale di Azure è stato un destinatario intermedio per la richiesta tra client e il back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Rappresenta l'indirizzo Internet Protocol "client" associato alla richiesta in fase di elaborazione. Ad esempio, una richiesta proveniente da un proxy può aggiungere l'intestazione X-Forwarded-For per indicare l'indirizzo IP del chiamante originale. |
| X-Azure-SocketIP | *X-Azure-SocketIP: 127.0.0.1* </br> Rappresenta l'indirizzo protocollo Internet del socket associato alla connessione TCP, la richiesta corrente proviene. Indirizzo IP del Client della richiesta non può essere uguale al relativo indirizzo IP Socket perché può essere arbitrariamente sovrascritto da un utente finale.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Frontdoor. È fondamentale per la risoluzione dei problemi relativi a come viene usato per eseguire ricerche nei log di accesso.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> Si tratta di un'intestazione utilizzata da Frontdoor per rilevare i cicli di richiesta e gli utenti non dovranno dipendere da esso. |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Il campo di intestazione X-Forwarded-For (XFF) HTTP è un metodo comune per identificare l'indirizzo IP di origine di un client che si connette a un server web tramite un bilanciamento del carico o proxy HTTP. Se si è verificato un'intestazione XFF esistente, quindi porta d'ingresso aggiunge l'indirizzo IP socket client a un altro percorso aggiunge un'intestazione XFF con l'indirizzo IP socket client. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Il campo dell'intestazione HTTP X-inoltrati-Host è un metodo comune per l'identificazione dell'host originale richiesto dal client nell'intestazione della richiesta HTTP di Host, poiché il nome host da porta principale può essere diverso per il server back-end gestisce la richiesta. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Il campo dell'intestazione HTTP X-Forwarded-Proto è un metodo comune per identificare il protocollo di origine di una richiesta HTTP, poiché a seconda della configurazione porta d'ingresso possono comunicare con il back-end usando HTTPS, anche se la richiesta per il proxy inverso è il protocollo HTTP. |

## <a name="3-front-door-to-client"></a>3. Da Frontdoor a client

Di seguito le intestazioni che vengono inviate da Frontdoor ai client. Qualsiasi intestazione inviata dal back-end al Frontdoor passa anche per il client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Frontdoor. È fondamentale per la risoluzione dei problemi relativi a come viene usato per eseguire ricerche nei log di accesso.|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png