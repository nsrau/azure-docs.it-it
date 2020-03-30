---
title: Elencare le assegnazioni di ruolo usando controllo degli accessi in base al ruolo di Azure e il portale di AzureList role assignments using Azure RBAC and the Azure portal
description: Informazioni su come determinare a quali risorse gli utenti, i gruppi, le entità servizio o le identità gestite hanno accesso all'uso del controllo degli accessi in base al ruolo di Azure e del portale di Azure.Learn how to determine what resources users, groups, service principals, or managed identities have access to using Azure role-based access control (RBAC) and the Azure portal.
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
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062233"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Elencare le assegnazioni di ruolo usando controllo degli accessi in base al ruolo di Azure e il portale di AzureList role assignments using Azure RBAC and the Azure portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando il portale di Azure.This article describes how to list role assignments using the Azure portal.

> [!NOTE]
> Se l'organizzazione ha esternalizzato le funzioni di gestione a un provider di servizi che utilizza la gestione delle risorse delegate di [Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)le assegnazioni di ruolo autorizzate da tale provider di servizi non verranno visualizzate qui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Elencare le assegnazioni di ruolo per un utente o un gruppoList role assignments for a user or group

Il modo più semplice per visualizzare i ruoli assegnati a un utente o a un gruppo in una sottoscrizione consiste nell'usare il riquadro Risorse di **Azure.The** easiest way to see the roles assigned to a user or group in a subscription is to use the Azure resources pane.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi selezionare **Utenti** o **Gruppi**.

1. Fare clic sull'utente o sul gruppo per cui si desidera elencare le assegnazioni di ruolo.

1. Fare clic su **Risorse di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'utente o al gruppo selezionato in vari ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo che si è autorizzati a leggere.

    ![Assegnazione dei ruoli a un utente](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Per modificare la sottoscrizione, fare clic sull'elenco **Sottoscrizioni.**

## <a name="list-owners-of-a-subscription"></a>Elencare i proprietari di un abbonamento

Gli utenti a cui è stato assegnato il ruolo [Proprietario](built-in-roles.md#owner) per una sottoscrizione possono gestire tutti gli elementi della sottoscrizione. Seguire questi passaggi per elencare i proprietari di una sottoscrizione.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sull'abbonamento di cui si vuole elencare i proprietari.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Scorrere fino alla sezione **Proprietari** per visualizzare tutti gli utenti a cui è stato assegnato il ruolo Proprietario per la sottoscrizione.

   ![Controllo di accesso alle sottoscrizioni - Scheda Assegnazioni di ruoloSubscription Access control - Role assignments tab](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Elencare le assegnazioni di ruolo in un ambitoList role assignments at a scope

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Elencare le assegnazioni di ruolo per un utente in un ambitoList role assignments for a user at a scope

Per elencare l'accesso per un utente, un gruppo, un'entità servizio o un'identità gestita, elencare le relative assegnazioni di ruolo. Seguire questi passaggi per elencare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita in un ambito specifico.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-list-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-list-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita assegnata dal sistemaList role assignments for a system-assigned managed identity

1. Nel portale di Azure aprire un'identità gestita assegnata dal sistema.

1. Nel menu a sinistra fare clic su **Identità**.

    ![Identità gestita assegnata dal sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. In **Assegnazioni di**ruolo fare clic su **Mostra i ruoli RBAC di Azure assegnati a questa identità gestita.**

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dal sistema selezionata in vari ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo che si è autorizzati a leggere.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistemaRole assignments for a system-assigned managed identity](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita assegnata dall'utenteList role assignments for a user-assigned managed identity

1. Nel portale di Azure aprire un'identità gestita assegnata dall'utente.

1. Fare clic su **Risorse di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dall'utente selezionata in vari ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo che si è autorizzati a leggere.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistemaRole assignments for a system-assigned managed identity](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Per modificare la sottoscrizione, fare clic sull'elenco **Sottoscrizioni.**

## <a name="list-number-of-role-assignments"></a>Numero di assegnazioni di ruolo elenco

È possibile avere fino a **2000** assegnazioni di ruolo in ogni sottoscrizione. Per tenere traccia di questo limite, la scheda **Assegnazioni** di ruolo include un grafico che elenca il numero di assegnazioni di ruolo per la sottoscrizione corrente.

![Grafico Controllo di accesso - Numero di assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se ci si avvicina al numero massimo e si tenta di aggiungere altre assegnazioni di ruolo, verrà visualizzato un avviso nel riquadro **Aggiungi assegnazione ruolo.** Per informazioni su come ridurre il numero di assegnazioni di ruolo, vedere Risolvere i problemi relativi al controllo [degli accessi in](troubleshooting.md#azure-role-assignments-limit)base al ruolo di Azure.

![Controllo di accesso - Avviso di aggiunta dell'assegnazione di ruoloAccess control - Add role assignment warning](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo tramite controllo degli accessi in base al ruolo di Azure e il portale di AzureAdd or remove role assignments using Azure RBAC and the Azure portal](role-assignments-portal.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
