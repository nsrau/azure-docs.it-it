---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180788"
---
- **Dettagli dell'ordine**. Quando viene creato un ordine, l'indirizzo di spedizione, l'indirizzo di posta elettronica e le informazioni di contatto dell'utente vengono archiviati nel portale di Azure.When an order is created, the shipping address, email address, and contact information of the user is stored in the Azure portal. Le informazioni salvate includono:
  - Nome del contatto
  - Numero di telefono
  - Indirizzo di posta elettronica
  - Indirizzo
  - city
  - CAP/codice postale
  - State
  - Paese/regione/regione
  - Numero di tracciabilità della spedizione

    I dettagli dell'ordine vengono crittografati e memorizzati nel servizio. Il servizio conserva le informazioni fino a quando non si elimina in modo esplicito la risorsa o l'ordine. L'eliminazione della risorsa e dell'ordine corrispondente viene bloccata dal momento in cui il dispositivo viene spedito fino a quando il dispositivo non ritorna a Microsoft.

- **Indirizzo di spedizione**. Dopo aver effettuato un ordine, il servizio Data Box fornisce l'indirizzo di spedizione a vettori di terze parti come UPS.

- **Condividere gli utenti**. Gli utenti del dispositivo possono anche accedere ai dati che si trovano sulle condivisioni. È possibile visualizzare un elenco di utenti che possono accedere ai dati di condivisione. Quando le condivisioni vengono eliminate, viene eliminato anche questo elenco.