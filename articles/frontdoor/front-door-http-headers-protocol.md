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
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951135"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP
Questo documento descrive il protocollo supportato dal servizio Frontdoor di Azure con le varie parti del percorso di chiamate come illustrato nell'immagine seguente. Le sezioni seguenti forniscono informazioni dettagliate sulle intestazioni HTTP supportate da Frontdoor.

![Servizio Frontdoor di Azure - supporto del protocollo intestazioni HTTP][1]

>[!WARNING]
>Il servizio Frontdoor di Azure non offre garanzie su eventuali intestazioni HTTP non documentate qui.

## <a name="1-client-to-front-door"></a>1. Da client a Frontdoor

Le intestazioni seguenti verranno analizzate da Frontdoor. Per qualsiasi altra intestazione, Frontdoor funge da intermediario e invia le intestazioni al back-end dell'applicazione.

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> Un GUID che identifica una o più richieste HTTP effettuate per eseguire il rendering di una singola pagina Web. Frontdoor inoltrerà il GUID al back-end usando l'intestazione X-FD-ImpressionGuid. </br> Se il client non invia l'intestazione, Frontdoor genera automaticamente un nuovo GUID e lo invia al back-end. |

## <a name="2-front-door-to-backend"></a>2. Da Frontdoor a back-end

Le intestazioni seguenti verranno inviate da Frontdoor al back-end dell'applicazione.

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> L'ID per identificare una richiesta fornita da Frontdoor. Questo valore dell’intestazione è l’informazione più importante per la risoluzione dei problemi. Questo ID può essere usato per cercare i log di accesso. Frontdoor invia la stessa intestazione sia al client che al back-end. |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>La versione del protocollo HTTP tra client e Frontdoor. I valori possibili sono: 1.1, 2.0, ecc. |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>L'indirizzo del protocollo IP "client" associato alla richiesta in fase di elaborazione |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>Il nodo specifico di Frontdoor che ha gestito la richiesta. Questa intestazione può essere registrata dal back-end per facilitare il debug. Tuttavia, non dipendono dalle convenzioni di denominazione di Frontdoor, dalla distribuzione del traffico tra i nodi di Frontdoor o dalla disponibilità del nodo singolo specifico. |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>Un identificatore univoco (GUID) creato all'inizio dell'elaborazione della richiesta. Tutti i log di accesso creati durante l'elaborazione della richiesta verranno associati allo stesso GUID. |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>Un GUID che identifica una o più richieste HTTP effettuate per eseguire il rendering di una singola pagina Web. Sul lato client è possibile specificare questo GUID con: </br>- Invio dell'intestazione della richiesta *X-MSEdge-IG*. Ad esempio: X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>- Invio della stringa di query "ig". Ad esempio: *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F*. Se il client non invia un GUID, Frontdoor ne genera uno nuovo. |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> L'URL della richiesta originale effettuata dal client. |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>Identifica per quale profilo Frontdoor viene gestita la richiesta. Può essere usato dal back-end che ospita più applicazioni per diversi Frontdoor per determinare per quale configurazione di Frontdoor è fornita la corrente richiesta. |
| X-FD-RequestHadClientId | Riservato |
| X-FD-RequestHadClientId | Riservato |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>Allo stesso modo del X-FD-ClientIP precedente |

## <a name="3-front-door-to-client"></a>3. Da Frontdoor a client

Di seguito le intestazioni che vengono inviate da Frontdoor ai client. Qualsiasi intestazione inviata dal back-end al Frontdoor passa anche per il client.

| Intestazione  | Esempio |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>L'ID per identificare una richiesta fornita da Frontdoor. Questo valore dell'intestazione è l'informazione più importante per la risoluzione dei problemi. Questo ID può essere usato per cercare i log di accesso. Frontdoor invia la stessa intestazione sia al client che al back-end. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come creare un Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png