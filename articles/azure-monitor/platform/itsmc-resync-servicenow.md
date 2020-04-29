---
title: Come correggere manualmente i problemi di sincronizzazione ServiceNow
description: Reimpostare la connessione a ServiceNow in modo che gli avvisi nel Microsoft Azure possano chiamare nuovamente ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81313675"
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

    1. Aggiornare i campi in base a [queste definizioni](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) in base al tipo di partner

    1. Fare clic su Sincronizza

       ![Nuova connessione](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Fare clic su Salva

        ![Nuova connessione](media/itsmc-resync-servicenow/save-8bit.png)

f.    Esaminare le notifiche per verificare se il processo è stato completato correttamente 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [connessioni di IT Service Management](itsmc-connections.md)
