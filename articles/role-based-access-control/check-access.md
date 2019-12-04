---
title: "Avvio rapido: Visualizzare l'accesso di un utente alle risorse di Azure"
description: Questo argomento di avvio rapido illustra come visualizzare l'accesso di un utente o di un'altra entità di sicurezza alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure.
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
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419609"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Guida introduttiva: Visualizzare l'accesso di un utente alle risorse di Azure

È possibile usare il pannello **Controllo di accesso (IAM)** in [Controllo degli accessi in base al ruolo](overview.md) per visualizzare l'accesso di un utente o di un'altra entità di sicurezza alle risorse di Azure. Tuttavia, a volte può essere necessario visualizzare rapidamente l'accesso di un singolo utente o di un'altra entità di sicurezza. Il modo più semplice per eseguire questa operazione è usare la funzionalità **Verifica l'accesso** nel portale di Azure.

## <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

 Il modo per visualizzare l'accesso di un utente consiste nell'elencare le relative assegnazioni di ruolo. Seguire questi passaggi per visualizzare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita nell'ambito della sottoscrizione.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla propria sottoscrizione.

1. Fare clic su **Controllo di accesso (IAM)** .

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
> [Esercitazione: Concedere a un utente l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](quickstart-assign-role-user-portal.md)
