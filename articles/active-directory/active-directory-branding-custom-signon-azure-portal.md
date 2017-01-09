---
title: Personalizzare la pagina di accesso in anteprima di Azure Active Directory | Documentazione Microsoft
description: Informazioni su come aggiungere informazioni personalizzate distintive dell&quot;azienda alla pagina di accesso di Azure
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 48f85fd7e50c1cb3ece7f8b75779fe7cde92c7da


---
# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Aggiungere informazioni personalizzate distintive dell'azienda nella pagina di accesso in anteprima di Azure Active Directory
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. L'anteprima di Azure Active Directory offre questa funzionalità permettendo di personalizzare l'aspetto delle pagine di accesso, in modo da includere il logo e le combinazioni di colori personalizzate dell'azienda. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)  La pagina di accesso è la pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

Se si vuole mostrare il marchio, i colori e altri elementi personalizzabili dell'azienda in questa pagina, vedere le immagini seguenti per capire la differenza tra le due esperienze.

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **prima** di una personalizzazione:

![Pagina di accesso di Office 365 prima della personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **dopo** una personalizzazione:

![Pagina di accesso di Office 365 dopo la personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso
Di solito si usa la pagina di accesso se è necessario accedere con un browser alle app e ai servizi cloud sottoscritti dall'organizzazione.

Se sono state apportate modifiche alla pagina di accesso, potrebbe trascorrere fino a un'ora prima che le modifiche vengano visualizzate.

Una pagina di accesso personalizzata viene visualizzata solo quando si visita un servizio con un URL specifico del tenant, ad esempio https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Quando si visita un servizio con URL non specifici del tenant (ad esempio, https://mail.office365.com), viene visualizzata una pagina di accesso non personalizzata. In questo caso, la personalizzazione viene visualizzata dopo avere immesso il proprio ID utente o avere selezionato un riquadro utente.

> [!NOTE]
> * Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Domini** del portale di Azure in cui è stata configurata la personalizzazione. Per altre informazioni, vedere l'argomento relativo all' [aggiunta di nomi di dominio personalizzati](active-directory-domains-add-azure-portal.md).
> * La personalizzazione della pagina di accesso non si applica alla pagina di accesso degli utenti di Microsoft. Se si accede con un account Microsoft, potrebbe essere visualizzato un elenco personalizzato di riquadri utente reso disponibile da Azure AD, ma le informazioni di personalizzazione dell'organizzazione non vengono applicate alla pagina di accesso degli account Microsoft.
> 
> 

Nella pagina di accesso, la casella di controllo **Mantieni l'accesso** consente a un utente di rimanere connesso quando chiude e riapre il browser. 

   ![Mantieni l'accesso](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Non influisce sulla durata della sessione. È possibile nascondere la casella di controllo nella pagina di accesso di Azure Active Directory.
La visualizzazione della casella di controllo dipende dall'impostazione dell'opzione **Mantieni l'accesso disabilitato**.

   ![Mantieni l'accesso](./media/active-directory-branding-custom-signon-azure-portal/02.png)

Per nascondere la casella di controllo, impostare questa opzione su **Sì**. 

> [!NOTE]
> Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dalla possibilità per gli utenti di selezionare questa casella. Se si configura questa impostazione come nascosta, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con una richiesta di accesso.
> 
> 

**Per aggiungere informazioni personalizzate distintive dell'azienda alla directory:**

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.
   
   ![Apertura di Gestione utenti](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Informazioni personalizzate distintive dell'azienda**.
4. Nel pannello **Utenti e gruppi - Informazioni personalizzate distintive dell'azienda** selezionare il comando **Modifica**.
   
    ![Modificare le informazioni personalizzate](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere informazioni personalizzate distintive dell'azienda specifiche della lingua](active-directory-branding-localize-azure-portal.md)




<!--HONumber=Jan17_HO1-->


