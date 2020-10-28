---
title: Concedere l'accesso in base al ruolo per gli utenti
titleSuffix: Azure Maps
description: Usare il controllo degli accessi in base al ruolo per concedere agli utenti l'autorizzazione per le mappe di Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895569"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Concedere l'accesso basato sui ruoli per gli utenti ad Azure Maps

Si concede il *controllo degli accessi in base al ruolo di Azure (RBAC di Azure)* assegnando un gruppo Azure ad o entità di sicurezza a una o più definizioni di ruolo di Azure maps. Per visualizzare le definizioni dei ruoli di Azure disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)** . Selezionare **Roles (ruoli** ) e quindi cercare i ruoli che iniziano con *Maps di Azure* .

* Per gestire in modo efficiente una grande quantità di accesso degli utenti a mappe di Azure, vedere [Azure ad groups](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Per consentire agli utenti di eseguire l'autenticazione all'applicazione, è necessario creare gli utenti in Azure AD. Vedere [aggiungere o eliminare utenti usando Azure ad](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Altre informazioni su [Azure ad](../../active-directory/fundamentals/index.yml) per gestire efficacemente una directory per gli utenti.

1. Passare all' **account Azure Maps** . Selezionare l'assegnazione di ruolo **controllo di accesso (IAM)**  >  **Role assignment** .

    ![Concedi l'accesso con RBAC di Azure](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella scheda **assegnazioni di ruolo** , in **ruolo** , selezionare una definizione di ruolo predefinita di Azure Maps, ad esempio **lettore dati** di Azure Maps o **collaboratore dati di Azure Maps** . In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD** . Selezionare l'entità in base al nome. Selezionare quindi **Salva** .

   * Vedere informazioni dettagliate su come [aggiungere o rimuovere assegnazioni di ruolo](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Le definizioni di ruolo predefinite di mappe di Azure forniscono un accesso di autorizzazione molto grande a molte API REST di Azure maps. Per limitare al minimo le API per gli utenti, vedere [creare una definizione di ruolo personalizzata e assegnare utenti](../../role-based-access-control/custom-roles.md) alla definizione di ruolo personalizzata. Ciò consentirà agli utenti di disporre dei privilegi minimi necessari per l'applicazione.