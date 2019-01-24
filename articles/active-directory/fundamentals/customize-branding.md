---
title: Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: it-pro, seodec18
ms.openlocfilehash: 7abfa0e52abb594668935d325835e3ef25818aa2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452424"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure Active Directory
Usare il logo e combinazioni colori personalizzate dell'azienda per offrire un aspetto coerente per le pagine di accesso di Azure Active Directory (Azure AD). La pagina di accesso viene visualizzata quando si accede alle applicazioni basate sul Web dell'organizzazione, come Office 365, che usano Azure AD come provider di identità.

>[!Note]
>Per l'aggiunta di informazioni personalizzate distintive dell'azienda è necessario usare Azure Active Directory Premium 1, Premium 2 o le edizioni Basic, oppure disporre di una licenza di Office 365. Per altre informazioni sulle licenze e sulle edizioni, vedere [Iscriversi ad Azure AD Premium](active-directory-get-started-premium.md).<br><br>Le edizioni Premium e Basic di Azure AD sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Le edizioni Azure AD Premium e Basic non sono attualmente supportate nel servizio di Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalizzare la pagina di accesso di Azure AD
È possibile personalizzare le pagine di accesso di Azure AD che vengono visualizzate quando gli utenti accedono alle applicazioni specifiche del tenant dell'organizzazione, quali [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com), o passando la variabile del dominio, ad esempio [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Le personalizzazioni distintive non verranno visualizzate immediatamente quando gli utenti accedono a siti come www.office.com. L'utente dovrà effettuare invece l'accesso prima che vengano visualizzate le informazioni distintive.

> [!NOTE]
> Tutti gli elementi personalizzati distintivi sono facoltativi. Se, ad esempio, si specifica un logo del banner senza immagine di sfondo, la pagina di accesso mostrerà il logo con un'immagine di sfondo predefinita ricavata dal sito di destinazione, ad esempio Office 365.<br><br>Inoltre, la personalizzazione della pagina di accesso non si applica agli account Microsoft personali. Se gli utenti o gli utenti guest aziendali accedono mediante un account Microsoft personale, la pagina di accesso non rifletterà le informazioni personalizzate distintive dell'azienda.

