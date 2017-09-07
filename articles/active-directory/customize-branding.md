---
title: Personalizzare la pagina di accesso in Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure
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
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 6bfce3d4ec243779229cc4f39e1c22149229a66a
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Avvio rapido: Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso in Azure AD
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. Azure Active Directory (Azure AD) offre questa funzionalità consentendo di personalizzare l'aspetto della pagina di accesso, in modo da includere il logo e le combinazioni colori personalizzate dell'azienda. La pagina di accesso è la pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

> [!NOTE]
> * La possibilità di aggiungere informazioni personalizzate distintive dell'azienda è disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure AD o se si possiede una licenza di Office 365. Per determinare se una funzionalità sia o meno supportata da un tipo di licenza specifico, vedere la pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso

<!--You can customize the following elements on the sign-in page: <attach image>-->

Le personalizzazioni distintive dell'azienda vengono visualizzate nella pagina di accesso di Azure AD quando gli utenti accedono a un URL specifico del tenant, ad esempio [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Quando gli utenti visitano un URL generico, ad esempio www.office.com, la pagina di accesso non contiene personalizzazioni distintive dell'azienda perché il sistema non riconosce l'utente. Le informazioni personalizzate distintive dell'azienda vengono visualizzate non appena gli utenti immettono l'ID utente o selezionano un riquadro utente.

> [!NOTE]
> * Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Domini** del portale di Azure in cui è stata configurata la personalizzazione. Per altre informazioni, vedere [Add a custom domain name](add-custom-domain.md) (Aggiungere un nome di dominio personalizzato).
> * La personalizzazione della pagina di accesso non si applica alla pagina di accesso dell'account Microsoft personale. Se i dipendenti o gli utenti guest aziendali accedono con un account Microsoft personale, la pagina di accesso non rifletterà le personalizzazioni distintive dell'azienda.


### <a name="banner-logo"></a>Logo banner 

Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
Il logo del banner viene visualizzato nella pagina di accesso e nelle pagine del pannello di accesso.<br>Nella pagina di accesso viene visualizzato una volta determinata l'organizzazione dell'utente, in genere dopo l'immissione del nome utente.  | JPG o PNG trasparente<br>Altezza massima: 36 pixel<br>Larghezza massima: 245 pixel | Usare il logo dell'organizzazione in questa posizione.<br>Usare un'immagine trasparente. Non dare per scontato che lo sfondo sarà bianco.<br>Non aggiungere spaziatura interna intorno al logo nell'immagine o il logo apparirà sproporzionatamente piccolo.

### <a name="username-hint"></a>Suggerimento per il nome utente   
Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
Consente di personalizzare il testo di suggerimento nel campo relativo al nome utente. | Testo Unicode contenente fino a 64 caratteri<br>Solo testo normale | Si consiglia di non impostare questo elemento se si prevede che utenti guest esterni all'organizzazione accedano all'app.
            
### <a name="sign-in-page-text"></a>Testo della pagina di accesso   
Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
Questo testo viene visualizzato nella parte inferiore del modulo di accesso e può essere usato per comunicare informazioni aggiuntive, ad esempio il numero di telefono dell'help desk o una nota legale. | Testo Unicode fino a 256 caratteri<br>Solo testo normale, senza collegamenti o tag HTML   

### <a name="sign-in-page-image"></a>Immagine della pagina di accesso  
Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
Questa immagine viene visualizzata sullo sfondo della pagina di accesso, è ancorata al centro dell'area visualizzabile e viene ridimensionata e ritagliata per riempire la finestra del browser.    <br>Negli schermi stretti, come quelli dei telefoni cellulari, l'immagine non viene visualizzata.<br>Quando la pagina viene caricata, il codice applica una maschera nera con opacità 0,55 sopra all'immagine. | JPG o PNG<br>Dimensioni immagine: 1920 x 1080 pixel<br>Dimensioni del file: &lt; 300 KB | <br>Usare le immagini nei casi in cui non è necessario richiamare l'attenzione sull'argomento. Il modulo di accesso opaco viene visualizzato sopra la parte centrale dell'immagine e può coprire qualsiasi parte dell'immagine, a seconda delle dimensioni della finestra del browser.<br>Mantenere le dimensioni del file quanto più piccole possibile per garantire tempi di caricamento rapidi. 

### <a name="background-color"></a>Colore di sfondo
Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
Questo colore viene usato al posto dell'immagine di sfondo con le connessioni con larghezza di banda ridotta. | Colore RGB in formato esadecimale (esempio: #FFFFFF) | È consigliabile usare il colore primario del logo del banner o il colore dell'organizzazione.

### <a name="show-option-to-remain-signed-in"></a>Impostazione Mostra l'opzione per mantenere l'accesso
Descrizione | Vincoli | Raccomandazioni
------- | ------- | ----------
La pagina di accesso di Azure AD include un'opzione che consente a un utente di rimanere connesso quando chiude e riapre il browser. Usare questa impostazione per nascondere tale opzione.<br>Impostare il valore su "No" per nascondere l'opzione agli utenti. | &nbsp; | Questa impostazione non influisce sulla durata della sessione.<br>Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dal fatto che gli utenti possano o meno scegliere se restare connessi. Se si configura questa impostazione come nascosta, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con una richiesta di accesso.

> [!NOTE]
> Tutti gli elementi sono facoltativi. Se, ad esempio, si specifica un logo del banner senza immagine di sfondo, la pagina di accesso mostrerà il logo e l'immagine di sfondo per il sito di destinazione, ad esempio Office 365.

## <a name="add-company-branding-to-your-directory"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla directory

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura di Gestione utenti](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Informazioni personalizzate distintive dell'azienda**.
4. Nel pannello **Utenti e gruppi - Informazioni personalizzate distintive dell'azienda** selezionare il comando **Modifica**.

    ![Modificare le informazioni personalizzate](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Per aggiungere alla directory informazioni personalizzate distintive dell'azienda specifiche di una lingua

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura di Gestione utenti](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Informazioni personalizzate distintive dell'azienda**.
4. Nel pannello **Utenti e gruppi - Informazioni personalizzate distintive dell'azienda** selezionare il comando **Aggiungi lingua**.

    ![Aggiungere elementi personalizzati distintivi dell'azienda specifiche della lingua](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come aggiungere informazioni personalizzate distintive dell'azienda alla directory di Azure AD. 

È possibile usare il collegamento seguente per aggiungere informazioni personalizzate distintive dell'azienda in Azure AD dal portale di Azure.

> [!div class="nextstepaction"]
> [Configurare la personalizzazione aziendale](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 
