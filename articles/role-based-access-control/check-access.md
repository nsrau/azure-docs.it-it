---
title: Avvio rapido - Visualizzare i ruoli assegnati a un utente tramite il portale di Azure | Microsoft Docs
description: Informazioni su come visualizzare le autorizzazioni di controllo degli accessi in base al ruolo assegnate a un utente, un gruppo, un'entità servizio o un'identità gestita tramite il portale di Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641867"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Avvio rapido - Visualizzare i ruoli assegnati a un utente tramite il portale di Azure

È possibile usare il pannello **Controllo di accesso (IAM)** in [Controllo degli accessi in base al ruolo](overview.md) per visualizzare le assegnazioni di ruolo per più utenti, gruppi, entità servizio e identità gestite, ma in alcuni casi è sufficiente esaminare rapidamente le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita. Il modo più semplice per eseguire questa operazione è usare la funzionalità **Verifica l'accesso** nel portale di Azure.

## <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Seguire questi passaggi per visualizzare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita nell'ambito della sottoscrizione.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla propria sottoscrizione.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/check-access/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/check-access/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/check-access/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e il portale di Azure](quickstart-assign-role-user-portal.md)
