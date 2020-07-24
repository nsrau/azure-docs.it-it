---
title: Come correggere manualmente i problemi di sincronizzazione ServiceNow
description: Reimpostare la connessione a ServiceNow in modo che gli avvisi nel Microsoft Azure possano chiamare nuovamente ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087935"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Come correggere manualmente i problemi di sincronizzazione ServiceNow

Monitoraggio di Azure può connettersi a provider di gestione dei servizi IT di terze parti (ITSM). ServiceNow è uno di questi provider.

Per motivi di sicurezza, potrebbe essere necessario aggiornare il token di autenticazione usato per la connessione con ServiceNow.
Usare il processo di sincronizzazione seguente per riattivare la connessione e aggiornare il token:


1. Cercare la soluzione nel banner di ricerca superiore, quindi selezionare le soluzioni pertinenti

    ![Nuova connessione](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Nella schermata della soluzione scegliere "Seleziona tutto" nel filtro della sottoscrizione e quindi filtrare in base a "ServiceDesk".

    ![Nuova connessione](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selezionare la soluzione della connessione ITSM.
1. Selezionare connessione ITSM nel banner a sinistra.

    ![Nuova connessione](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selezionare ogni connettore dall'elenco. 
    1. Per configurarlo, fare clic sul nome del connettore
    1. Elimina tutti i connettori non più in uso

    1. Aggiornare i campi in base a [queste definizioni](./itsmc-connections.md) in base al tipo di partner

    1. Fare clic su Sincronizza

       ![Nuova connessione](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Fare clic su Salva

        ![Nuova connessione](media/itsmc-resync-servicenow/save-8bit.png)

f.    Esaminare le notifiche per verificare se il processo è stato completato correttamente 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [connessioni di IT Service Management](itsmc-connections.md)
