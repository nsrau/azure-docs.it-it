---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180788"
---
- **Dettagli sugli ordini**. Quando viene creato un ordine, l'indirizzo di spedizione, indirizzo di posta elettronica e le informazioni di contatto dell'utente viene archiviato nel portale di Azure. Le informazioni salvate includono:
  - Nome del contatto
  - Numero di telefono
  - Indirizzo di posta elettronica
  - Indirizzo
  - city
  - ZIP Code/postal code
  - Stato
  - Paese/provincia/area geografica
  - Numero di tracciabilità della spedizione

    I dettagli dell'ordine vengono crittografati e archiviati nel servizio. Il servizio mantiene le informazioni fino a quando non si elimina esplicitamente la risorsa o l'ordine. L'eliminazione della risorsa e l'ordine corrispondente viene bloccato dal momento in cui che il dispositivo viene spedito fino a quando il dispositivo torna a Microsoft.

- **Indirizzo di spedizione**. Dopo che è stato effettuato un ordine, il servizio Data Box fornisce l'indirizzo di spedizione a vettori di terze parti come gruppo di continuità.

- **Gli utenti di condividere**. Gli utenti nel dispositivo possono accedere anche dati presenti nelle condivisioni. Un elenco di utenti che possono accedere ai dati di condivisione può essere visualizzato. Quando le condivisioni vengono eliminate, viene eliminato anche questo elenco.