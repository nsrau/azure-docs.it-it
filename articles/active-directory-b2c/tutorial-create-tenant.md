---
title: Esercitazione - Creare un tenant di Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e2568bca8f8ecf170c82c5388823193b8b0457cf
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734457"
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
2. Assicurarsi di usare la directory che contiene la sottoscrizione facendo clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant. Questa directory è diversa da quello che conterrà il tenant di Azure AD B2C.

    ![Passare alla directory della sottoscrizione](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
4. Cercare e selezionare **Active Directory B2C** e quindi fare clic su **Crea**.
5. Scegli **creare un nuovo Tenant di Azure AD B2C**, immettere un nome dell'organizzazione e il nome di dominio iniziale, che viene usato nel nome del tenant, selezionare il paese/area geografica (non può essere modificato in un secondo momento) e quindi fare clic su **crea** .

    ![Creare un tenant](./media/tutorial-create-tenant/create-tenant.png)

    In questo esempio il nome del tenant è contoso0926Tenant.onmicrosoft.com

6. Nella pagina **Creare un nuovo tenant B2C o collegarne uno esistente**, selezionare **Collega un tenant Azure AD B2C esistente alla sottoscrizione di Azure**, selezionare il tenant creato, selezionare la sottoscrizione e fare clic su **Crea nuovo**.
7. Immettere un nome per il gruppo di risorse che conterrà il tenant, selezionare la località e quindi fare clic su **Crea**.
8. Per iniziare a usare il nuovo tenant, assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che lo contiene.

    ![Passare alla directory del tenant](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

> [!div class="nextstepaction"]
> [Registrare le applicazioni](tutorial-register-applications.md)
