---
title: "Avvio rapido: Configurare le proprietà per un'applicazione nel tenant di Azure Active Directory (Azure AD)"
description: In questo argomento di avvio rapido si usa il portale di Azure per configurare un'applicazione registrata nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: a1a99e9f02a25f5e1d57ea485930a4f26149b53f
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808406"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare le proprietà per un'applicazione nel tenant di Azure Active Directory (Azure AD)

Nell'argomento di avvio rapido precedente è stata aggiunta un'applicazione al tenant di Azure Active Directory (Azure AD). Quando si aggiunge un'applicazione, il tenant di Azure AD riconosce di essere il provider di identità per l'app. A questo punto, verranno configurate alcune delle proprietà per l'app.
 
## <a name="prerequisites"></a>Prerequisiti

Per configurare le proprietà di un'applicazione nel tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.

## <a name="configure-app-properties"></a>Configurare le proprietà dell'app

Una volta completata l'aggiunta di un'applicazione al tenant di Azure AD, viene visualizzata la pagina di panoramica. Se si configura un'applicazione già aggiunta, consultare il primo argomento di avvio rapido, che illustra come visualizzare le applicazioni aggiunte al tenant. 

Per modificare le proprietà dell'applicazione:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Trovare e selezionare l'applicazione da configurare.
2. Nella sezione **Gestisci** selezionare **Proprietà** per aprire il riquadro **Proprietà** per la modifica.

    ![Screenshot della schermata Proprietà che mostra le proprietà modificabili dell'app.](media/add-application-portal/edit-properties.png)

3. Esaminare le opzioni disponibili per la configurazione:
    - **Abilitata per l'accesso degli utenti?** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **Assegnazione di utenti obbligatoria** determina se gli utenti non assegnati all'applicazione potranno eseguire l'accesso.
    - **Visibile agli utenti?** determina se gli utenti assegnati a un'app possono vederla nel [pannello di accesso](https://myapps.microsoft.com) e nell'icona di avvio delle app di Office 365. (Vedere il menu waffle nell'angolo in alto a sinistra di un sito Web di Office 365 o Microsoft 365).
4. Usare le tabelle seguenti per scegliere le opzioni ottimali per le proprie esigenze.

   - Comportamento per gli utenti *assegnati*:

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti assegnati | Esperienza degli utenti assegnati |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti assegnati?* |
       | Sì | Sì | Sì | Sì | Sì  |
       | Sì | Sì | No  | Sì | No   |
       | Sì | No  | Sì | Sì | Sì  |
       | Sì | No  | No  | Sì | No   |
       | No  | Sì | Sì | No  | No   |
       | No  | Sì | No  | No  | No   |
       | No  | No  | Sì | No  | No   |
       | No  | No  | No  | No  | No   |

   - Comportamento per gli utenti *non assegnati*:

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti non assegnati | Esperienza degli utenti non assegnati |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti non assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti non assegnati?* |
       | Sì | Sì | Sì | No  | No   |
       | Sì | Sì | No  | No  | No   |
       | Sì | No  | Sì | Sì | No   |
       | Sì | No  | No  | Sì | No   |
       | No  | Sì | Sì | No  | No   |
       | No  | Sì | No  | No  | No   |
       | No  | No  | Sì | No  | No   |
       | No  | No  | No  | No  | No   |

     *L'applicazione viene visualizzata agli utenti nel pannello di accesso e nell'icona di avvio delle app di Office 365?

## <a name="use-a-custom-logo"></a>Usare un logo personalizzato

Per usare un logo personalizzato:

1. Creare un logo di 215 x 215 pixel e salvarlo in formato PNG.
2. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Trovare e selezionare l'applicazione da configurare.
3. Nella sezione **Gestisci** selezionare **Proprietà** per aprire il riquadro **Proprietà** per la modifica. 
4. Selezionare l'icona per caricare il logo.
5. Al termine, fare clic su **Salva**.

    ![Screenshot della schermata Proprietà che mostra come cambiare il logo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > L'anteprima visualizzata in questo riquadro **Proprietà** non viene aggiornata immediatamente. Per visualizzare l'icona aggiornata, è possibile chiudere e riaprire il riquadro **Proprietà**.


> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare con questa serie di argomenti di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'accesso Single Sign-On per un'app, passare all'articolo successivo.
> [!div class="nextstepaction"]
> [Configurare Single Sign-On](add-application-portal-setup-sso.md)
