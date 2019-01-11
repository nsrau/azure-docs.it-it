---
title: Ottimizzare i tempi di query nel server di Database di Azure per PostgreSQL tramite la strategia di archiviazione delle tabelle TOAST
description: Questo articolo descrive come ottimizzare i tempi di query con la strategia di archiviazione delle tabelle TOAST in un server di Database di Azure per PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 1fb818a65e26f969f72131b0f5265f3efdd36bb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542217"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Ottimizzazione dei tempi di query con la strategia di archiviazione delle tabelle TOAST 
Questo articolo descrive come ottimizzare i tempi di query con la strategia di archiviazione delle tabelle TOAST.

## <a name="toast-table-storage-strategies"></a>Strategia di archiviazione delle tabelle TOAST
Esistono quattro strategie differenti per archiviare su disco colonne che supportano TOAST, che rappresentano varie combinazioni tra la compressione e l'archiviazione out-of-line. La strategia può essere impostata a livello di tipo di dati e a livello di colonna.
- **Plain** impedisce la compressione o l'archiviazione out-of-line e disabilita inoltre l'uso di intestazioni a un byte per i tipi varlena. **Plain** è l'unica strategia possibile per le colonne con tipi di dati che non supportano TOAST.
- **Extended** consente sia la compressione che l'archiviazione out-of-line. **Extended** è la strategia predefinita per la maggior parte dei tipi di dati che supportano TOAST. Verrà prima tentata la compressione e poi l'archiviazione out-of-line se la riga è ancora troppo grande.
- **External** consente l'archiviazione out-of-line, ma non la compressione. L'uso della strategia **External** consentirà di velocizzare le operazioni sottostringa su testo esteso e colonne bytea, al prezzo di maggiore spazio di archiviazione, perché queste operazioni sono ottimizzate per recuperare solo le parti richieste del valore out-of-line quando non è compresso.
- **Main** consente la compressione, ma non l'archiviazione out-of-line. L'archiviazione out-of-line verrà comunque eseguita per tali colonne, ma solo come ultima risorsa quando non esiste altro modo per rendere la riga sufficientemente piccola da rientrare in una pagina.

## <a name="using-toast-table-storage-strategies"></a>Uso delle strategie di archiviazione delle tabelle TOAST
Se le query accedono a tipi di dati che supportano TOAST, valutare la possibilità di usare **Main** invece dell'opzione predefinita **Extended** per ridurre i tempi di query. **Main** non preclude l'archiviazione out-of-line. D'altra parte, se le query non accedono a tipi di dati che supportano TOAST, potrebbe essere preferibile mantenere l'opzione **Extended**. In questo modo, una parte maggiore delle righe della tabella principale rientrerà nella cache del buffer condivisa, a vantaggio delle prestazioni.

Per un carico di lavoro che usa uno schema con tabelle estese in larghezza e numeri di caratteri elevati, prendere in considerazione l'uso di tabelle TOAST PostgreSQL. Una tabella di esempio di un client includeva più di 350 colonne con varie colonne estese a 255 caratteri. Il tempo di query risultante dal benchmark è risultato ridotto da 4203 secondi a 467 secondi, con un miglioramento pari all'89%, dopo la conversione della strategia di archiviazione TOAST a **Main**.

## <a name="next-steps"></a>Passaggi successivi
Esaminare il carico di lavoro in base alle caratteristiche sopra indicate. 

Consultare la documentazione di PostgreSQL seguente: [Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) (Capitolo 68: Archiviazione fisica del database) 