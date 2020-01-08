---
title: 'Esercitazione: creare un tenant di Azure Active Directory B2C'
description: Informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b7e872f3320a2c72b6e9fb3ffba65510b205ca03
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425569"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Esercitazione: Creare un tenant di Azure Active Directory B2C

Prima che le applicazioni possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito dall'utente.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passa alla directory contenente il tenant di Azure AD B2C
> * Aggiungere la risorsa Azure AD B2C come **preferito** nell'portale di Azure

La registrazione di un'applicazione verrà illustrata nell'esercitazione successiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

1. Accedere al [portale di Azure](https://portal.azure.com/). Accedere con un account Azure a cui è stato assegnato almeno il ruolo [collaboratore](../role-based-access-control/built-in-roles.md) all'interno della sottoscrizione o un gruppo di risorse all'interno della sottoscrizione.

1. Selezionare la directory che contiene la sottoscrizione.

    Nella barra degli strumenti portale di Azure selezionare l'icona **directory + sottoscrizione** e quindi selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quella che conterrà il tenant del Azure AD B2C.

    ![Tenant sottoscrizione, filtro directory + sottoscrizione con tenant di sottoscrizione selezionato](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Cercare **Azure Active Directory B2C**, quindi selezionare **Crea**.
1. Selezionare **Crea un nuovo tenant Azure AD B2C**.

    ![Crea un nuovo tenant Azure AD B2C selezionato in portale di Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Immettere il **nome dell'organizzazione** e il **nome di dominio iniziale**. Selezionare il **paese o l'area geografica** (non può essere modificato in seguito) e quindi selezionare **Crea**.

    Il nome di dominio viene utilizzato come parte del nome di dominio completo del tenant. In questo esempio il nome del tenant è *contosob2c.onmicrosoft.com*:

    ![Creare un modulo tenant con valori di esempio in portale di Azure](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Al termine della creazione del tenant, selezionare il collegamento **Crea nuovo tenant B2C o collega a tenant esistente** nella parte superiore della pagina di creazione del tenant.

    ![Collegare il collegamento di navigazione del tenant evidenziato in portale di Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selezionare **collega un tenant di Azure ad B2C esistente alla sottoscrizione di Azure**.

   ![Collega una selezione della sottoscrizione esistente in portale di Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selezionare il **Tenant Azure ad B2C** creato, quindi selezionare la **sottoscrizione**.

    Per **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un **nome** per il gruppo di risorse che conterrà il tenant, selezionare il **percorso del gruppo di risorse**e quindi selezionare **Crea**.

    ![Modulo delle impostazioni di sottoscrizione collegamento in portale di Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>Selezionare la directory del tenant B2C

Per iniziare a usare il nuovo tenant di Azure AD B2C, è necessario passare alla directory che contiene il tenant.

Selezionare il filtro **directory + sottoscrizione** nel menu in alto della portale di Azure, quindi selezionare la directory che contiene il tenant del Azure ad B2C.

Se inizialmente non viene visualizzato il nuovo tenant di Azure B2C nell'elenco, aggiornare la finestra del browser, quindi selezionare di nuovo il filtro **directory + sottoscrizione** nel menu in alto.

![Directory con tenant B2C selezionato in portale di Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Aggiungi Azure AD B2C come preferito (facoltativo)

Questo passaggio facoltativo rende più semplice selezionare il tenant Azure AD B2C nelle esercitazioni seguenti e in tutte le esercitazioni successive.

Anziché cercare *Azure ad B2C* in **tutti i servizi** ogni volta che si desidera lavorare con il tenant, è invece possibile aggiungere la risorsa a Preferiti. Quindi, è possibile selezionarlo dalla sezione **Preferiti** del menu del portale per passare rapidamente al tenant Azure ad B2C.

È sufficiente eseguire questa operazione una sola volta. Prima di eseguire questi passaggi, assicurarsi di passare alla directory contenente il tenant Azure AD B2C come descritto nella sezione precedente, [selezionare la directory del tenant B2C](#select-your-b2c-tenant-directory).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Scegliere **tutti i servizi**dal menu portale di Azure.
1. Nella casella di ricerca **tutti i servizi** cercare **Azure ad B2C**, passare il puntatore sul risultato della ricerca e quindi selezionare l'icona a stella nella descrizione comando. **Azure ad B2C** ora viene visualizzato nella portale di Azure in **Preferiti**.
1. Se si desidera modificare la posizione del nuovo preferito, scegliere **Azure ad B2C**dal menu portale di Azure, quindi trascinarlo verso l'alto o verso il basso nella posizione desiderata.

    ![Azure AD B2C, menu Preferiti portale di Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passa alla directory contenente il tenant di Azure AD B2C
> * Aggiungere la risorsa Azure AD B2C come **preferito** nell'portale di Azure

Successivamente, informazioni su come registrare un'applicazione Web nel nuovo tenant.

> [!div class="nextstepaction"]
> [Registrare le applicazioni >](tutorial-register-applications.md)
