---
title: Esercitazione - Creare un tenant di Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come preparare la registrazione delle applicazioni creando un tenant di Azure Active Directory B2C con il portale di Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236894"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Esercitazione: Creare un tenant di Azure Active Directory B2C

Prima che le applicazioni possano interagire con Azure Active Directory (Azure AD) B2C, devono essere registrate in un tenant gestito.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

La registrazione di un'applicazione verrà illustrata nell'esercitazione successiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace cercare e selezionare **Active Directory B2C** e quindi fare clic su **Crea**.
3. Scegliere **Crea un nuovo tenant Azure AD B2C**, immettere un nome di organizzazione e il nome di dominio iniziale, che viene usato nel nome del tenant, selezionare il paese e quindi fare clic su **Crea**. Verificare il paese del tenant perché non potrà essere modificato in seguito.

    ![Creare un tenant](./media/tutorial-create-tenant/create-tenant.png)

    In questo esempio il nome del tenant è contoso0522Tenant.onmicrosoft.com

Per iniziare a gestire il nuovo tenant, fare clic sulla parola **qui** in **Fare clic su qui per gestire la nuova directory**. Verrà visualizzato un messaggio **Risoluzione dei problemi** che informa che è necessario collegare la sottoscrizione al nuovo tenant. 

## <a name="link-your-tenant-to-your-subscription"></a>Collegare il tenant alla sottoscrizione

È necessario collegare il tenant di Azure AD B2C alla sottoscrizione di Azure per abilitare tutte le funzionalità e pagare i costi di utilizzo. Se non si collega il tenant alla sottoscrizione, le applicazioni non funzioneranno correttamente.

1. Assicurarsi di usare la directory contenente la sottoscrizione che si vuole associare al nuovo tenant passando alla directory nell'angolo in alto a destra del portale di Azure.

    ![Cambiare directory](./media/tutorial-create-tenant/switch-directories.png)

    Selezionare quindi la directory contenente la sottoscrizione.

    ![Selezionare la directory](./media/tutorial-create-tenant/select-directory.png)

2. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
3. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace cercare e selezionare **Active Directory B2C** e quindi fare clic su **Crea**.
4. Scegliere **Collega un tenant Azure AD B2C esistente alla sottoscrizione di Azure**, selezionare il tenant creato, selezionare la sottoscrizione, immettere *myContosoTenantRG* come nome del gruppo di risorse, accettare la posizione e quindi fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un tenant di Azure AD B2C
> * Collegare il tenant alla sottoscrizione

> [!div class="nextstepaction"]
> [Abilitare un'applicazione Web per l'autenticazione mediante account](active-directory-b2c-tutorials-web-app.md)