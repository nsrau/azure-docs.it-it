---
title: Aggiungere informazioni personalizzate distintive dell&quot;azienda alla pagina di accesso e al pannello di accesso in Azure Active Directory
description: Informazioni su come aggiungere informazioni personalizzate distintive dell&quot;azienda alla pagina di accesso di Azure e al pannello di accesso
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso
Molte organizzazioni vogliono applicare un aspetto coerente ai siti Web e servizi che gestiscono. Azure Active Directory offre questa possibilità permettendo ai professionisti IT di personalizzare l'aspetto delle pagine Web indicate di seguito, con immagini e logo aziendali:

* **Pagina di accesso**: si tratta della pagina visualizzata da dipendenti e utenti guest quando accedono a Office 365 o ad altre applicazioni che usano Azure AD.
* **Pagina del pannello di accesso**: il pannello di accesso è un portale basato sul Web che consente di visualizzare e avviare applicazioni basate sul cloud per le quali l'amministratore di Azure AD ha concesso i diritti di accesso. Il pannello di accesso è disponibile all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com).

Questo argomento illustra come personalizzare la pagina di accesso e il pannello di accesso.

> [!NOTE]
> * La possibilità di aggiungere informazioni personalizzate distintive dell'azienda è disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory o si ha una licenza di Office 365. Per altre informazioni, vedere le edizioni di Azure Active Directory.
> 
> * Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il forum di Azure Active Directory.


## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso
In genere gli utenti interagiscono con la pagina di accesso di Azure AD quando provano ad accedere a servizi e applicazioni cloud sottoscritti dall'organizzazione.

Se sono state apportate modifiche di personalizzazione alla pagina di accesso, potrebbe trascorrere fino a un'ora prima che vengano visualizzate dagli utenti finali.

Le informazioni personalizzate distintive dell'azienda vengono visualizzate nella pagina di accesso di Azure AD quando gli utenti accedono a un URL specifico del tenant, ad esempio https://outlook.com/contoso.com.

Quando un utente visita un servizio usando un URL generico, ad esempio www.office.com, la pagina di accesso non contiene informazioni personalizzate distintive dell'azienda perché il sistema non riconosce l'utente. Le informazioni personalizzate distintive dell'azienda vengono visualizzate non appena si immette l'ID utente o si seleziona un'icona utente.

> [!NOTE]
> * Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Active Directory** > **Directory** > **Domini** del portale di Azure classico dopo la configurazione della personalizzazione.
> * La personalizzazione della pagina di accesso non si applica alla pagina di accesso dell'account Microsoft personale. Se i dipendenti o agli utenti guest aziendali accedono con un account Microsoft personale, la pagina di accesso non rifletterà le informazioni personalizzate distintive dell'azienda.
>

Gli screenshot riportati di seguito illustrano la personalizzazione delle pagine di accesso.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Scenario 1: un dipendente di Contoso visita un URL di app generico, ad esempio www.office.com

In questo esempio un utente di Contoso accede a un'applicazione per dispositivi mobili o a un'applicazione Web tramite un URL generico. La figura a sinistra rappresenta sempre l'app, mentre il riquadro di interazione a destra viene aggiornato con le informazioni personalizzate di Contoso, se appropriato.

