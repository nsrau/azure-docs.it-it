---
title: Sportello anteriore di Azure-riscrittura URL | Microsoft Docs
description: Questo articolo consente di comprendere in che modo Azure front door riscrive l'URL per le route, se configurato.
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445491"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Riscrittura di URL (percorso di trasferimento personalizzato)
Il front-end di Azure supporta la riscrittura dell'URL configurando un **percorso di invio personalizzato** facoltativo da usare quando si costruisce la richiesta da trasmettere al back-end. Per impostazione predefinita, se non viene fornito un percorso di inoltri personalizzato, la porta anteriore copia il percorso dell'URL in ingresso nell'URL usato nella richiesta da inviare. L'intestazione Host usata nella richiesta inoltrata viene configurata per il back-end selezionato. Vedere [Intestazione Host di back-end](front-door-backend-pool.md#hostheader) per informazioni su cosa può fare l'intestazione e come configurarla.

La parte potente della riscrittura dell'URL è che il percorso di invio personalizzato copierà qualsiasi parte del percorso in ingresso che corrisponde a un percorso con caratteri jolly al percorso avanti. questi segmenti di percorso sono i segmenti **verdi** nell'esempio seguente:
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Riscrittura URL in Frontdoor di Azure":::

## <a name="url-rewrite-example"></a>Esempio di riscrittura URL
Si consideri una regola di routing con la seguente combinazione di host e percorsi front-end configurati:

| Hosts      | Percorsi       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

La prima colonna della tabella sottostante mostra esempi di richieste in arrivo e la seconda colonna mostra quale sarebbe il percorso "più specifico" che corrisponde alla route "Percorso".  La terza e la colonna risultante della tabella sono esempi di percorsi di **inoltri personalizzati**configurati.

Per esempio, nella seconda riga, si legge che per le richieste in arrivo `www.contoso.com/sub`, se il percorso di inoltro personalizzato fosse `/`, allora il percorso di trasferimento sarebbe `/sub`. Se il percorso di trasferimento personalizzato fosse `/fwd/`, il percorso inoltrato sarebbe `/fwd/sub`. E così via, per le colonne rimanenti. Le parti **evidenziate** dei percorsi seguenti rappresentano le parti incluse nella corrispondenza dei caratteri jolly.

| Richiesta in ingresso       | Percorso di corrispondenza più specifico | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| \.contoso.com/www            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**Sub** contoso.com/www     | /\*                      | /**Sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| \.contoso.com/www**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| \.contoso.com/foo www         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.contoso.com/foo/www        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| \.**barra** contoso.com/foo/www | /foo/\*                  | /**bar**   | /fwd/**bar**   | **barra** /foo/   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>Impostazioni facoltative
Per le impostazioni delle regole di gestione specificate, è inoltre possibile specificare impostazioni facoltative aggiuntive:

* **Configurazione della cache** : se disabilitato o non specificato, le richieste che corrispondono a questa regola di routing non tenterà di usare il contenuto memorizzato nella cache, ma recupereranno sempre dal back-end. Altre informazioni, vedere [Memorizzazione nella cache con Frontdoor](front-door-caching.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
