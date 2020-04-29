---
title: Sportello anteriore di Azure-Reindirizzamento URL | Microsoft Docs
description: Questo articolo illustra in che modo il portale di Azure supporta il reindirizzamento URL per le route, se configurate.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295467"
---
# <a name="url-redirect"></a>Reindirizzamento URL
È possibile usare lo sportello anteriore di Azure per reindirizzare il traffico. È possibile reindirizzare il traffico a più livelli (protocollo, nome host, percorso, stringa di query) e tutte le funzionalità possono essere configurate per i singoli microservizi perché il reindirizzamento è basato sul percorso. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.
</br>

![Reindirizzamento dell'URL della porta anteriore di Azure][1]

## <a name="redirection-types"></a>Tipi di Reindirizzamento
Un tipo di reindirizzamento imposta il codice di stato della risposta per i client per comprendere lo scopo del reindirizzamento. Sono supportati i tipi di reindirizzamento seguenti:

- **301 (spostato in modo permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente e tutti i riferimenti futuri a questa risorsa devono usare uno degli URI racchiusi. Usare il codice di stato 301 per il Reindirizzamento da HTTP a HTTPS. 
- **302 (trovato)**: indica che la risorsa di destinazione risiede temporaneamente in un URI diverso. Poiché il reindirizzamento potrebbe essere modificato in un'occasione, il client deve continuare a usare l'URI di richiesta effettivo per richieste future.
- **307 (Reindirizzamento temporaneo)**: indica che la risorsa di destinazione risiede temporaneamente in un URI diverso e l'agente utente non deve modificare il metodo di richiesta se esegue un reindirizzamento automatico a tale URI. Poiché il reindirizzamento può variare nel tempo, il client deve continuare a usare l'URI di richiesta effettivo originale per richieste future.
- **308 (Reindirizzamento permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente e tutti i riferimenti futuri a questa risorsa devono usare uno degli URI racchiusi. I client con funzionalità di modifica dei collegamenti devono ricollegare automaticamente i riferimenti all'URI della richiesta efficace a uno o più dei nuovi riferimenti inviati dal server, ove possibile.

## <a name="redirection-protocol"></a>Protocollo di Reindirizzamento
È possibile impostare il protocollo che verrà utilizzato per il reindirizzamento. Questo consente uno dei casi d'uso più comuni della funzionalità di reindirizzamento, ovvero l'impostazione del Reindirizzamento da HTTP a HTTPS.

- **Solo HTTPS**: impostare il protocollo solo su HTTPS, se si sta cercando di reindirizzare il traffico da http a HTTPS. Lo sportello anteriore di Azure consiglia di impostare sempre il reindirizzamento solo su HTTPS.
- **Solo http**: reindirizza la richiesta in ingresso a http. Utilizzare questo valore solo se si desidera che il traffico HTTP non sia crittografato.
- **Richiesta di corrispondenza**: questa opzione mantiene il protocollo usato dalla richiesta in ingresso. Pertanto, una richiesta HTTP rimane HTTP e una richiesta HTTPS rimane il reindirizzamento post HTTPS.

## <a name="destination-host"></a>Host di destinazione
Come parte della configurazione di un routing di reindirizzamento, è anche possibile modificare il nome host o il dominio per la richiesta di reindirizzamento. È possibile impostare questo campo per modificare il nome host nell'URL per il reindirizzamento oppure mantenere il nome host dalla richiesta in ingresso. Quindi, usando questo campo è possibile reindirizzare tutte le `https://www.contoso.com/*` richieste `https://www.fabrikam.com/*`inviate a.

## <a name="destination-path"></a>Percorso di destinazione
Per i casi in cui si desidera sostituire il segmento di percorso di un URL come parte del reindirizzamento, è possibile impostare questo campo con il nuovo valore del percorso. In caso contrario, è possibile scegliere di mantenere il valore del percorso come parte del reindirizzamento. Quindi, usando questo campo, è possibile reindirizzare tutte le `https://www.contoso.com/\*` richieste `https://www.contoso.com/redirected-site`inviate a a.

## <a name="query-string-parameters"></a>Parametri della stringa di query
È anche possibile sostituire i parametri della stringa di query nell'URL reindirizzato. Per sostituire una stringa di query esistente dall'URL della richiesta in ingresso, impostare questo campo su' Sostituisci ' e quindi impostare il valore appropriato. In caso contrario, è possibile mantenere il set originale di stringhe di query impostando il campo su' preserve '. Ad esempio, usando questo campo, è possibile reindirizzare tutto il traffico `https://www.contoso.com/foo/bar` inviato `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`a. 

## <a name="destination-fragment"></a>Frammento di destinazione
Il frammento di destinazione è la parte dell'URL dopo ' #', normalmente usata dai browser per la destinazione di una sezione specifica in una pagina. È possibile impostare questo campo per aggiungere un frammento all'URL di reindirizzamento.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png