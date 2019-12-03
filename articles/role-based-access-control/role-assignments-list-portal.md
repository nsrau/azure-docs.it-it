---
title: Elencare le assegnazioni di ruolo usando RBAC di Azure e il portale di Azure
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando il controllo degli accessi in base al ruolo di Azure (RBAC) e il portale di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 25e11ae1311df9d0392340b32e0691298f78ee1c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710425"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Elencare le assegnazioni di ruolo usando RBAC di Azure e il portale di Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] questo articolo descrive come elencare le assegnazioni di ruolo usando il portale di Azure.

## <a name="list-role-assignments-for-a-user-or-group"></a>Elencare le assegnazioni di ruolo per un utente o un gruppo

Il modo più semplice per visualizzare i ruoli assegnati a un utente o a un gruppo in una sottoscrizione consiste nell'usare il riquadro **risorse di Azure** .

1. Nella portale di Azure fare clic su **tutti i servizi** , quindi selezionare **utenti** o **gruppi**.

1. Fare clic sull'utente o sul gruppo per il quale si desidera elencare le assegnazioni di ruolo.

1. Fare clic su **Risorse di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'utente o al gruppo selezionato in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazione dei ruoli a un utente](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizioni** .

## <a name="list-role-assignments-at-a-scope"></a>Elencare le assegnazioni di ruolo in un ambito

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Elencare le assegnazioni di ruolo per un utente in un ambito

Per elencare l'accesso per un utente, un gruppo, un'entità servizio o un'identità gestita, è possibile elencare le assegnazioni di ruolo. Attenersi alla procedura seguente per elencare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita in un determinato ambito.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-list-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-list-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e il portale di Azure](role-assignments-portal.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
