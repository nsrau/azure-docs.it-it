---
title: Personalizzare la pagina di accesso nel tenant di Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/19/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 03a6b82f769ed9a36c5d3ff9934de75d1536e1ae
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Avvio rapido: Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso in Azure AD
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. Azure Active Directory (Azure AD) offre questa funzionalità consentendo di personalizzare l'aspetto della pagina di accesso, in modo da includere il logo e le combinazioni colori personalizzate dell'azienda. La pagina di accesso viene visualizzata quando si accede alle applicazioni basate sul Web, come Office 365, che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

> [!NOTE]
> * La possibilità di aggiungere informazioni personalizzate distintive dell'azienda è disponibile solo se è stata acquistata la licenza Premium o Basic di Azure AD o se si possiede una licenza di Office 365. Per determinare se una funzionalità sia o meno supportata da un tipo di licenza specifico, vedere la pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Le edizioni Premium e Basic di Azure AD sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure AD Premium e le edizioni Basic non sono attualmente supportati nel servizio di Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso

<!--You can customize the following elements on the sign-in page: <attach image>-->

Le personalizzazioni distintive dell'azienda vengono visualizzate nella pagina di accesso di Azure AD quando gli utenti accedono a un URL specifico del tenant, ad esempio [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Ad esempio, quando gli utenti visitano il sito www.office.com, la pagina di accesso non contiene personalizzazioni distintive dell'azienda perché l'utente non ha ancora inserito le credenziali. Le informazioni personalizzate distintive dell'azienda vengono visualizzate non appena gli utenti immettono l'ID utente o selezionano un riquadro utente.

> [!NOTE]
> * Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Domini** del portale di Azure in cui è stata configurata la personalizzazione. Per altre informazioni, vedere [Add a custom domain name](add-custom-domain.md) (Aggiungere un nome di dominio personalizzato).
> * La personalizzazione della pagina di accesso non si applica alla pagina di accesso dell'account Microsoft personale. Se i dipendenti o gli utenti guest aziendali accedono con un account Microsoft personale, la pagina di accesso non rifletterà le personalizzazioni distintive dell'azienda.


### <a name="banner-logo"></a>Logo banner 

DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Il logo del banner viene visualizzato nella pagina di accesso e nelle pagine del pannello di accesso.<br>Nella pagina di accesso il logo viene visualizzato dopo l'immissione del nome utente. | JPG o PNG trasparente<br>Altezza massima: 36 pixel<br>Larghezza massima: 245 pixel | Usare il logo dell'organizzazione in questa posizione.<br>Usare un'immagine trasparente. Non dare per scontato che lo sfondo sarà bianco.<br>Non aggiungere spaziatura interna intorno al logo nell'immagine o il logo apparirà sproporzionatamente piccolo.

### <a name="username-hint"></a>Suggerimento per il nome utente   
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Questa opzione consente di personalizzare il testo di suggerimento nel campo del nome utente. | Testo Unicode contenente fino a 64 caratteri<br>Solo testo normale | Evitare di impostare questa opzione se si prevede che utenti guest esterni all'organizzazione accedano all'app.
            
### <a name="sign-in-page-text"></a>Testo della pagina di accesso   
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Questa opzione viene visualizzata nella parte inferiore del modulo di accesso e può essere usata per comunicare informazioni aggiuntive, ad esempio il numero di telefono dell'help desk o una nota legale. | Testo Unicode contenente fino a 256 caratteri<br>Solo testo normale, senza collegamenti o tag HTML    

