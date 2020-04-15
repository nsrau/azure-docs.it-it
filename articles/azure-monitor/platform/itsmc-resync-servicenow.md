---
title: Come risolvere manualmente i problemi di sincronizzazione di ServiceNow
description: Reimpostare la connessione su ServiceNow in modo che gli avvisi in Microsoft Azure possano chiamare nuovamente ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313675"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Come risolvere manualmente i problemi di sincronizzazione di ServiceNow

Monitoraggio di Azure può connettersi a provider di gestione dei servizi IT (ITSM) di terze parti. ServiceNow è uno di questi provider.

For security reasons, you may need to refresh the authentication token used for your connection with ServiceNow.
Utilizzare il processo di sincronizzazione seguente per riattivare la connessione e aggiornare il token:


1. Cerca la soluzione nel banner di ricerca in alto, quindi seleziona le soluzioni pertinenti

    ![Nuova connessione](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Nella schermata della soluzione, scegliere "Seleziona tutto" nel filtro di sottoscrizione e quindi filtrare in base a "ServiceDesk"

    ![Nuova connessione](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selezionare la soluzione della connessione ITSM.
1. Selezionare Connessione ITSM nel banner di sinistra.

    ![Nuova connessione](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selezionare ogni connettore dall'elenco. 
    1. Fare clic sul nome del connettore per configurarlo
    1. Eliminare tutti i connettori non più in uso

    1. Aggiornare i campi in base a [queste definizioni](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) in base al tipo di partner

    1. Fare clic su sincronizzazione

       ![Nuova connessione](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clicca su Salva

        ![Nuova connessione](media/itsmc-resync-servicenow/save-8bit.png)

f.    Esaminare le notifiche per verificare se il processo è stato completato con successo 

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle connessioni di [gestione dei servizi IT](itsmc-connections.md)
