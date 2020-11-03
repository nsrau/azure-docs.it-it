---
title: 'Esercitazione: Creare un tenant di Azure Active Directory B2C'
description: Seguire questa esercitazione per informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dce41f979a46ae2bda568b5db79f0e0304705dd8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670161"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Esercitazione: Creare un tenant di Azure Active Directory B2C

Prima che le applicazioni possano interagire con Azure Active Directory B2C (Azure AD B2C), devono essere registrate in un tenant gestito.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passare alla directory contenente il tenant di Azure AD B2C
> * Aggiungere la risorsa Azure AD B2C ai **Preferiti** nel portale di Azure

La registrazione di un'applicazione verrà illustrata nell'esercitazione successiva.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

1. Accedere al [portale di Azure](https://portal.azure.com/). Accedere con un account Azure a cui è stato assegnato almeno il ruolo [Collaboratore](../role-based-access-control/built-in-roles.md) nella sottoscrizione o in un gruppo di risorse nella sottoscrizione.

1. Selezionare la directory contenente la sottoscrizione.

    Nella barra degli strumenti del portale di Azure selezionare l'icona **Directory e sottoscrizione** e quindi selezionare la directory contenente la sottoscrizione. Si tratta di una directory diversa rispetto a quella che conterrà il tenant di Azure AD B2C.

    ![Tenant della sottoscrizione, filtro Directory e sottoscrizione con il tenant della sottoscrizione selezionato](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Cercare **Azure Active Directory B2C** e quindi fare clic su **Crea**.
1. Selezionare **Crea un nuovo tenant Azure AD B2C**.

    ![Opzione Crea un nuovo tenant di Azure AD B2C selezionata nel portale di Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)
    
1. Immettere i valori per **Nome organizzazione** e **Nome di dominio iniziale**. Selezionare il **paese o l'area geografica** (non si può cambiare in seguito) e quindi selezionare **Crea**.

    Il nome di dominio viene usato come parte del nome di dominio del tenant. In questo esempio il nome del tenant è *contosob2c.onmicrosoft.com* :

1. Una volta completata la creazione del tenant, selezionare il collegamento **Creare un nuovo tenant B2C o collegarne uno esistente** nella parte superiore della pagina.

    ![Collegamento per creare o collegare un tenant evidenziato nel portale di Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selezionare **Collegare un tenant di Azure AD B2C esistente alla sottoscrizione di Azure**. Per eseguire questo passaggio, è necessario aver eseguito l'accesso con il ruolo di proprietario.

   ![Selezione del collegamento di una sottoscrizione esistente nel portale di Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selezionare il **tenant di Azure AD B2C** creato, quindi selezionare la **sottoscrizione**.

    Per **Gruppo di risorse** selezionare **Crea nuovo**. Immettere un valore in **Nome** per il gruppo di risorse che conterrà il tenant, effettuare una selezione in **Località del gruppo di risorse** e quindi selezionare **Crea**.

    ![Modulo di impostazioni per collegare la sottoscrizione nel portale di Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)
    

È possibile collegare più tenant di Azure AD B2C a una singola sottoscrizione di Azure ai fini della fatturazione. Per collegare un tenant, è necessario essere un amministratore nel tenant di Azure AD B2C a cui è stato assegnato almeno un ruolo di Collaboratore nella sottoscrizione di Azure. Vedere [Collegare un tenant di Azure AD B2C a una sottoscrizione](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Selezionare la directory del tenant B2C

Per iniziare a usare il nuovo tenant di Azure AD B2C, è necessario passare alla directory contenente il tenant.

Selezionare il filtro **Directory e sottoscrizione** nel menu del portale di Azure in alto e quindi selezionare la directory contenente il tenant di Azure AD B2C.

Se inizialmente il nuovo tenant Azure B2C non è visualizzato nell'elenco, aggiornare la finestra del browser, quindi selezionare di nuovo il filtro **Directory e sottoscrizione** nel menu in alto.

![Directory contenente il tenant B2C selezionata nel portale di Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Aggiungere Azure AD B2C a Preferiti (facoltativo)

Questo passaggio facoltativo semplifica la selezione del tenant Azure AD B2C nell'esercitazione seguente e in tutte le esercitazioni successive.

Anziché cercare *Azure AD B2C* in **Tutti i servizi** ogni volta che si vuole usare il tenant, è possibile invece aggiungere la risorsa a Preferiti. Sarà quindi possibile selezionarla dalla sezione **Preferiti** del menu del portale per passare rapidamente al tenant Azure AD B2C.

È necessario eseguire questa operazione solo una volta. Prima di eseguire la procedura, assicurarsi di passare alla directory contenente il tenant Azure AD B2C come descritto nella sezione precedente [Selezionare la directory del tenant B2C](#select-your-b2c-tenant-directory).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu del portale di Azure selezionare **Tutti i servizi**.
1. Nella casella di ricerca **Tutti i servizi** cercare **Azure AD B2C** , passare il puntatore del mouse sul risultato della ricerca e quindi selezionare l'icona a forma di stella nella descrizione comando. A questo punto, **Azure AD B2C** verrà visualizzato nella sezione **Preferiti** del portale di Azure.
1. Per modificare la posizione del nuovo preferito, passare al menu del portale di Azure, selezionare **Azure AD B2C** , quindi trascinarlo verso l'alto o verso il basso nella posizione desiderata.

    ![Azure AD B2C, menu Preferiti, portale di Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione
> * Passare alla directory contenente il tenant di Azure AD B2C
> * Aggiungere la risorsa Azure AD B2C ai **Preferiti** nel portale di Azure

Successivamente, si procederà a registrare un'applicazione Web nel nuovo tenant.

> [!div class="nextstepaction"]
> [Registrare le applicazioni >](tutorial-register-applications.md)