### <a name="sign-in-page-image"></a>Immagine della pagina di accesso  
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Questa opzione viene visualizzata sullo sfondo della pagina di accesso, è ancorata al centro dell'area visualizzabile e viene ridimensionata e ritagliata per riempire la finestra del browser.    <br>Negli schermi stretti, come quelli dei telefoni cellulari, l'immagine non viene visualizzata.<br>Quando la pagina viene caricata, viene applicata una maschera nera con opacità 0,55 sopra all'immagine. | JPG o PNG<br>Dimensioni immagine: 1920 x 1080 pixel<br>Dimensioni del file: &lt; 300 KB | <br>Usare le immagini nei casi in cui non è necessario richiamare l'attenzione sull'argomento. Il modulo di accesso opaco viene visualizzato sopra la parte centrale dell'immagine e può coprire qualsiasi parte dell'immagine, a seconda delle dimensioni della finestra del browser.<br>Mantenere le dimensioni del file piccole per garantire tempi di caricamento rapidi. 

### <a name="sign-in-page-background-color"></a>Colore di sfondo della pagina di accesso
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Questo colore viene usato al posto dell'immagine di sfondo con le connessioni con larghezza di banda ridotta. | Colore RGB in formato esadecimale (esempio: #FFFFFF) | È consigliabile usare il colore primario del logo del banner o il colore dell'organizzazione.

### <a name="square-logo-image"></a>Immagine con logo quadrato
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
Questa immagine viene visualizzata durante la configurazione dei PC Windows 10 Enterprise. Fornisce il contesto quando i dipendenti configurano il loro nuovo PC aziendale. L'immagine viene visualizzata per i tenant che usano [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) per distribuire i dispositivi aziendali e nelle pagine di immissione della password in altri ambienti Windows 10. | Immagine PNG (preferibile) o JPG trasparente<br>Dimensioni dell'immagine: 240x240 px<br>Dimensioni del file: &lt; 10 KB | Usare il logo dell'organizzazione in questa posizione.<br> Usare un'immagine trasparente.<br>Non dare per scontato che lo sfondo sarà bianco.<br>Non aggiungere spaziatura interna al logo nell'immagine o il logo apparirà sproporzionatamente piccolo.

### <a name="show-option-to-remain-signed-in"></a>Impostazione Mostra l'opzione per mantenere l'accesso
DESCRIZIONE | Vincoli | Raccomandazioni
------- | ------- | ----------
La pagina di accesso di Azure AD include un'opzione che consente a un utente di restare connesso quando chiude e riapre il browser. Questa impostazione nasconde l'opzione.<br>Impostare il valore su **No** per nascondere l'opzione agli utenti. | &nbsp; | Questa impostazione non influisce sulla durata della sessione.<br>Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dal fatto che gli utenti possano o meno scegliere se restare connessi. Se si imposta l'opzione su **No**, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con richieste di accesso.

> [!NOTE]
> Tutti gli elementi sono facoltativi. Se, ad esempio, si specifica un logo del banner senza immagine di sfondo, la pagina di accesso mostrerà il logo e l'immagine di sfondo per il sito di destinazione, ad esempio Office 365.

## <a name="add-company-branding-to-your-directory"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla directory

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per il tenant.
2. Selezionare **Utenti e gruppi** > **Informazioni personalizzate distintive dell'azienda** > **Modifica**.
  
  ![Apertura delle informazioni personalizzate](./media/customize-branding/navigation-to-branding.png)
3. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
  
  ![Modificare le informazioni personalizzate](./media/customize-branding/edit-branding.png)
5. Al termine, selezionare **Salva**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Per aggiungere alla directory informazioni personalizzate distintive dell'azienda specifiche di una lingua

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Utenti e gruppi** > **Informazioni personalizzate distintive dell'azienda** > **Nuova lingua**.
  
  ![Aggiungere elementi personalizzati distintivi dell'azienda specifiche della lingua](./media/customize-branding/add-language.png)
5. Modificare gli elementi da personalizzare. Tutti gli elementi sono facoltativi.
6. Al termine, selezionare **Salva**.

Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come aggiungere informazioni personalizzate distintive dell'azienda alla directory di Azure AD. 

È possibile usare il collegamento seguente per aggiungere informazioni personalizzate distintive dell'azienda in Azure AD dal portale di Azure.

> [!div class="nextstepaction"]
> [Configurare la personalizzazione aziendale](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 