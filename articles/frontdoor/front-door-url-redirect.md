---
title: Porta frontale di Azure - Reindirizzamento URL Documenti Microsoft
description: Questo articolo consente di comprendere in che modo Azure Front Door supporta il reindirizzamento degli URL per le route, se configurato.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295467"
---
# <a name="url-redirect"></a>Reindirizzamento URL
È possibile usare Porta frontale di Azure per reindirizzare il traffico. È possibile reindirizzare il traffico a più livelli (protocollo, nome host, percorso, stringa di query) e tutte le funzionalità possono essere configurate per singoli microservizi poiché il reindirizzamento è basato sul percorso. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.
</br>

![Reindirizzamento URL porta frontale di AzureAzure Front Door URL Redirect][1]

## <a name="redirection-types"></a>Tipi di reindirizzamento
Un tipo di reindirizzamento imposta il codice di stato della risposta per i client per comprendere lo scopo del reindirizzamento. Sono supportati i seguenti tipi di reindirizzamento:

- **301 (Spostato in modo permanente):** indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente e tutti i riferimenti futuri a questa risorsa devono utilizzare uno degli URI racchiusi. Usare il codice di stato 301 per il reindirizzamento da HTTP a HTTPS. 
- **302 (Trovato):** indica che la risorsa di destinazione si trova temporaneamente in un URI diverso. Poiché il reindirizzamento potrebbe essere modificato in alcuni casi, il client deve continuare a utilizzare l'URI di richiesta effettivo per le richieste future.
- **307 (Reindirizzamento temporaneo):** indica che la risorsa di destinazione si trova temporaneamente in un URI diverso e che l'agente utente NON deve modificare il metodo di richiesta se esegue un reindirizzamento automatico a tale URI. Poiché il reindirizzamento può cambiare nel tempo, il client deve continuare a utilizzare l'URI della richiesta effettivo originale per le richieste future.
- **308 (Reindirizzamento permanente):** indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente e tutti i riferimenti futuri a questa risorsa devono utilizzare uno degli URI racchiusi. I client con funzionalità di modifica dei collegamenti devono ricollegare automaticamente i riferimenti all'URI di richiesta effettivo a uno o più nuovi riferimenti inviati dal server, ove possibile.

## <a name="redirection-protocol"></a>Protocollo di reindirizzamento
È possibile impostare il protocollo che verrà utilizzato per il reindirizzamento. Ciò consente uno dei casi d'uso più comuni di funzionalità di reindirizzamento, vale a dire impostare il reindirizzamento HTTP a HTTPS.

- **Solo HTTPS**: Impostare il protocollo su SOLO HTTPS, se si desidera reindirizzare il traffico da HTTP a HTTPS. Azure Front Door consiglia di impostare sempre il reindirizzamento su HTTPS.
- **Solo HTTP**: Reindirizza la richiesta in ingresso a HTTP. Utilizzare questo valore solo se si desidera mantenere il traffico HTTP non crittografato.
- **Richiesta di corrispondenza:** questa opzione mantiene il protocollo utilizzato dalla richiesta in ingresso. Pertanto, una richiesta HTTP rimane HTTP e una richiesta HTTPS rimane HTTPS post-redirection.

## <a name="destination-host"></a>Host di destinazione
Come parte della configurazione di un routing di reindirizzamento, è anche possibile modificare il nome host o il dominio per la richiesta di reindirizzamento. È possibile impostare questo campo per modificare il nome host nell'URL per il reindirizzamento o mantenere in altro modo il nome host dalla richiesta in ingresso. Quindi, utilizzando questo campo è possibile `https://www.contoso.com/*` `https://www.fabrikam.com/*`reindirizzare tutte le richieste inviate a .

## <a name="destination-path"></a>Percorso di destinazione
Per i casi in cui si desidera sostituire il segmento di percorso di un URL come parte del reindirizzamento, è possibile impostare questo campo con il nuovo valore di percorso. In caso contrario, è possibile scegliere di mantenere il valore del percorso come parte di redirect. Quindi, utilizzando questo campo, è possibile `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`reindirizzare tutte le richieste inviate a .

## <a name="query-string-parameters"></a>Parametri della stringa di query
È inoltre possibile sostituire i parametri della stringa di query nell'URL reindirizzato. Per sostituire qualsiasi stringa di query esistente dall'URL della richiesta in ingresso, impostare questo campo su 'Sostituisci' e quindi impostare il valore appropriato. In caso contrario, è possibile mantenere il set originale di stringhe di query impostando il campo su 'Preserve'. Ad esempio, utilizzando questo campo, è possibile `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`reindirizzare tutto il traffico inviato a . 

## <a name="destination-fragment"></a>Frammento di destinazione
Il frammento di destinazione è la parte dell'URL dopo il simbolo ''', normalmente utilizzata dai browser per atterrare su una sezione specifica di una pagina. È possibile impostare questo campo per aggiungere un frammento all'URL di reindirizzamento.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png