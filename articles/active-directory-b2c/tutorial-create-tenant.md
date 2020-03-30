---
title: Esercitazione - Creare un tenant B2C di Azure Active DirectoryTutorial - Create an Azure Active Directory B2C tenant
description: Informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186404"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Esercitazione: Creare un tenant di Azure Active Directory B2C

Prima che le applicazioni possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passare alla directory contenente il tenant B2C di Azure AD
> * Aggiungere la risorsa B2C di Azure AD come preferito nel portale di AzureAdd the Azure AD B2C resource as a **Favorite** in the Azure portal

La registrazione di un'applicazione verrà illustrata nell'esercitazione successiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

1. Accedere al [portale](https://portal.azure.com/)di Azure . Accedere con un account di Azure a cui è stato assegnato almeno il ruolo [Collaboratore](../role-based-access-control/built-in-roles.md) all'interno della sottoscrizione o un gruppo di risorse all'interno della sottoscrizione.

1. Selezionare la directory che contiene la sottoscrizione.

    Nella barra degli strumenti del portale di Azure selezionare l'icona **Directory e sottoscrizione** e quindi selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quella che conterrà il tenant B2C di Azure AD.

    ![Tenant di sottoscrizione, directory : filtro di sottoscrizione con tenant sottoscrizione selezionato](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Cercare **Azure Active Directory B2C**e quindi selezionare **Crea**.
1. Selezionare **Crea un nuovo tenant Azure AD B2C**.

    ![Creare un nuovo tenant B2C di Azure AD selezionato nel portale di AzureCreate a new Azure AD B2C tenant selected in Azure portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Immettere **un Nome organizzazione** e Nome di dominio **iniziale**. Selezionare il **paese (non** può essere modificato in un secondo momento) e quindi selezionare **Crea**.

    Il nome di dominio viene utilizzato come parte del nome di dominio tenant completo. In questo esempio, il nome del tenant è *contosob2c.onmicrosoft.com:*

    ![Creare un modulo tenant con valori di esempio nel portale di AzureCreate tenant form in with example values in Azure portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Al termine della creazione del tenant, selezionare il collegamento **Crea nuovo tenant B2C o Collega a tenant esistente** nella parte superiore della pagina di creazione del tenant.

    ![Collegamento breadcrumb del tenant evidenziato nel portale di Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selezionare **Collega un tenant di Azure AD B2C esistente alla sottoscrizione**di Azure.

   ![Collegare una selezione di sottoscrizione esistente nel portale di AzureLink an existing subscription selection in Azure portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selezionare il **tenant B2C** di Azure AD creato, quindi selezionare la **sottoscrizione**.

    Per **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un **Nome** per il gruppo di risorse che conterrà il tenant, selezionare il **percorso del gruppo**di risorse e quindi **selezionare Crea**.

    ![Modulo delle impostazioni di sottoscrizione del collegamento nel portale di AzureLink subscription settings form in Azure portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    È possibile collegare più tenant di Azure AD B2C a una singola sottoscrizione di Azure a scopo di fatturazione.

## <a name="select-your-b2c-tenant-directory"></a>Selezionare la directory tenant B2C

Per iniziare a usare il nuovo tenant B2C di Azure AD, è necessario passare alla directory che contiene il tenant.

Selezionare il filtro **Directory e sottoscrizione** nel menu superiore del portale di Azure, quindi selezionare la directory che contiene il tenant B2C di Azure AD.

Se all'inizio non vedi il tuo nuovo tenant di Azure B2C nell'elenco, aggiorna la finestra del browser, quindi seleziona di nuovo il filtro **Directory e sottoscrizione** nel menu in alto.

![Directory contenitore tenant B2C selezionata nel portale di AzureB2C tenant-containing directory selected in Azure portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Aggiungere Azure AD B2C come preferito (facoltativo)Add Azure AD B2C as a favorite (optional)

Questo passaggio facoltativo semplifica la selezione del tenant B2C di Azure AD nelle esercitazioni seguenti e in tutte le esercitazioni successive.

Anziché cercare *Azure AD B2C* in **Tutti i servizi** ogni volta che si vuole usare il tenant, è possibile aggiungere la risorsa ai preferiti. Quindi, è possibile selezionarlo dalla sezione **Preferiti** del menu del portale per passare rapidamente al tenant B2C di Azure AD.

È necessario eseguire questa operazione una sola volta. Prima di eseguire questi passaggi, assicurarsi di essere passati alla directory contenente il tenant B2C di Azure AD come descritto nella sezione [precedente, Selezionare la directory tenant B2C](#select-your-b2c-tenant-directory).

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nel menu del portale di Azure selezionare **Tutti i servizi**.
1. Nella casella di ricerca **Tutti i servizi** cercare Azure AD **B2C**, passare il puntatore del mouse sul risultato della ricerca e quindi selezionare l'icona a forma di stella nella descrizione comando. **Azure AD B2C** viene ora visualizzato nel portale di Azure in **Preferiti**.
1. Se si vuole modificare la posizione del nuovo preferito, passare al menu del portale di Azure, selezionare **Azure AD B2C**, quindi trascinarlo verso l'alto o verso il basso nella posizione desiderata.

    ![Azure AD B2C, menu Preferiti, portale di Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passare alla directory contenente il tenant B2C di Azure AD
> * Aggiungere la risorsa B2C di Azure AD come preferito nel portale di AzureAdd the Azure AD B2C resource as a **Favorite** in the Azure portal

Successivamente, informazioni su come registrare un'applicazione Web nel nuovo tenant.

> [!div class="nextstepaction"]
> [Registrare le applicazioni >](tutorial-register-applications.md)
