---
title: Aggiungere informazioni personalizzate distintive dell'azienda specifiche della lingua nella pagina di accesso in anteprima di Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere testo e immagini di informazioni personalizzate sull'azienda alla pagina di accesso Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Aggiungere informazioni personalizzate distintive dell'azienda specifiche della lingua nella pagina di accesso in anteprima di Azure Active Directory
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. L'anteprima di Azure Active Directory offre questa funzionalità permettendo di personalizzare l'aspetto delle pagine di accesso, in modo da includere il logo e le combinazioni di colori personalizzate dell'azienda. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) La pagina di accesso è la pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

## Personalizzazione della pagina di accesso per un'altra lingua
È possibile aggiungere elementi specifici della lingua alla pagina di accesso personalizzata solo se è già stata creata una pagina di accesso personalizzata come descritto in [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso](active-directory-branding-custom-signon-azure-portal.md). È possibile configurare una pagina di accesso per ogni directory con un set predefinito di elementi personalizzabili. Dopo avere configurato un set predefinito di elementi di personalizzazione, è possibile configurare altre versioni per impostazioni locali diverse. È anche possibile combinare e abbinare diversi elementi. Ad esempio, è possibile:

* Creare un'**immagine della pagina di accesso** predefinita adatta per tutte le impostazioni cultura, quindi creare versioni specifiche per l'inglese e il francese. Quando si impostano i browser su una di queste due lingue, viene visualizzata l'immagine specifica della lingua, mentre per tutte le altre lingue viene visualizzata l'illustrazione predefinita.
* Configurare loghi diversi per l'organizzazione, ad esempio una versione giapponese o ebraica.

È consigliabile limitare il numero di varianti linguistiche per facilitare la manutenzione e migliorare le prestazioni.

**Per aggiungere informazioni personalizzate distintive dell'azienda alla directory:**

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.
   
   ![Apertura di Gestione utenti](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Company branding** (Informazioni personalizzate distintive dell'azienda).
4. Nel pannello **Utenti e gruppi - Company branding (Informazioni personalizzate distintive dell'azienda)** selezionare il comando **Aggiungi lingua**.
   
    ![Aggiungere elementi personalizzati distintivi dell'azienda specifiche della lingua](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## Passaggi successivi
[Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso](active-directory-branding-custom-signon-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->