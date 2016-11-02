<properties
pageTitle="Personalizzare la pagina di accesso in anteprima di Azure Active Directory | Microsoft Azure"
description="Informazioni su come aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/12/2016"
ms.author="curtand"/>

# Aggiungere informazioni personalizzate distintive dell'azienda nella pagina di accesso in anteprima di Azure Active Directory

Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. L'anteprima di Azure Active Directory offre questa funzionalità permettendo di personalizzare l'aspetto delle pagine di accesso, in modo da includere il logo e le combinazioni di colori personalizzate dell'azienda. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) La pagina di accesso è la pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

Se si vuole mostrare il marchio, i colori e altri elementi personalizzabili dell'azienda in questa pagina, vedere le immagini seguenti per capire la differenza tra le due esperienze.

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **prima** di una personalizzazione:

![Pagina di accesso di Office 365 prima della personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **dopo** una personalizzazione:

![Pagina di accesso di Office 365 dopo la personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## Personalizzazione della pagina di accesso

Di solito si usa la pagina di accesso se è necessario accedere con un browser alle app e ai servizi cloud sottoscritti dall'organizzazione.

Se sono state apportate modifiche alla pagina di accesso, potrebbe trascorrere fino a un'ora prima che le modifiche vengano visualizzate.

Una pagina di accesso personalizzata viene visualizzata quando si visita un servizio con un URL specifico del tenant, ad esempio https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Quando si visita un servizio con URL non specifici del tenant, ad esempio https://mail.office365.com, viene visualizzata una pagina di accesso non personalizzata. In questo caso, la personalizzazione viene visualizzata dopo avere immesso il proprio ID utente o avere selezionato un riquadro utente.

> [AZURE.NOTE]
>
- Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Domini** del portale di Azure in cui è stata configurata la personalizzazione. Per altre informazioni, vedere l'argomento relativo all'[aggiunta di nomi di dominio personalizzati](active-directory-domains-add-azure-portal.md).
- La personalizzazione della pagina di accesso non si applica alla pagina di accesso degli utenti di Microsoft. Se si accede con un account Microsoft, potrebbe essere visualizzato un elenco personalizzato di riquadri utente reso disponibile da Azure AD, ma le informazioni di personalizzazione dell'organizzazione non vengono applicate alla pagina di accesso degli account Microsoft.

**Per aggiungere informazioni personalizzate distintive dell'azienda alla directory:**

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2.  Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

    ![Apertura di Gestione utenti](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Nel pannello **Utenti e gruppi** selezionare **Company branding** (Informazioni personalizzate distintive dell'azienda).

4. Nel pannello **Utenti e gruppi - Company branding (Informazioni personalizzate distintive dell'azienda)** selezionare il comando **Modifica**.

    ![Modificare le informazioni personalizzate](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.

6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## Passaggi successivi

[Aggiungere informazioni personalizzate distintive dell'azienda specifiche della lingua](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->