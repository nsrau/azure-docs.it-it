---
title: "Avvio rapido: Assegnare utenti a un'app che usa Azure Active Directory come provider di identità"
description: Questo argomento di avvio rapido illustra come consentire agli utenti di usare un'app configurata per l'uso di Azure AD come provider di identità.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 9d396c0787798c783b44748dc2461c073334cc7e
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322501"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Avvio rapido: Assegnare utenti a un'app che usa Azure AD come provider di identità

Nell'argomento di avvio rapido precedente sono state configurate le proprietà di un'app. Quando si impostano le proprietà, si configura l'esperienza per gli utenti assegnati e per quelli non assegnati. Questo argomento di avvio rapido illustra il processo di assegnazione di utenti all'app.

## <a name="prerequisites"></a>Prerequisiti

Per assegnare utenti a un'app aggiunta al tenant di Azure AD, è necessario avere:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).
- Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.

## <a name="assign-users-to-an-app"></a>Assegnare gli utenti a un'app
1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Trovare e selezionare l'applicazione da configurare.
2. Nel menu di spostamento a sinistra selezionare **Utenti e gruppi**.
   > [!NOTE]
   > Alcune app di Microsoft 365 e Office 365 richiedono l'uso di PowerShell. 
3. Selezionare il pulsante **Aggiungi utente**.
4. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.
5. Selezionare l'utente o il gruppo da assegnare all'applicazione. È anche possibile iniziare a digitare il nome dell'utente o del gruppo nella casella di ricerca. È possibile scegliere più utenti e gruppi e le selezioni verranno visualizzate in **Elementi selezionati**.
    > [!IMPORTANT]
    > Quando si assegna un gruppo a un'applicazione, solo gli utenti del gruppo avranno accesso. L'assegnazione non si estende ai gruppi annidati.

    > [!NOTE]
    > L'assegnazione basata su gruppo richiede Azure Active Directory Premium, edizione P1 o P2. L'assegnazione basata su gruppo è supportata solo per i gruppi di sicurezza. Le appartenenze a gruppi annidati e i gruppi di Office 365 non sono attualmente supportati. Per altri requisiti di licenza per le funzionalità descritte in questo articolo, vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Al termine, scegliere **Seleziona**.
   ![Assegnare un utente o gruppo all'app](./media/assign-user-or-group-access-portal/assign-users.png)
7. Nel riquadro **Utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco e fare clic sul pulsante **Seleziona** nella parte inferiore del riquadro.
8. Se l'applicazione lo supporta, è possibile assegnare un ruolo all'utente o al gruppo. Nel riquadro **Aggiungi assegnazione** selezionare **Ruolo**. Nel riquadro **Seleziona ruolo** scegliere un ruolo da applicare agli utenti o ai gruppi selezionati, quindi fare clic su **OK** nella parte inferiore del riquadro. 
    > [!NOTE]
    > Se l'applicazione non supporta la selezione dei ruoli, verrà assegnato il ruolo di accesso predefinito. In questo caso, l'applicazione gestisce il livello di accesso degli utenti.
9. Nella parte inferiore del riquadro **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

> [!NOTE]
> È possibile annullare l'assegnazione di utenti o gruppi seguendo la stessa procedura. Selezionare l'utente o il gruppo di cui annullare l'assegnazione e quindi selezionare **Rimuovi**. Alcune app di Microsoft 365 e Office 365 richiedono l'uso di PowerShell. 

## <a name="clean-up-resources"></a>Pulire le risorse

Una volta completato l'argomento di avvio rapido, è consigliabile eliminare l'app. In questo modo è possibile mantenere pulito il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'accesso Single Sign-On per un'app, passare all'articolo successivo.
> [!div class="nextstepaction"]
> [Configurare Single Sign-On](add-application-portal-setup-sso.md)
