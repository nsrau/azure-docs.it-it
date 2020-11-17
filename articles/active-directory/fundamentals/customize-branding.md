---
title: "Aggiungere la personalizzazione alla pagina di accesso dell'organizzazione: Azure AD"
description: Istruzioni su come aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb138ade0f579a7545c0910646b6adfb7d5ac02a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650224"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso di Azure Active Directory
Usare il logo e combinazioni colori personalizzate dell'azienda per offrire un aspetto coerente per le pagine di accesso di Azure Active Directory (Azure AD). Le pagine di accesso vengono visualizzate quando gli utenti possono accedere alle app basate sul Web dell'organizzazione, ad esempio Microsoft 365, che usa Azure AD come provider di identità.

>[!NOTE]
>Per aggiungere una personalizzazione personalizzata è necessario avere Azure Active Directory Premium 1 o Premium 2 licenze. Per altre informazioni sulle licenze e sulle edizioni, vedere [Iscriversi ad Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium edizioni sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Le edizioni di Azure AD Premium non sono attualmente supportate nel servizio di Azure gestito da 21Vianet in Cina. Per altre informazioni, contattare Microsoft tramite il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Personalizzare la pagina di accesso di Azure AD
È possibile personalizzare le pagine di accesso di Azure AD che vengono visualizzate quando gli utenti accedono alle applicazioni specifiche del tenant dell'organizzazione, quali `https://outlook.com/contoso.com`, o passando la variabile del dominio, ad esempio `https://passwordreset.microsoftonline.com/?whr=contoso.com`.

La personalizzazione personalizzata non verrà visualizzata immediatamente quando gli utenti passano a siti come, www \. Office.com. L'utente dovrà effettuare invece l'accesso prima che vengano visualizzate le informazioni distintive. Dopo che l'utente ha eseguito l'accesso, la visualizzazione della personalizzazione potrebbe richiedere 15 minuti o più. 

> [!NOTE]
> Tutti gli elementi personalizzati distintivi sono facoltativi. Se, ad esempio, si specifica un logo del banner senza immagine di sfondo, la pagina di accesso visualizzerà il logo con un'immagine di sfondo predefinita del sito di destinazione, ad esempio Microsoft 365.<br><br>Inoltre, la personalizzazione della pagina di accesso non viene applicata agli account Microsoft personali. Se gli utenti o gli utenti guest aziendali accedono mediante un account Microsoft personale, la pagina di accesso non rifletterà le informazioni personalizzate distintive dell'azienda.

### <a name="to-customize-your-branding"></a>Per personalizzare le informazioni personalizzate distintive
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, quindi selezionare **Informazioni personalizzate distintive dell'azienda** e selezionare **Configura**.

    ![Contoso - Pagina delle informazioni personalizzate distintive dell'azienda, opzione Configura evidenziata](media/customize-branding/company-branding-configure-button.png)

3. Nella pagina **Configurare le informazioni personalizzate distintive dell'azienda**, indicare una o tutte le informazioni seguenti.

    >[!IMPORTANT]
    >Tutte le immagini personalizzate che è possibile aggiungere in questa pagina hanno restrizioni relative alle dimensioni dell'immagine (pixel) e potenzialmente alle dimensioni del file (KB). A causa di queste restrizioni, sarà probabilmente necessario usare un editor di foto per creare immagini della giusta dimensione.

    - **Impostazioni generali**

        ![Configurare la pagina delle informazioni personalizzate distintive dell'azienda, con le impostazioni generali completate](media/customize-branding/configure-company-branding-general-settings.png)

        - **Linguaggio.** La lingua viene impostata automaticamente come predefinita e non può essere cambiata.
        
        - **Immagine di sfondo della pagina di accesso.** Selezionare un file di immagine .png o .jpg da visualizzare come sfondo per le pagine di accesso. L'immagine verrà ancorata al centro del browser e verrà ridimensionata fino alla dimensione dello spazio visualizzabile. Non è possibile selezionare un'immagine di dimensioni maggiori di 1920x1080 pixel o con una dimensione di file superiore a 300 KB.
        
            È consigliabile usare le immagini senza uno stato attivo, ad esempio, una casella bianca opaca viene visualizzata al centro dello schermo e può coprire qualsiasi parte dell'immagine in base alle dimensioni dello spazio visualizzabile.

        - **Logo del banner.** Selezionare una versione .png o .jpg del logo da visualizzare nella pagina di accesso dopo che l'utente ha inserito un nome utente nella pagina del portale **My Apps (Le mie applicazioni)**.
            
            L'immagine non può essere più alta di 60 pixel o più ampia di 280 pixel. È consigliabile usare un'immagine trasparente poiché lo sfondo potrebbe non corrispondere allo sfondo del logo. È inoltre consigliabile non aggiungere una spaziatura interna intorno all'immagine perché potrebbe far sembrare piccolo il logo.

        - **Hint nome utente.** Digitare il testo di suggerimento visualizzato agli utenti qualora non ricordino il loro nome utente. Questo testo deve essere Unicode, senza collegamenti o codice e non può superare i 64 caratteri. Se gli utenti guest accedono all'app, è consigliabile non aggiungere questo hint.

        - **Testo e formattazione della pagina di accesso.** Digitare il testo visualizzato in fondo alla pagina di accesso. Questo testo può essere usato per comunicare informazioni aggiuntive, ad esempio il numero di telefono dell'help desk o una nota legale. Il testo deve essere Unicode e non deve superare i 1024 caratteri.

           È possibile personalizzare il testo della pagina di accesso immesso. Per iniziare un nuovo paragrafo, usare due volte il tasto INVIO. È anche possibile modificare la formattazione del testo in modo da includere grassetto, corsivo, una sottolineatura o un collegamento selezionabile. Utilizzare la sintassi seguente per aggiungere la formattazione al testo: 

          > Collegamento ipertestuale ```[text](link)``` 
          
          > Grassetto: ``` **text** ``` o ``` __text__ ``` 
          
          > Corsivo: ``` *text* ``` o ``` _text_ ``` 
          
          > Sottolineare ``` ++text++ ``` 

    - **Impostazioni avanzate**
            
        ![Configurare la pagina delle informazioni personalizzate distintive dell'azienda, con le impostazioni avanzate completate](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Colore di sfondo della pagina di accesso.** Specificare il colore esadecimale (ad esempio, bianco è #FFFFFF) che verrà visualizzato al posto dell'immagine di sfondo in situazioni di connessione a larghezza di banda ridotta. È consigliabile usare il colore primario del logo del banner o il colore dell'organizzazione.

        - **Immagine con logo quadrato.** Selezionare un'immagine con estensione png (scelta consigliata) o jpg del logo dell'organizzazione da visualizzare agli utenti durante il processo di installazione per i nuovi dispositivi Windows 10 Enterprise. Questa immagine viene usata solo per l'autenticazione di Windows e viene visualizzata solo sui tenant che usano [Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) per la distribuzione o per le pagine di immissione della password in altre situazioni di Windows 10. In alcuni casi può essere visualizzato anche nella finestra di dialogo di consenso.
        
            Le dimensioni dell'immagine non possono essere maggiori di 240x240 pixel e le dimensioni del file devono essere minori di 10 KB. È consigliabile usare un'immagine trasparente poiché lo sfondo potrebbe non corrispondere allo sfondo del logo. È inoltre consigliabile non aggiungere una spaziatura interna intorno all'immagine perché potrebbe far sembrare piccolo il logo.
    
        - **Immagine con logo quadrato, tema scuro.** Uguale all'immagine con logo quadrato precedente. Questa immagine del logo sostituisce l'immagine con logo quadrato quando viene utilizzata con uno sfondo scuro, come ad esempio con le schermate unite di Windows 10 Azure AD durante la configurazione guidata.  Se il logo sembra valido in background bianco, blu scuro e nero, non è necessario aggiungere questa immagine. 
        
        - **Mostra l'opzione per mantenere l'accesso.** È possibile scegliere di consentire agli utenti di rimanere connessi per Azure AD fino a quando non viene disconnesso in modo esplicito. Se si sceglie **No**, questa opzione è nascosta e gli utenti devono accedere ogni volta che il browser viene chiuso e riaperto.

            Questa funzionalità è disponibile solo per l'oggetto di personalizzazione predefinito e non per alcun oggetto specifico del linguaggio. Per altre informazioni sulla configurazione e la risoluzione dei problemi relativi all'opzione che consente di rimanere connessi, vedere [configurare la richiesta "Stay signed in?" per gli account di Azure ad](keep-me-signed-in.md)
        
            >[!NOTE]
            >Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dal fatto che gli utenti possano o meno scegliere se restare connessi. Se si imposta l'opzione su **No**, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con richieste di accesso.
   

3. Dopo aver aggiunto la personalizzazione, selezionare **Salva**.

    Se questo processo crea la prima configurazione delle Informazioni personalizzate distintive dell'azienda, diventa il valore predefinito per il tenant. Se si dispone di configurazioni aggiuntive, sarà possibile scegliere la configurazione predefinita.
    
    >[!IMPORTANT]
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

    ![Contoso-pagina branding aziendale con la nuova configurazione della lingua visualizzata](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Aggiungere le informazioni personalizzate distintive dell'azienda alle pagine
Aggiungere le informazioni personalizzate distintive dell'azienda alle pagine modificando la fine dell'URL con il testo, `?whr=yourdomainname`. Questa modifica funziona su più pagine, tra cui la pagina di configurazione dell'autenticazione a più fattori, la pagina di configurazione della reimpostazione delle password self-service (SSPR) e la pagina di accesso.

**Esempi:**

**URL originale:** https://aka.ms/MFASetup<br>
**URL personalizzato:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**URL originale:** https://aka.ms/SSPR<br>
**URL personalizzato:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