![Pagina di accesso di Office 365 prima e dopo la personalizzazione][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Scenario 2: un dipendente di Contoso passa all'app di Contoso limitata ai soli utenti interni

In questo esempio un utente di Contoso accede a un'app interna usando un URL specifico dell'azienda. La figura a sinistra rappresenta il marchio aziendale, ovvero Contoso. Il riquadro di interazione a destra è bloccato su Contoso e guida i dipendenti durante l'accesso.

![Pagina di accesso dell'app con restrizioni][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Scenario 3: un dipendente di Contoso passa a un'app di Contoso aperta agli utenti esterni

In questo esempio l'utente accede a un'applicazione line-of-business di Contoso, ma potrebbe non essere un dipendente di Contoso. La figura a sinistra rappresenta il proprietario della risorsa, ovvero Contoso, come nello scenario \#2 precedente. In questo caso, però, il riquadro di interazione a destra non è bloccato su Contoso. Ciò significa che possono accedere anche gli utenti esterni.

![Accesso a un'app aperta][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Scenario 4: un utente guest di Fabrikam passa a un'app di Contoso aperta agli utenti esterni

In questo esempio un utente di Contoso accede a un'app interna usando un URL specifico dell'azienda. La figura a sinistra rappresenta il marchio aziendale, ovvero Contoso. Il riquadro di interazione a destra è bloccato su Contoso e guida i dipendenti durante l'accesso.

![Accesso come utente esterno][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Quali elementi della pagina è possibile personalizzare?

Ecco gli elementi che è possibile personalizzare nella pagina di accesso:

![][5]

| Elemento della pagina | Posizione nella pagina |
|:--- | --- |
| Logo banner | Visualizzato in alto a destra nella pagina. Sostituisce il logo dell'app dopo che viene determinata l'organizzazione dell'utente, in genere dopo che viene immesso il nome utente. |
| Immagine di sfondo | Visualizzata come immagine con smarginatura sul lato sinistro della pagina di accesso. Sostituisce l'immagine dell'app per scenari di accesso con tenant, ovvero quando gli utenti accedono a un'app pubblicata dalla propria organizzazione o da un'organizzazione di cui sono utenti guest.<br>Per le connessioni a larghezza di banda ridotta, l'immagine di sfondo viene sostituita con un colore di sfondo. Negli schermi stretti, come quelli dei telefoni, l'immagine non viene visualizzata.<br>Quando l'utente ridimensiona il browser, l'immagine di sfondo viene ritagliata. Nel progettare l'immagine è consigliabile tenere gli elementi visivi principali nell'angolo superiore sinistro, in modo che non vengano ritagliati. | 
| Casella di controllo "Mantieni l'accesso" | Visualizzata sotto la casella **Password**. |
| Testo della pagina di accesso | Testo boilerplate da visualizzare sopra il piè di pagina. Può essere usato per fornire informazioni utili agli utenti, ad esempio il numero di telefono dell'help desk o una nota legale. |

> [!NOTE]
> Tutti gli elementi sono facoltativi. Se, ad esempio, si specifica un logo del banner ma non un'immagine di sfondo, la pagina di accesso mostra il logo e l'immagine per il sito di destinazione, ad esempio l'immagine dell'autostrada californiana di Office 365.
>

Nella pagina di accesso personalizzata la casella di controllo **Mantieni l'accesso** consente all'utente di rimanere connesso quando chiude e riapre il browser, senza influire sulla durata della sessione.

La casella di controllo viene visualizzata o meno a seconda dell'impostazione di **Nascondi l'opzione Mantieni l'accesso**.

![Impostazione "Nascondi l'opzione Mantieni l'accesso"][6]

Per nascondere la casella di controllo, configurare questa impostazione come **Nascosta**.

> [!NOTE]
> Alcune funzionalità di SharePoint Online e di Office 2010 dipendono dalla possibilità per gli utenti di selezionare questa casella. Se si configura questa impostazione come nascosta, gli utenti potrebbero visualizzare richieste di accesso aggiuntive e impreviste.
>
>

È anche possibile localizzare tutti gli elementi della pagina. Dopo aver configurato un set di elementi di personalizzazione "predefinito", è possibile configurare altre versioni per impostazioni locali diverse. È anche possibile combinare e abbinare diversi elementi. Ad esempio, è possibile:

* Creare un'immagine "predefinita" adatta per tutte le impostazioni cultura, quindi creare versioni specifiche per l'inglese e il francese. Quando si impostano i browser su una di queste due lingue, viene visualizzata l'immagine specifica, mentre per tutte le altre lingue viene visualizzata l'illustrazione predefinita.
* Configurare loghi diversi per l'organizzazione, ad esempio una versione giapponese o ebraica.

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


## <a name="customizable-elements"></a>Elementi personalizzabili
I loghi aziendali vengono usati per la pagina di accesso e per il pannello di accesso, mentre gli altri elementi vengono usati solo nella pagina di accesso. La tabella seguente contiene informazioni per i diversi elementi personalizzabili.

| Nome | Descrizione | Vincoli | Consigli |
| --- | --- | --- | --- |
| Logo banner |Il logo del banner viene visualizzato nella pagina di accesso e nel pannello di accesso. |<p>JPG o PNG</p><p>60x280 pixel</p><p>10 KB</p> |<p>Usare il logo completo dell'organizzazione, inclusi il simbolo e il logotipo</p><p>Mantenere un'altezza inferiore a 30 pixel per evitare l'introduzione di barre di scorrimento nei dispositivi mobili</p><p>Non superare 4 KB</p><p>Usare un'immagine PNG trasparente (non dare per scontato che la pagina di accesso abbia sempre uno sfondo bianco)</p> |
| Logo icona | Attualmente non usato. |<p>JPG o PNG</p><p>120x120 pixel</p><p>10 KB</p> |<p>Mantenere semplice (evitare testo troppo piccolo), in quanto l'immagine potrebbe essere ridimensionata fino al 50% |
| </p> | | | |
| Etichetta del nome utente di accesso | Attualmente non usata. |<p>Testo Unicode, fino a 50 caratteri</p><p>Solo testo normale, senza collegamenti o tag HTML</p> |<p>Mantenere breve e semplice</p><p>Chiedere agli utenti in che modo chiamano in genere l'account aziendale o dell'istituto di istruzione ricevuto</p> |
| Testo boilerplate della pagina di accesso |Questo testo boilerplate viene visualizzato sotto il modulo della pagina di accesso e può essere usato per comunicare istruzioni aggiuntive o indicazioni su come ottenere informazioni e supporto tecnico. |<p>Testo Unicode, fino a 256 caratteri</p><p>Solo testo normale, senza collegamenti o tag HTML</p> |Mantenere il numero di caratteri inferiore a 250 (circa tre righe di testo) |
| Immagine di sfondo della pagina di accesso | Immagine di grandi dimensioni visualizzata nella parte sinistra della pagina di accesso, a destra per le lingue RTL, quando gli utenti accedono a URL specifici del tenant. |<p>JPG o PNG</p><p>1420x1200</p><p>500 KB</p> |<p>1420x1200 pixel</p><p>Importante: mantenere il file quanto più piccolo possibile, idealmente meno di 200 KB. Se l'immagine è troppo grande, influisce negativamente sulle prestazioni della pagina di accesso quando non è memorizzata nella cache.</p><p>Questa immagine viene quasi sempre ritagliata in base alle diverse proporzioni degli schermi. Mantenere gli elementi visivi principali nell'angolo superiore sinistro.</p> |
| Colore di sfondo della pagina di accesso | Per le connessioni a larghezza di banda ridotta, questo colore a tinta unita viene usato al posto dell'immagine di sfondo. | Deve essere un colore RGB in formato esadecimale, ad esempio: \#FFFFFF. | È consigliabile scegliere il colore primario del logo del banner. |

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

