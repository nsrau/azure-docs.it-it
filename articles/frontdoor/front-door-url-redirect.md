---
title: Sportello anteriore di Azure-Reindirizzamento URL | Microsoft Docs
description: Questo articolo illustra come supportare il reindirizzamento URL per le regole di routing di Azure.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442050"
---
# <a name="url-redirect"></a>Reindirizzamento URL
Lo sportello anteriore di Azure può reindirizzare il traffico a ognuno dei livelli seguenti: protocollo, nome host, percorso, stringa di query. Queste funzionalità possono essere configurate per i singoli microservizi perché il reindirizzamento è basato sul percorso. Ciò consente di semplificare la configurazione dell'applicazione ottimizzando l'utilizzo delle risorse e supporta nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Reindirizzamento dell'URL della porta anteriore di Azure":::

## <a name="redirection-types"></a>Tipi di Reindirizzamento
Un tipo di reindirizzamento imposta il codice di stato della risposta per i client per comprendere lo scopo del reindirizzamento. Sono supportati i tipi di reindirizzamento seguenti:

- **301 (spostato in modo permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente. Tutti i riferimenti futuri a questa risorsa utilizzeranno uno degli URI racchiusi. Usare il codice di stato 301 per il Reindirizzamento da HTTP a HTTPS. 
- **302 (Found)**: indica che la risorsa di destinazione è temporaneamente in un URI diverso. Poiché il reindirizzamento può variare in un'occasione, il client deve continuare a usare l'URI di richiesta effettivo per richieste future.
- **307 (Reindirizzamento temporaneo)**: indica che la risorsa di destinazione è temporaneamente in un URI diverso. L'agente utente non deve modificare il metodo di richiesta se esegue un reindirizzamento automatico a tale URI. Poiché il reindirizzamento può variare nel tempo, il client deve continuare a usare l'URI di richiesta effettivo originale per richieste future.
- **308 (Reindirizzamento permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente. Tutti i riferimenti futuri a questa risorsa devono usare uno degli URI racchiusi.

## <a name="redirection-protocol"></a>Protocollo di Reindirizzamento
È possibile impostare il protocollo che verrà utilizzato per il reindirizzamento. I casi d'uso più comuni della funzionalità di reindirizzamento, consiste nell'impostare il Reindirizzamento da HTTP a HTTPS.

- **Solo HTTPS**: impostare il protocollo solo su HTTPS, se si intende reindirizzare il traffico da http a HTTPS. Lo sportello anteriore di Azure consiglia di impostare sempre il reindirizzamento solo su HTTPS.
- **Solo http**: reindirizza la richiesta in ingresso a http. Utilizzare questo valore solo se si desidera che il traffico HTTP non sia crittografato.
- **Request match**: questa opzione mantiene il protocollo usato dalla richiesta in ingresso. Pertanto, una richiesta HTTP rimane HTTP e una richiesta HTTPS rimane il reindirizzamento post HTTPS.

## <a name="destination-host"></a>Host di destinazione
Come parte della configurazione di un routing di reindirizzamento, è anche possibile modificare il nome host o il dominio per la richiesta di reindirizzamento. È possibile impostare questo campo per modificare il nome host nell'URL per il reindirizzamento oppure mantenere il nome host dalla richiesta in ingresso. Quindi, usando questo campo è possibile reindirizzare tutte le richieste inviate `https://www.contoso.com/*` a `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Percorso di destinazione
Per i casi in cui si desidera sostituire il segmento di percorso di un URL come parte del reindirizzamento, è possibile impostare questo campo con il nuovo valore del percorso. In caso contrario, è possibile scegliere di mantenere il valore del percorso come parte del reindirizzamento. Quindi, usando questo campo, è possibile reindirizzare tutte le richieste inviate a `https://www.contoso.com/\*` a  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parametri della stringa di query
È anche possibile sostituire i parametri della stringa di query nell'URL reindirizzato. Per sostituire una stringa di query esistente dall'URL della richiesta in ingresso, impostare questo campo su' Sostituisci ' e quindi impostare il valore appropriato. In caso contrario, è possibile mantenere il set originale di stringhe di query impostando il campo su' preserve '. Ad esempio, usando questo campo, è possibile reindirizzare tutto il traffico inviato a `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Frammento di destinazione
Il frammento di destinazione è la parte dell'URL dopo ' #', che viene utilizzata dal browser per la visualizzazione di una sezione specifica di una pagina Web. È possibile impostare questo campo per aggiungere un frammento all'URL di reindirizzamento.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
