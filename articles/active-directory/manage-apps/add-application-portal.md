---
title: "Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)"
description: Questa guida introduttiva usa il portale di Azure per aggiungere un'applicazione della raccolta al tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956101"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)

Azure Active Directory (Azure AD) offre una raccolta contenente migliaia di applicazioni preintegrate. Molte delle applicazioni usate dall'organizzazione sono probabilmente già presenti nella raccolta. In questo argomento di avvio rapido si usa il portale di Azure per aggiungere un'applicazione della raccolta al tenant di Azure AD. Questo argomento di avvio rapido illustra la procedura di aggiunta di un'app che fa già parte della raccolta. È possibile integrare con Azure AD anche app non incluse nella raccolta, ma la relativa procedura non è descritta in questo argomento di avvio rapido. 

Dopo l'aggiunta di un'applicazione al tenant di Azure AD, è possibile:

- Configurare le proprietà per l'app.
- Gestire l'accesso degli utenti all'app con criteri di accesso condizionale.
- Configurare l'accesso Single Sign-On in modo che gli utenti possano accedere all'app con le credenziali Azure AD personali.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere un'applicazione al tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- (Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md)).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Aggiungere un'app al tenant di Azure AD

Per aggiungere un'applicazione della raccolta al tenant di Azure AD:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
2. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali**. Verrà aperto il riquadro **Tutte le applicazioni**, in cui è visualizzato un campione casuale delle applicazioni presenti nel tenant di Azure AD.
3. Nel riquadro **Applicazioni aziendali** selezionare **Nuova applicazione**. 
    ![Selezionare Nuova applicazione per aggiungere un'app della raccolta al tenant](media/add-application-portal/new-application.png)
4. Passare alla nuova esperienza di anteprima della raccolta: Nel banner nella parte superiore della **pagina Aggiungi applicazione** selezionare il collegamento **Fare clic qui per provare la nuova raccolta di app migliorata**.
5. Viene visualizzato il riquadro **Esplora la raccolta di Azure AD (anteprima)** , contenente sezioni per le piattaforme cloud, le applicazioni locali e le applicazioni in primo piano. Per le applicazioni elencate nella sezione **Applicazioni in primo piano** vengono visualizzate icone che indicano se l'accesso Single Sign-On (SSO) federato e il provisioning sono supportati.
    ![Cercare un'app per nome o categoria](media/add-application-portal/browse-gallery.png)
6. È possibile sfogliare la raccolta per individuare l'applicazione da aggiungere oppure cercare l'applicazione immettendone il nome nella casella di ricerca. Selezionare quindi l'applicazione nei risultati. Nel modulo è possibile modificare il nome dell'applicazione in base alle esigenze dell'organizzazione. In questo esempio il nome è stato modificato in **GitHub-test**.
    ![Mostra come aggiungere un'applicazione dalla raccolta](media/add-application-portal/create-application.png)
7. Selezionare **Crea**. Verrà visualizzata una pagina Attività iniziali con le opzioni per configurare l'applicazione per l'organizzazione.

L'aggiunta di un'applicazione è stata completata. L'argomento di avvio rapido successivo illustra come modificare il logo e altre proprietà dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un'app](add-application-portal-configure.md)
- [Configurare Single Sign-On](add-application-portal-setup-sso.md)
- [Eliminare un'app](delete-application-portal.md)