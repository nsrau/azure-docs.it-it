---
title: 'Esercitazione: creare un tenant di Azure Active Directory B2C'
description: Informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ce389d1f434fb0eb37413873b02e3ddfff8f7fba
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849403"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Esercitazione: Creare un tenant di Azure Active Directory B2C

Prima che le applicazioni possano interagire con Azure Active Directory (Azure AD) B2C, devono essere registrate in un tenant gestito.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

La registrazione di un'applicazione verrà illustrata nell'esercitazione successiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene la sottoscrizione. Fare clic sul **filtro directory e sottoscrizione** nel menu in alto, quindi selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quella che conterrà il tenant del Azure AD B2C.

    ![Filtro di directory e sottoscrizione con il tenant di sottoscrizione selezionato](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
4. Cercare e selezionare **Active Directory B2C** e quindi fare clic su **Crea**.
5. Scegliere **Crea un nuovo Tenant Azure ad B2C** e immettere il nome dell'organizzazione e il nome di dominio iniziale. Selezionare il paese/area geografica (non può essere modificato in seguito) e quindi fare clic su **Crea**.

    Il nome di dominio iniziale viene usato come parte del nome del tenant. In questo esempio il nome del tenant è *contoso0926Tenant.onmicrosoft.com*:

    ![Pagina di creazione tenant B2C nel portale di Azure](./media/tutorial-create-tenant/create-tenant.PNG)

6. Nella pagina **Crea nuovo tenant B2C o collegamento a tenant esistente** scegliere **collega un tenant di Azure ad B2C esistente alla sottoscrizione di Azure**.

    Selezionare il tenant creato e selezionare la sottoscrizione.

    Per gruppo di risorse selezionare **Crea nuovo**. Immettere un nome per il gruppo di risorse che conterrà il tenant, selezionare la località e quindi fare clic su **Crea**.
1. Per iniziare a usare il nuovo tenant, assicurarsi di usare la directory che contiene il tenant di Azure AD B2C facendo clic sul **filtro directory e sottoscrizione** nel menu in alto e scegliendo la directory che lo contiene.

    Se inizialmente non viene visualizzato il nuovo tenant di Azure B2C nell'elenco, aggiornare la finestra del browser, quindi selezionare di nuovo il **filtro directory e sottoscrizione** nel menu in alto.

    ![Filtro di directory e sottoscrizione con tenant B2C selezionato](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

Successivamente, informazioni su come registrare un'applicazione Web nel nuovo tenant.

> [!div class="nextstepaction"]
> [Registrare le applicazioni >](tutorial-register-applications.md)
