---
title: Servizio Frontdoor di Azure - riscrittura URL | Microsoft Docs
description: Questo articolo aiuta a comprendere come il servizio Frontdoor di Azure, se configurato, effettui la riscrittura URL per le route.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736183"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Riscrittura di URL (percorso di trasferimento personalizzato)
Il servizio Frontdoor di Azure supporta la riscrittura URL consentendo di configurare un **percorso di trasferimento personalizzato** opzionale da usare quando si costruisce la richiesta da trasferire al back-end. Per impostazione predefinita, se non viene specificato alcun percorso di trasferimento personalizzato, Frontdoor copia il percorso dell'URL in ingresso nell'URL usato nella richiesta inoltrata. L'intestazione Host usata nella richiesta inoltrata viene configurata per il back-end selezionato. Vedere [Intestazione Host di back-end](front-door-backend-pool.md#hostheader) per informazioni su cosa può fare l'intestazione e come configurarla.

La parte più significativa della riscrittura URL che usa il percorso di inoltro personalizzato è costituita dalla copia di qualsiasi parte del percorso in ingresso corrispondente a un percorso con caratteri jolly nel percorso di trasferimento (questi segmenti di percorso sono i segmenti **verdi** indicati nell'esempio che segue):
</br>
![Riscrittura URL in Frontdoor di Azure][1]

## <a name="url-rewrite-example"></a>Esempio di riscrittura URL
Prendere in considerazione una regola di gestione con i seguenti host front-end e i percorsi configurati:

| Hosts      | Percorsi       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

La prima colonna della tabella sottostante mostra esempi di richieste in arrivo e la seconda colonna mostra quale sarebbe il percorso "più specifico" che corrisponde alla route "Percorso".  La terza colonna e quelle successive nella prima riga della tabella riportano esempi di **Percorsi di trasferimento personalizzati** configurati, con le righe restanti che rappresentano esempi di come risulterebbe il percorso di richiesta trasferito se corrispondesse alla richiesta in tale riga.

Per esempio, nella seconda riga, si legge che per le richieste in arrivo `www.contoso.com/sub`, se il percorso di inoltro personalizzato fosse `/`, allora il percorso di trasferimento sarebbe `/sub`. Se il percorso di trasferimento personalizzato fosse `/fwd/`, il percorso inoltrato sarebbe `/fwd/sub`. E così via, per le colonne rimanenti. Le parti **evidenziate** dei percorsi seguenti rappresentano le parti incluse nella corrispondenza dei caratteri jolly.


| Richiesta in ingresso       | Percorso di corrispondenza più specifico | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Impostazioni facoltative
Per le impostazioni delle regole di gestione specificate, è inoltre possibile specificare impostazioni facoltative aggiuntive:

* **Configurazione di cache**: se disabilitata o non specificata, le richieste che corrispondono a questa regola di gestione non proveranno a usare il contenuto memorizzato nella cache, recuperandolo in alternativa sempre dal back-end. Altre informazioni, vedere [Memorizzazione nella cache con Frontdoor](front-door-caching.md).



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg