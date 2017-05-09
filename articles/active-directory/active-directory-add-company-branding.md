---
title: Aggiungere informazioni personalizzate distintive dell&quot;azienda alla pagina di accesso e al pannello di accesso
description: Informazioni su come aggiungere informazioni personalizzate distintive dell&quot;azienda alla pagina di accesso di Azure e al pannello di accesso
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/03/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 144132cf0d591e398d5a6ebafb06fd915eec38b8
ms.openlocfilehash: 1ec1a20dec318153923afd6a0875545d1e102f29
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="add-company-branding-to-your-sign-in-and-access-panel-pages"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. Azure Active Directory offre questa funzionalità permettendo di personalizzare l'aspetto delle pagine Web indicate di seguito, per poter includere il logo e le combinazioni di colori personalizzate dell'azienda:

* **Pagina di accesso** : si tratta della pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Si interagisce con questa pagina durante un'individuazione dell'area di autenticazione principale o per immettere le credenziali. L'individuazione dell'area di autenticazione principale permette al sistema di reindirizzare gli utenti federati ai propri servizi token di sicurezza locali (come AD FS).
* **Pagina del pannello di accesso** : il pannello di accesso è un portale basato sul Web che consente di visualizzare e avviare applicazioni basate sul cloud per le quali l'amministratore di Azure AD ha concesso i diritti di accesso. Per accedere al pannello di accesso, usare l'URL seguente: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Questo argomento illustra come personalizzare la pagina di accesso e il pannello di accesso.

> [!NOTE]
> * La possibilità di aggiungere informazioni personalizzate distintive dell'azienda è disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory o si è utenti di Office 365. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> * Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
>
>

## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso
Di solito si usa la pagina di accesso se è necessario accedere con un browser alle app e ai servizi cloud sottoscritti dall'organizzazione.

Se sono state apportate modifiche alla pagina di accesso, potrebbe trascorrere fino a un'ora prima che le modifiche vengano visualizzate.

Una pagina di accesso personalizzata viene visualizzata solo quando si visita un servizio con un URL specifico del tenant, ad esempio https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Quando si visita un servizio con URL non specifici del tenant (ad esempio, https://mail.office365.com), viene visualizzata una pagina di accesso non personalizzata. In questo caso, la personalizzazione viene visualizzata dopo avere immesso il proprio ID utente o avere selezionato un riquadro utente.

> [!NOTE]
> * Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Active Directory** > **Directory** > **Domini** del portale di Azure classico dopo la configurazione della personalizzazione.
> * La personalizzazione della pagina di accesso non si applica alla pagina di accesso degli utenti di Microsoft. Se si accede con un account Microsoft personale, potrebbe essere visualizzato un elenco personalizzato di icone utente reso disponibile da Azure AD, ma le informazioni di personalizzazione dell'organizzazione non vengono applicate alla pagina di accesso degli account Microsoft.
>
>

Se si vuole mostrare il marchio, i colori e altri elementi personalizzabili dell'azienda in questa pagina, vedere le immagini seguenti per capire la differenza tra le due esperienze.

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **prima** di una personalizzazione:

![Pagina di accesso di Office 365 prima della personalizzazione][1]

Lo screenshot seguente mostra un esempio della pagina di accesso di Office 365 in un computer desktop **dopo** una personalizzazione:

![Pagina di accesso di Office 365 dopo la personalizzazione][2]

La schermata seguente mostra un esempio della pagina di accesso di Office 365 in un dispositivo mobile **prima** di una personalizzazione:

![Pagina di accesso di Office 365 prima della personalizzazione][3]

La schermata seguente mostra un esempio della pagina di accesso di Office 365 in un dispositivo mobile **dopo** una personalizzazione:

![Pagina di accesso di Office 365 dopo la personalizzazione][4]

Quando si ridimensiona la finestra del browser, l'immagine di grandi dimensioni, come quella mostrata sopra, viene spesso ritagliata in base alle diverse proporzioni dello schermo. Tenendo presente questo aspetto, è consigliabile cercare di mantenere gli elementi visivi principali dell'immagine in modo che appaiano nell'angolo superiore sinistro (o superiore destro per le lingue da destra a sinistra). Questo è importante perché il ridimensionamento viene eseguito in genere dall'angolo inferiore destro verso quello superiore sinistro oppure dal basso verso l'alto.

La figura seguente mostra il modo in cui l'immagine viene ritagliata quando si ridimensiona la finestra del browser verso sinistra:

![][6]

Ecco come appare l'immagine dopo aver ridimensionato la finestra del browser dal basso verso l'alto:

![][7]

## <a name="what-elements-on-the-page-can-i-customize"></a>Quali elementi della pagina è possibile personalizzare?
Ecco gli elementi che è possibile personalizzare nella pagina di accesso:

![][5]

| Elemento della pagina | Posizione nella pagina |
|:--- | --- |
| Logo banner |Visualizzato in alto a destra nella pagina. Sostituisce il logo visualizzato dal sito di destinazione a cui si accede, ad esempio Office 365 o Azure. |
| Immagine di grandi dimensioni/colore di sfondo |Visualizzati a sinistra nella pagina. Sostituisce l'immagine visualizzata dal sito di destinazione a cui si accede. Il colore di sfondo può essere visualizzato al posto dell'immagine di grandi dimensioni in caso di connessioni con larghezza di banda ridotta o su schermi stretti. |
| Mantieni l'accesso |Visualizzato sotto la casella di testo Password. |
| Testo pagina di accesso |Visualizzato sopra il piè di pagina quando è necessario fornire informazioni utili prima di un accesso con un account aziendale o dell'istituto di istruzione. Ad esempio, è possibile includere il numero di telefono dell'help desk o una nota legale. |

> [!NOTE]
> Tutti gli elementi sono facoltativi. Se, ad esempio, si specifica un logo del banner senza immagine di grandi dimensioni, la pagina di accesso mostra il logo e l'immagine per il sito di destinazione, ovvero l'immagine dell'autostrada californiana di Office 365.
>
>

Nella pagina di accesso, la casella di controllo **Mantieni l'accesso** consente a un utente di rimanere connesso quando chiude e riapre il browser. Non influisce sulla durata della sessione. È possibile nascondere la casella di controllo nella pagina di accesso di Azure Active Directory.

La casella di controllo viene selezionata o meno a seconda dell'impostazione di **Nascondi l'opzione Mantieni l'accesso**.

![][9]

Per nascondere la casella di controllo, configurare questa impostazione come **Nascosta**.

> [!NOTE]
> Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dalla possibilità per gli utenti di selezionare questa casella. Se si configura questa impostazione come nascosta, gli utenti potrebbero visualizzare prompt aggiuntivi e imprevisti con una richiesta di accesso.
>
>

È anche possibile localizzare tutti gli elementi della pagina. Dopo aver configurato un set di elementi di personalizzazione "predefinito", è possibile configurare altre versioni per impostazioni locali diverse. È anche possibile combinare e abbinare diversi elementi. Ad esempio, è possibile:

* Creare un'immagine di grandi dimensioni "predefinita" adatta per tutte le impostazioni cultura, quindi creare versioni specifiche per l'inglese e il francese. Quando si impostano i browser su una di queste due lingue, viene visualizzata l'immagine specifica, mentre per tutte le altre lingue viene visualizzata l'illustrazione predefinita.
* Configurare logo diversi per l'organizzazione, ad esempio una versione giapponese o ebraica.

## <a name="access-panel-page-customization"></a>Personalizzazione del pannello di accesso
Il pannello di accesso è essenzialmente una pagina del portale per l'accesso rapido alle app cloud per cui è stato concesso l'accesso dall'amministratore. In questa pagina le app vengono visualizzate come icone applicazione su cui è possibile fare clic.

La schermata seguente mostra un esempio di pagina del pannello di accesso dopo la personalizzazione.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Configurare la directory con informazioni personalizzate distintive dell'azienda
È possibile configurare un set predefinito di elementi personalizzabili per ogni directory nel portale di Azure classico. Dopo aver salvato gli elementi predefiniti, un amministratore può aggiungere versioni localizzate di ogni elemento per lingue/impostazioni locali diverse. Tutti gli elementi personalizzabili sono facoltativi.

Se, ad esempio, si configura un logo del banner predefinito senza immagine di grandi dimensioni, nella pagina di accesso il logo viene visualizzato nell'angolo superiore destro. Tuttavia, viene visualizzata l'immagine predefinita del sito.

Si immagini la configurazione seguente:

* Un logo del banner predefinito e testo della pagina di accesso in inglese
* Testo della pagina di accesso specifica della lingua per il tedesco

Se la lingua preferita è il tedesco, viene visualizzato il logo del banner predefinito, ma il testo è in tedesco.

Benché sia tecnicamente possibile configurare un set diverso per ogni lingua supportata da Azure AD, è preferibile mantenere ridotto il numero di variazioni, per motivi di manutenzione e prestazioni.

> [!IMPORTANT]
> Yammer mostra la pagina di accesso personalizzata di Azure AD solo dopo l'accesso dell'utente. L'utente vede prima la pagina di accesso a Office 365 generica e poi la pagina personalizzata.   
 
 
**Per aggiungere informazioni personalizzate distintive della società alla directory, seguire questa procedura:**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore della directory da personalizzare.
2. Selezionare la directory da personalizzare.
3. Sulla barra degli strumenti in alto fare clic su **Configura**.
4. Fare clic su **Modifica personalizzazione**.
5. Modificare gli elementi da personalizzare. Tutti i campi sono facoltativi.
6. Fare clic su **Save**.

Può trascorrere fino a un'ora prima che qualsiasi nuova modifica apportata per la personalizzazione della pagina di accesso venga visualizzata.

**Per aggiungere informazioni personalizzate distintive della società specifiche per una lingua, seguire questa procedura:**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore della directory da personalizzare.
2. Selezionare la directory da personalizzare.
fs3. Sulla barra degli strumenti in alto fare clic su **Configura**.
4. Fare clic su **Modifica personalizzazione**.
5. Fare clic su **Aggiungi impostazioni di personalizzazione per una lingua specifica**.
6. Selezionare la lingua per cui si vuole personalizzare il logo, quindi fare clic su **Avanti**.
7. Modificare solo gli elementi per cui si vogliono configurare sostituzioni specifiche della lingua. Tutti i campi sono facoltativi. Se un campo viene lasciato vuoto, al suo posto viene visualizzato il valore personalizzato predefinito oppure il valore predefinito Microsoft, se non è configurato alcun valore personalizzato predefinito.
8. Fare clic su **Save**.

**Per rimuovere informazioni personalizzate distintive della società dalla directory, seguire questa procedura:**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore della directory da personalizzare.
2. Selezionare la directory da personalizzare.
3. Sulla barra degli strumenti in alto fare clic su **Configura**.
4. Fare clic su **Modifica personalizzazione**.
5. Nella pagina Modifica personalizzazione selezionare **Modifica impostazioni di personalizzazione esistenti** e quindi passare alla pagina successiva.
6. A seconda degli elementi che si vuole rimuovere, eseguire una o più delle operazioni seguenti:

    a. In **Logo banner** selezionare **Rimuovi logo caricato**.

    b. In **Logo icona** selezionare **Rimuovi logo caricato**.

    c. Rimuovere il testo da tutte le caselle di testo.

    d. Fare clic su **Next**.

    e. Rimuovere il testo da tutte le caselle di testo.
7. Fare clic su **Salva** per rimuovere gli elementi.
8. Se necessario, fare di nuovo clic su **Modifica personalizzazione** e ripetere gli stessi passaggi per tutte le personalizzazioni specifiche della lingua da rimuovere.
    Tutte le impostazioni di personalizzazione sono state rimosse quando il modulo **Modifica personalizzazione predefinita** visualizzato facendo clic su **Modifica personalizzazione** non contiene impostazioni esistenti configurate.

## <a name="testing-and-examples"></a>Test ed esempi
È consigliabile tentare con un tenant di prova prima di apportare modifiche nell'ambiente di produzione.

**Per verificare se la personalizzazione è stata applicata:**

1. Aprire una sessione del browser in incognito o InPrivate.
2. Passare a https://outlook.com/contoso.com, sostituendo contoso.com con il dominio personalizzato.

Questo metodo funziona con i domini simili a contoso.onmicrosoft.com.

Per semplificare la creazione di set di personalizzazione efficaci, Microsoft ha personalizzato le due pagine di accesso fittizie seguenti:

* [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
* [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Per testare impostazioni specifiche della lingua, è necessario modificare le preferenze per la lingua predefinite nel Web browser scegliendo una lingua impostata nella personalizzazione. In Internet Explorer si configura questa opzione nel menu **Opzioni Internet** .

## <a name="customizable-elements"></a>Elementi personalizzabili
Alcuni elementi personalizzabili in Azure AD prevedono più casi di utilizzo. I logo aziendali possono essere configurati una volta per ogni directory e vengono usati sia nella pagina di accesso sia nel pannello di accesso. Alcuni elementi personalizzabili sono specifici solo della pagina di accesso. La tabella seguente contiene informazioni per i diversi elementi personalizzabili.

| Nome | Descrizione | Vincoli | Consigli |
| --- | --- | --- | --- |
| Logo banner |Il logo del banner viene visualizzato nella pagina di accesso e nel pannello di accesso. |<p>JPG o PNG</p><p>60x280 pixel</p><p>10 KB</p> |<p>Usare il logo completo dell'organizzazione, inclusi il simbolo e il logotipo</p><p>Mantenere un'altezza inferiore a 30 pixel per evitare l'introduzione di barre di scorrimento nei dispositivi mobili</p><p>Non superare 4 KB</p><p>Usare un'immagine PNG trasparente (non dare per scontato che la pagina di accesso abbia sempre uno sfondo bianco)</p> |
| Logo icona |Attualmente non usato nella pagina di accesso. In futuro, potrebbe essere usato per sostituire il simbolo generico dell'account aziendale o dell'istituto di istruzione in punti diversi dell'esperienza. |<p>JPG o PNG</p><p>120x120 pixel</p><p>10 KB</p> |<p>Mantenere semplice (evitare testo troppo piccolo), in quanto l'immagine potrebbe essere ridimensionata fino al 50% |
| </p> | | | |
| Etichetta nome utente pagina di accesso |Attualmente non usata nella pagina di accesso. In futuro, questo testo potrebbe essere usato per sostituire la stringa generica dell'account aziendale o dell'istituto di istruzione in diversi punti dell'esperienza. Il testo può essere impostato su qualcosa come "Account Contoso" o "ID Contoso". |<p>Testo Unicode, fino a 50 caratteri</p><p>Solo testo normale, senza collegamenti o tag HTML</p> |<p>Mantenere breve e semplice</p><p>Chiedere agli utenti in che modo chiamano in genere l'account aziendale o dell'istituto di istruzione ricevuto</p> |
| Testo pagina di accesso |Questo "boilerplate" appare sotto il modulo della pagina di accesso e può essere usato per comunicare istruzioni aggiuntive o indicazioni su come ottenere informazioni e supporto tecnico. |<p>Testo Unicode, fino a 256 caratteri</p><p>Solo testo normale, senza collegamenti o tag HTML</p> |Mantenere il numero di caratteri inferiore a 250 (circa tre righe di testo) |
| Illustrazione pagina di accesso |Questa illustrazione è un'immagine di grandi dimensioni visualizzata nella pagina di accesso, a sinistra del modulo della pagina di accesso. |<p>JPG o PNG</p><p>1420x1200</p><p>500 KB</p> |<p>1420x1200 pixel</p><p>Importante: mantenere il file quanto più piccolo possibile, idealmente meno di 200 KB. Se l'immagine è troppo grande, influisce negativamente sulle prestazioni della pagina di accesso quando non è memorizzata nella cache.</p><p>Questa immagine viene spesso ritagliata, in base alle diverse proporzioni dello schermo. Mantenere gli elementi visivi principali nell'angolo superiore sinistro (superiore destro per le lingue da destra a sinistra), perché man mano che la finestra del browser si riduce il ridimensionamento avviene dall'angolo inferiore destro verso l'angolo superiore sinistro.</p> |
| Colore di sfondo della pagina di accesso |Il colore di sfondo della pagina di accesso viene usato nell'area a sinistra del modulo della pagina di accesso. |Deve essere un colore RGB in formato esadecimale (esempio: #FFFFFF) |<p>Il colore di sfondo può essere visualizzato al posto dell'immagine di grandi dimensioni in caso di connessioni con larghezza di banda ridotta</p><p>È consigliabile scegliere il colore primario del logo del banner</p> |

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

