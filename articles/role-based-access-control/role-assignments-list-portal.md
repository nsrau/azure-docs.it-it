---
title: Elencare le assegnazioni di ruolo di Azure usando la portale di Azure-RBAC
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ed7f2b57df97a99a73ad191e45fb900a04f8021f
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983024"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Elencare le assegnazioni di ruolo di Azure usando il portale di Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando il portale di Azure.

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Elencare le assegnazioni di ruolo per un utente o un gruppo

Il modo più semplice per visualizzare i ruoli assegnati a un utente o a un gruppo in una sottoscrizione consiste nell'usare il riquadro **risorse di Azure** .

1. Nella portale di Azure selezionare tutti i **Servizi** dal menu portale di Azure.

1. Selezionare **Azure Active Directory** , quindi selezionare **utenti** o **gruppi**.

1. Fare clic sull'utente o sul gruppo per il quale si desidera elencare le assegnazioni di ruolo.

1. Fare clic su **Risorse di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'utente o al gruppo selezionato in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazione dei ruoli a un utente](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizioni** .

## <a name="list-owners-of-a-subscription"></a>Elenca i proprietari di una sottoscrizione

Gli utenti a cui è stato assegnato il ruolo di [proprietario](built-in-roles.md#owner) per una sottoscrizione possono gestire tutti gli elementi nella sottoscrizione. Per elencare i proprietari di una sottoscrizione, seguire questa procedura.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione di cui si desidera elencare i proprietari.

1. Fare clic su **controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Scorrere fino alla sezione **owners (proprietari** ) per visualizzare tutti gli utenti a cui è stato assegnato il ruolo di proprietario per questa sottoscrizione.

   ![Controllo di accesso alla sottoscrizione-scheda assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Elencare le assegnazioni di ruolo in un ambito

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Elencare le assegnazioni di ruolo per un utente in un ambito

Per elencare l'accesso per un utente, un gruppo, un'entità servizio o un'identità gestita, è possibile elencare le assegnazioni di ruolo. Attenersi alla procedura seguente per elencare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita in un determinato ambito.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-list-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-list-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita assegnata dal sistema

1. Nella portale di Azure aprire un'identità gestita assegnata dal sistema.

1. Nel menu a sinistra fare clic su **Identity**.

    ![Identità gestita assegnata dal sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. In **assegnazioni di ruolo**fare clic su **Mostra i ruoli RBAC di Azure assegnati a questa identità gestita**.

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dal sistema selezionata in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita assegnata dall'utente

1. Nella portale di Azure aprire un'identità gestita assegnata dall'utente.

1. Fare clic su **Risorse di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dall'utente selezionata in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizioni** .

## <a name="list-number-of-role-assignments"></a>Elencare il numero di assegnazioni di ruolo

È possibile avere fino a **2000** assegnazioni di ruolo in ogni sottoscrizione. Per tenere traccia di questo limite, nella scheda **assegnazioni di ruolo** è incluso un grafico che elenca il numero di assegnazioni di ruolo per la sottoscrizione corrente.

![Controllo di accesso-numero di assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se ci si avvicina al numero massimo e si tenta di aggiungere altre assegnazioni di ruolo, verrà visualizzato un avviso nel riquadro **Aggiungi assegnazione ruolo** . Per i modi in cui è possibile ridurre il numero di assegnazioni di ruolo, vedere [risolvere i problemi relativi a RBAC di Azure](troubleshooting.md#azure-role-assignments-limit).

![Controllo di accesso-avviso di aggiunta di assegnazione di ruolo](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