### <a name="to-customize-your-branding"></a>Per personalizzare le informazioni personalizzate distintive
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, quindi selezionare **Informazioni personalizzate distintive dell'azienda** e selezionare **Configura**.

    ![Contoso - Pagina delle informazioni personalizzate distintive dell'azienda, opzione Configura evidenziata](media/customize-branding/company-branding-configure-button.png)

3. Nella pagina **Configurare le informazioni personalizzate distintive dell'azienda**, indicare una o tutte le informazioni seguenti.

    >[!Important]
    >Tutte le immagini personalizzate che è possibile aggiungere in questa pagina hanno restrizioni relative alle dimensioni dell'immagine (pixel) e potenzialmente alle dimensioni del file (KB). A causa di queste restrizioni, sarà probabilmente necessario usare un editor di foto per creare immagini della giusta dimensione.

    - **Impostazioni generali**

        ![Configurare la pagina delle informazioni personalizzate distintive dell'azienda, con le impostazioni generali completate](media/customize-branding/configure-company-branding-general-settings.png)

        - **Lingua.** La lingua viene impostata automaticamente come predefinita e non può essere cambiata.
        
        - **Immagine di sfondo della pagina di accesso.** Selezionare un file di immagine .png o .jpg da visualizzare come sfondo per le pagine di accesso. 
        
            L'immagine non può essere più grande di 1920x1080 pixel e deve avere una dimensione del file inferiore a 300 kB.

        - **Logo banner.** Selezionare una versione .png o .jpg del logo da visualizzare nella pagina di accesso dopo che l'utente ha inserito un nome utente nella pagina del portale **My Apps (Le mie applicazioni)**.
            
            L'immagine non può essere più alta di 36 pixel o più ampia di 245 pixel. È consigliabile usare un'immagine trasparente poiché lo sfondo potrebbe non corrispondere allo sfondo del logo. È inoltre consigliabile non aggiungere una spaziatura interna intorno all'immagine perché potrebbe far sembrare piccolo il logo.

        - **Suggerimento per il nome utente.** Digitare il testo di suggerimento visualizzato agli utenti qualora non ricordino il loro nome utente. Questo testo deve essere Unicode, senza collegamenti o codice e non può superare i 64 caratteri. Se gli utenti guest accedono all'app, è consigliabile non aggiungere questo hint.

        - **Testo della pagina di accesso.** Digitare il testo visualizzato in fondo alla pagina di accesso. Questo testo può essere usato per comunicare informazioni aggiuntive, ad esempio il numero di telefono dell'help desk o una nota legale. Questo testo deve essere in formato Unicode e non superare i 256 caratteri. È consigliabile, inoltre, non includere collegamenti o tag HTML.

    - **Impostazioni avanzate**
            
        ![Configurare la pagina delle informazioni personalizzate distintive dell'azienda, con le impostazioni avanzate completate](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Colore di sfondo della pagina di accesso.** Specificare il colore esadecimale (ad esempio, bianco è #FFFFFF) che verrà visualizzato al posto dell'immagine di sfondo in situazioni di connessione a larghezza di banda ridotta. È consigliabile usare il colore primario del logo del banner o il colore dell'organizzazione.

        - **Immagine con logo quadrato.** Selezionare un'immagine in formato .png (preferito) o .jpg del logo dell'organizzazione che sarà visualizzata dagli utenti durante il processo di installazione per i nuovi dispositivi Windows 10 Enterprise. Questa immagine viene usata solo per l'autenticazione di Windows e viene visualizzata solo sui tenant che usano [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) per la distribuzione o per le pagine di immissione della password in altre situazioni di Windows 10.
        
            L'immagine non può essere più grande di 240x240 pixel e il file deve essere inferiore a 10 kB. È consigliabile usare un'immagine trasparente poiché lo sfondo potrebbe non corrispondere allo sfondo del logo. È inoltre consigliabile non aggiungere una spaziatura interna intorno all'immagine perché potrebbe far sembrare piccolo il logo.
    
        - **Immagine con logo quadrato, tema scuro.** Uguale all'immagine con logo quadrato precedente. Questa immagine del logo sostituisce l'immagine con logo quadrato quando viene utilizzata con uno sfondo scuro, come ad esempio con le schermate unite di Windows 10 Azure AD durante la configurazione guidata.  Se il logo ha un aspetto corretto su sfondi bianchi, blu scuro e neri, non è necessario aggiungere questa immagine. 
        
        - **Mostra l'opzione per mantenere l'accesso.** È possibile scegliere di consentire agli utenti di restare connessi ad Azure AD fino alla disconnessione in modo esplicito. Se si sceglie **No**, questa opzione è nascosta e gli utenti devono accedere ogni volta che il browser viene chiuso e riaperto.
        
            >[!Note]
            >Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dal fatto che gli utenti possano o meno scegliere se restare connessi. Se si imposta l'opzione su **No**, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con richieste di accesso.
   

3. Dopo aver aggiunto la personalizzazione, selezionare **Salva**.

    Se questo processo crea la prima configurazione delle Informazioni personalizzate distintive dell'azienda, diventa il valore predefinito per il tenant. Se si dispone di configurazioni aggiuntive, sarà possibile scegliere la configurazione predefinita.
    
    >[!Important]
    >Per aggiungere ulteriori configurazioni di Informazioni personalizzate distintive dell'azienda al tenant, è necessario scegliere **Nuova lingua** nella pagina **Contoso - Informazioni personalizzate distintive dell'azienda**. Verrà visualizzata la pagina **Configurare le informazioni personalizzate distintive dell'azienda**, in cui è possibile seguire gli stessi passaggi riportati in precedenza.

## <a name="update-your-custom-branding"></a>Aggiornare le informazioni personalizzate distintive dell'azienda
Dopo aver creato le informazioni personalizzate distintive dell'azienda, è possibile tornare indietro e modificare qualsiasi informazione.

### <a name="to-edit-your-custom-branding"></a>Per modificare le informazioni personalizzate distintive dell'azienda
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, quindi selezionare **Informazioni personalizzate distintive dell'azienda** e selezionare **Configura**.

    ![Contoso - Informazioni personalizzate distintive dell'azienda, con la configurazione predefinita visualizzata](media/customize-branding/company-branding-default-config.png)

3. Nella pagina **Configurare le informazioni personalizzate distintive dell'azienda**, aggiungere, rimuovere o modificare le informazioni, in base alle descrizioni riportate nella sezione [Personalizzare la pagina di accesso di Azure AD](#customize-your-azure-ad-sign-in-page) di questo articolo.

4. Selezionare **Salva**.

  Può trascorrere fino a un'ora prima che qualsiasi modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Per aggiungere alla directory informazioni personalizzate distintive dell'azienda specifiche di una lingua
Non è possibile modificare la lingua della configurazione originale dalla lingua predefinita. Tuttavia, se è necessaria una configurazione in una lingua diversa, è possibile creare una nuova configurazione.

### <a name="to-add-a-language-specific-branding-configuration"></a>Per aggiungere una configurazione di personalizzazione specifica di una lingua

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, quindi selezionare **Informazioni personalizzate distintive dell'azienda** e selezionare **Nuova lingua**.

    ![Contoso - Pagina delle informazioni personalizzate distintive dell'azienda con l'opzione Nuova lingua evidenziata](media/customize-branding/company-branding-new-language.png)

3. Nella pagina **Configurare le informazioni personalizzate distintive dell'azienda**, selezionare la lingua (ad esempio francese) e aggiungere le informazioni tradotte, in base alle descrizioni riportate nella sezione [Personalizzare la pagina di accesso di Azure AD](#customize-your-azure-ad-sign-in-page) di questo articolo.

4. Selezionare **Salva**.

    La pagina **Contoso - Informazioni personalizzate distintive dell'azienda** viene aggiornata per indicare la nuova configurazione in francese.

    ![Contoso - Informazioni personalizzate distintive dell'azienda, con la configurazione predefinita visualizzata](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Aggiungere le informazioni personalizzate distintive dell'azienda alle pagine
Aggiungere le informazioni personalizzate distintive dell'azienda alle pagine modificando la fine dell'URL con il testo, `?whr=yourdomainname`. Questa modifica funziona su più pagine, tra cui la pagina di configurazione dell'autenticazione a più fattori, la pagina di configurazione della reimpostazione delle password self-service (SSPR) e la pagina di accesso.

**Esempi:**

**URL originale:** https://aka.ms/MFASetup<br>
**URL personalizzato:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**URL originale:** https://aka.ms/SSPR<br>
**URL personalizzato:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 