---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263314"
---
|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|routePrefix|api|Il prefisso della route che si applica a tutte le route. Utilizzare una stringa vuota per rimuovere il prefisso predefinito. |
|maxOutstandingRequests|200<sup>*</sup>|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo. Questo limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". Ciò consente ai chiamanti di usare strategie di ripetizione dei tentativi basate sul tempo e di controllare la latenza massima delle richieste. Questa impostazione controlla solo l'accodamento che si verifica all'interno del percorso di esecuzione dell'host dello script. Altre code, ad esempio la coda di richieste ASP.NET, saranno valide e non interessate da questa impostazione. <sup>*</sup> Il valore predefinito per la versione 1. x è unbounded (`-1`). Il valore predefinito per la versione 2.x in un piano a consumo è 200. Il valore predefinito per la versione 2. x in un piano dedicato è unbounded (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Il numero massimo di funzioni HTTP che verrà eseguito in parallelo. Ciò consente di controllare la concorrenza e pertanto di semplificare la gestione dell'uso delle risorse. Ad esempio, potrebbe essere presente una funzione HTTP che usa una quantità di risorse di sistema (memoria/CPU/socket) tale da creare problemi quando la concorrenza è troppo elevata. Oppure potrebbe essere presente una funzione che invia richieste a un servizio di terze parti e tali chiamate devono essere a frequenza limitata. In questi casi potrebbe risultare utile l'applicazione di una limitazione. <sup>*</sup> Il valore predefinito per la versione 1. x è unbounded (`-1`). Il valore predefinito per la versione 2.x in un piano a consumo è 100. Il valore predefinito per la versione 2. x in un piano dedicato è unbounded (`-1`).|
|dynamicThrottlesEnabled|true<sup>*</sup>|Quando è abilitata, questa impostazione determina la pipeline di elaborazione della richiesta per il controllo periodico delle prestazioni dei contatori del sistema, ad esempio connessioni/thread/processi/memoria/CPU e così via. Se uno di questi contatori supera una soglia massima predefinita (80%), le richieste verranno rifiutate con una risposta 429 "Occupato" fino a quando i contatori non tornano a livelli normali. <sup>*</sup> Il valore predefinito per la versione 1. x è false. Il valore predefinito per la versione 2.x in un piano a consumo è true. Il valore predefinito per la versione 2.x in un piano dedicato è false.|
