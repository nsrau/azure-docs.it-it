---
title: Azure Front sportello servizio - URL di reindirizzamento | Microsoft Docs
description: Questo articolo aiuta a comprendere come servizio di ingresso principale di Azure supporta il reindirizzamento degli URL per le route, se configurato.
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
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332604"
---
# <a name="url-redirect"></a>Reindirizzamento URL
È possibile utilizzare servizio di ingresso principale di Azure per reindirizzare il traffico. È possibile reindirizzare il traffico a più livelli (protocollo, il nome host, percorso, stringa di query) e tutte le funzionalità configurabili per i singoli microservizi come il reindirizzamento è basata sul percorso. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.
</br>

![Reindirizzamento di URL di Azure di ingresso principale][1]

## <a name="redirection-types"></a>Tipi di reindirizzamento
Un tipo di reindirizzamento imposta il codice di stato di risposta per i client comprendere lo scopo del reindirizzamento. Sono supportati i tipi di reindirizzamento seguenti:

- **301 (spostato permanentemente)** : Indica che la risorsa di destinazione è stata assegnata un nuovo URI permanente e dovrebbero usare uno degli URI chiusi tutti i riferimenti futuri a questa risorsa. Usare il codice di stato 301 per il protocollo HTTP per il reindirizzamento HTTPS. 
- **302 (trovato)** : Indica che la risorsa di destinazione si trova temporaneamente in un URI diverso. Poiché il reindirizzamento può essere modificato in alcuni casi, il client dovrebbe continuare a usare l'URI della richiesta effettiva per le richieste future.
- **307 (reindirizzamento temporaneo)** : Indica che la risorsa di destinazione si trova temporaneamente in un URI diverso e l'agente utente non deve modificare il metodo di richiesta se esegue un reindirizzamento automatico a quell'URI. Poiché il reindirizzamento può cambiare nel tempo, il client dovrebbe continuare a usare l'URI della richiesta effettiva originali per le richieste future.
- **(Reindirizzamento permanente) 308**: Indica che la risorsa di destinazione è stata assegnata un nuovo URI permanente e dovrebbero usare uno degli URI chiusi tutti i riferimenti futuri a questa risorsa. I client con collegamento di modifica di funzionalità, suggerire a ricollegare automaticamente i riferimenti a effettiva URI della richiesta a uno o più dei riferimenti di nuovo inviati dal server, laddove possibile.

## <a name="redirection-protocol"></a>Protocollo di reindirizzamento
È possibile impostare il protocollo che verrà usato per il reindirizzamento. In questo modo per uno dei casi di utilizzo più comuni di funzionalità di reindirizzamento, che consiste nell'impostare HTTP per il reindirizzamento HTTPS.

- **Solo HTTPS**: Impostare il protocollo HTTPS, solo se si sta cercando di reindirizzare il traffico da HTTP a HTTPS. Servizio di ingresso principale di Azure consiglia di devono sempre impostare il reindirizzamento su HTTPS solo.
- **Solo HTTP**: Questo codice reindirizza la richiesta in ingresso a HTTP. Utilizzare questo valore solo se si desidera mantenere il traffico HTTP, non crittografate.
- **Corrispondenza richiesta**: Questa opzione consente di mantenere il protocollo usato dalla richiesta in ingresso. Pertanto, una richiesta HTTP rimane HTTP e una richiesta HTTPS rimane il reindirizzamento post HTTPS.

## <a name="destination-host"></a>Host di destinazione
Come parte della configurazione del routing di reindirizzamento, è anche possibile modificare il nome host o il dominio per la richiesta di reindirizzamento. È possibile impostare questo campo per modificare il nome host nell'URL di reindirizzamento o in caso contrario, mantenere il nome host della richiesta in ingresso. Pertanto, con questo campo è possibile reindirizzare tutte le richieste inviate https://www.contoso.com/ * a https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Percorso di destinazione
Per i casi in cui si desidera sostituire il segmento di percorso di un URL nell'ambito del reindirizzamento, è possibile impostare questo campo con il nuovo valore di percorso. In caso contrario, è possibile scegliere di mantenere il valore del percorso nell'ambito del reindirizzamento. Quindi, Usa questo campo, è possibile reindirizzare tutte le richieste inviate a https://www.contoso.com/ * a https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parametri della stringa di query
È inoltre possibile sostituire i parametri della stringa di query nell'URL di reindirizzamento. Per sostituire qualsiasi stringa di query esistente dall'URL della richiesta in ingresso, impostare questo campo per 'Replace' e quindi impostare il valore appropriato. In caso contrario, è possibile mantenere il set originale di stringhe di query impostando il campo su 'Preserve'. Ad esempio, con questo campo, è possibile reindirizzare tutto il traffico inviato a https://www.contoso.com/foo/bar a https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Frammento di destinazione
Il frammento di destinazione è la parte dell'URL dopo '#', in genere usato dai browser a una sezione specifica in una pagina. È possibile impostare questo campo per aggiungere un frammento all'URL di reindirizzamento.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png