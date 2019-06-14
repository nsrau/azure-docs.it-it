---
title: Esercitazione - creare un tenant di Azure Active Directory B2C
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
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056311"
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
2. Assicurarsi che si usa la directory che contiene la sottoscrizione. Scegliere il **filtro di Directory e sottoscrizione** nel menu in alto, quindi selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quello che conterrà il tenant di Azure AD B2C.

    ![Passare alla directory della sottoscrizione](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
4. Cercare e selezionare **Active Directory B2C** e quindi fare clic su **Crea**.
5. Scegli **creare un nuovo Tenant di Azure AD B2C** e immettere un nome dell'organizzazione e un nome di dominio iniziale. Selezionare il paese/area geografica (non può essere modificato in un secondo momento) e quindi fare clic su **Create**.

    Il nome di dominio iniziale viene utilizzato come parte del nome del tenant. In questo esempio, è il nome del tenant *contoso0926Tenant.onmicrosoft.com*:

    ![Creare un tenant](./media/tutorial-create-tenant/create-tenant.PNG)

6. Nel **Crea nuovo Tenant di B2C o collegarne uno esistente** pagina, scegliere **collega un esistente AD B2C Azure Tenant alla sottoscrizione di Azure**.

    Selezionare il tenant di cui è stato creato e selezionare la sottoscrizione.

    Per il gruppo di risorse, selezionare **Crea nuovo**. Immettere un nome per il gruppo di risorse che conterrà il tenant, selezionare la località e quindi fare clic su **Crea**.
1. Per iniziare a usare il nuovo tenant, verificare che si usa la directory che contiene il tenant di Azure AD B2C scegliendo il **filtro per Directory e sottoscrizione** nel menu in alto e scegliendo la directory che lo contiene.

    Se inizialmente non viene visualizzato il nuovo tenant di B2C di Azure nell'elenco, aggiornare la finestra del browser, quindi selezionare il **Directory e sottoscrizione filtro** nuovamente nel menu in alto.

    ![Passare alla directory del tenant](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

Successivamente, informazioni su come registrare un'applicazione web nel tenant di nuovo.

> [!div class="nextstepaction"]
> [Registrare le applicazioni >](tutorial-register-applications.md)
