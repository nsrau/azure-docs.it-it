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
ms.openlocfilehash: 8dfc3714362b082168c32ba73e234e9b5fb43525
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87545178"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Concedere l'accesso basato sui ruoli per gli utenti ad Azure Maps

Per concedere il *controllo degli accessi in base al ruolo* , è possibile assegnare un gruppo Azure ad o entità di sicurezza a una o più definizioni di ruolo di controllo di accesso di Azure maps. Per visualizzare le definizioni dei ruoli di Azure disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **Roles (ruoli**) e quindi cercare i ruoli che iniziano con *Maps di Azure*.

* Per gestire in modo efficiente una grande quantità di accesso degli utenti a mappe di Azure, vedere [Azure ad groups](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* Per consentire agli utenti di eseguire l'autenticazione all'applicazione, è necessario creare gli utenti in Azure AD. Vedere [aggiungere o eliminare utenti usando Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

Altre informazioni su [Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/) per gestire efficacemente una directory per gli utenti.

1. Passare all' **account Azure Maps**. Selezionare l'assegnazione di ruolo **controllo di accesso (IAM)**  >  **Role assignment**.

    ![Concedere il controllo degli accessi in base al ruolo](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella scheda **assegnazioni di ruolo** , in **ruolo**, selezionare una definizione di ruolo predefinita di Azure Maps, ad esempio **lettore dati** di Azure Maps o **collaboratore dati di Azure Maps**. In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. Selezionare l'entità in base al nome. Selezionare **Salva**.

   * Vedere informazioni dettagliate su come [aggiungere o rimuovere assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Le definizioni di ruolo predefinite di mappe di Azure forniscono un accesso di autorizzazione molto grande a molte API REST di Azure maps. Per limitare al minimo le API per gli utenti, vedere [creare una definizione di ruolo personalizzata e assegnare utenti](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) alla definizione di ruolo personalizzata. Ciò consentirà agli utenti di disporre dei privilegi minimi necessari per l'applicazione.