<properties 
	pageTitle="Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso" 
	description="Argomento che illustra come molte aziende possano applicare un look e un aspetto coerenti a tutti i siti Web e servizi che gestiscono, in modo da evitare la confusione degli utenti finali ogni volta che usano tali siti." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso

> [AZURE.NOTE]
> 
- La possibilità di aggiungere informazioni personalizzate distintive dell'azienda è disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
- Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina che usano l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

Molte aziende vogliono applicare un look e un aspetto coerenti a tutti i siti Web e servizi che gestiscono, in modo da evitare la confusione degli utenti finali ogni volta che questi usano tali siti. Azure Active Directory offre questa funzionalità permettendo di personalizzare l'aspetto delle pagine Web rivolte all'utente finale indicate di seguito, in modo da poter includere il logo e le combinazioni di colori dell'azienda:

- **Pagina di accesso**: a questa pagina vengono reindirizzati gli utenti dopo che accedono a Office 365 o ad altre applicazioni moderne e basate sul Web che usano Azure AD come provider di identità. La maggior parte degli utenti interagirà con questa pagina si per passare da Individuazione area di autenticazione principale, che permette al sistema di reindirizzare gli utenti federati ai propri servizi token di sicurezza locali (come ADFS), sia per immettere le proprie credenziali.

- **Pannello di accesso**: questa pagina è un portale basato sul Web che permette a un utente finale il cui account aziendale o dell'istituto di istruzione si trova in una directory di Azure AD di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD gli ha concesso l'accesso. Al pannello di accesso possono accedere tutti gli utenti dell'organizzazione tramite l'indirizzo myapps.microsoft.com.

## Personalizzazione della pagina di accesso

Poiché la pagina di accesso è in genere la pagina Web usata più di frequente dagli utenti finali che devono accedere con un browser alle app e ai servizi cloud sottoscritti dall'organizzazione, è essenziale che abbia un aspetto corretto. Se si vuole usare l'esperienza della pagina di accesso senza personalizzazione, non è necessario eseguire alcuna operazione.

### Quanto tempo è necessario perché le modifiche di personalizzazione siano visibili nelle pagine di accesso?

Può trascorrere fino a un'ora prima che gli utenti visualizzino qualsiasi nuova modifica apportata per la personalizzazione della pagina di accesso.

### In quali casi gli utenti visualizzano una pagina di accesso personalizzata?

Gli utenti visualizzano una pagina di accesso personalizzata quando visitano un servizio con un URL specifico del tenant, come https://outlook.com/**contoso**.com o https://mail.**contoso**.com (se è stato creato un record CNAME).

Se gli utenti visitano un servizio con URL non specifici del tenant, come https://mail.office365.com, visualizzeranno una pagina di accesso non personalizzata. La pagina di accesso verrà aggiornata per mostrare la personalizzazione dopo che gli utenti immettono il proprio ID utente o selezionano un'icona utente.

> [AZURE.NOTE]
> 
- Il nome di dominio deve essere visualizzato come "Attivo" nella sezione **Active Directory** > **Directory** > **Domini** del portale di gestione di Azure una volta configurata la personalizzazione.
- La personalizzazione della pagina di accesso non si applica alla pagina di accesso degli utenti di Microsoft. Questo significa che gli utenti che accedono con un account Microsoft personale (in precedenza chiamato Windows Live ID) potrebbero visualizzare un elenco personalizzato di icone utente reso disponibile da Azure AD, ma le informazioni di personalizzazione distintive dell'organizzazione non verranno applicate alla pagina di accesso degli account Microsoft.

### Che cosa viene visualizzato dagli utenti finali una volta personalizzata la pagina di accesso?

Se si vuole mostrare il marchio, i colori e altri elementi personalizzabili dell'azienda in questa pagina, vedere le immagini seguenti per capire la differenza tra le due esperienze.

Quando un utente prova ad accedere da un computer desktop, ecco un esempio di cosa visualizza nella pagina di accesso di Office 365 *prima* della personalizzazione:

![][1]

Ed ecco cosa visualizza lo stesso utente *dopo* la personalizzazione:

![][2]

Quando un utente prova ad accedere da un dispositivo mobile, ecco un esempio di cosa visualizza nella pagina di accesso di Office 365 *prima* della personalizzazione:

![][3]

Ed ecco cosa visualizza lo stesso utente *dopo* la personalizzazione:

![][4]

### Quali elementi della pagina è possibile personalizzare?

Ecco gli elementi che è possibile personalizzare nella pagina di accesso:

![][5]

 Elemento della pagina | Posizione nella pagina
	------------- | -------------
Logo banner | Visualizzato in alto a destra nella pagina. Sostituisce il logo normalmente visualizzato dal sito di destinazione cui accedono gli utenti, ad esempio Office 365 o Azure.
Immagine di grandi dimensioni/colore di sfondo | Visualizzati a sinistra nella pagina. Sostituiscono l'immagine normalmente visualizzata dal sito di destinazione cui accedono gli utenti. Il colore di sfondo può essere visualizzato al posto dell'immagine di grandi dimensioni in caso di connessioni con larghezza di banda ridotta o su schermi molto stretti.
Testo pagina di accesso | Visualizzato sopra il piè di pagina quando è necessario fornire informazioni utili per gli utenti prima che accedano con il proprio account aziendale o dell'istituto di istruzione. Ad esempio, è possibile includere il numero di telefono dell'help desk o una nota legale.

> [AZURE.NOTE]Tutti gli elementi sono facoltativi. Se, ad esempio, si specifica un logo del banner, ma nessuna immagine di grandi dimensioni, la pagina di accesso mostrerà il logo e l'immagine per il sito di destinazione, ovvero l'immagine dell'autostrada californiana di Office 365.

È anche possibile localizzare tutti gli elementi della pagina. Dopo aver configurato un set di elementi di personalizzazione "predefinito", è possibile configurare anche altre versioni per impostazioni locali diverse. È anche possibile combinare e abbinare diversi elementi. Ad esempio, è possibile:

- Creare un'immagine di grandi dimensioni "predefinita" adatta per tutte le impostazioni cultura, quindi creare versioni specifiche per l'inglese e il francese. Gli utenti i cui browser sono impostati su una di queste due lingue visualizzeranno l'immagine specifica, mentre tutti gli altri visualizzeranno quella predefinita.
- Configurare logo diversi per l'organizzazione, ad esempio una versione giapponese o ebraica.

### Come apparirà l'immagine dopo aver ridimensionato il browser?

Durante il ridimensionamento della finestra del browser, l'immagine di grandi dimensioni, come quella mostrata sopra, verrà quasi sempre ritagliata in base alle diverse proporzioni dello schermo. Tenendo presente questo aspetto, è consigliabile cercare di mantenere gli elementi visivi principali dell'immagine in modo che appaiano sempre nell'angolo superiore sinistro (o superiore destro per le lingue da destra a sinistra). Questo è importante perché il ridimensionamento viene eseguito in genere dall'angolo inferiore destro verso quello superiore sinistro oppure dal basso verso l'alto.

La figura seguente mostra il modo in cui l'immagine viene ritagliata quando si ridimensiona la finestra del browser verso sinistra:

![][6]

Ed ecco come appare l'immagine dopo aver ridimensionato la finestra del browser dal basso verso l'alto:

![][7]

## Personalizzazione del pannello di accesso

Il pannello di accesso è essenzialmente una pagina del portale per tutti gli utenti finali che devono accedere rapidamente, facendo clic sulle icone applicazione, alle diverse app cloud cui è stato loro concesso l'accesso. Se si vuole usare l'esperienza del pannello di accesso senza personalizzazione, non è necessario eseguire alcuna operazione.

### Che cosa viene visualizzato dagli utenti finali una volta personalizzato il pannello di accesso?

![][8]

## Configurare la directory con informazioni personalizzate distintive dell'azienda

È possibile configurare un set predefinito di elementi personalizzabili per ogni directory nel portale di gestione. Dopo aver salvato gli elementi predefiniti, un amministratore può anche scegliere di aggiungere versioni localizzate di ogni elemento per lingue/impostazioni locali diverse. Tutti gli elementi personalizzabili sono facoltativi.

Se, ad esempio, si configura un logo del banner predefinito senza immagine di grandi dimensioni, nella pagina di accesso il logo apparirà nell'angolo superiore destro, indipendentemente da come verrà visualizzata l'immagine predefinita del sito. Se si configura un logo del banner predefinito e il testo della pagina di accesso in inglese, ma si configura anche un testo della pagina di accesso specifico per la lingua tedesca, gli utenti la cui preferenza per la lingua è impostata sul tedesco visualizzeranno il logo del banner predefinito, ma il testo in tedesco. Benché sia tecnicamente possibile configurare un set diverso per ogni lingua supportata da Azure AD, è preferibile mantenere ridotto il numero di variazioni, per motivi di manutenzione e prestazioni.

Per aggiungere informazioni personalizzate distintive dell'azienda alla directory:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) come amministratore della directory che si vuole personalizzare.
2. Selezionare la directory che si vuole personalizzare.
3. Selezionare la scheda **Configura** e quindi fare clic su **Modifica personalizzazione**.
4. Modificare gli elementi che si vuole personalizzare. Notare che tutti i campi sono facoltativi.
5. Fare clic su **Salva**.

Può trascorrere fino a un'ora prima che gli utenti visualizzino qualsiasi nuova modifica apportata per la personalizzazione della pagina di accesso.

Per aggiungere informazioni personalizzate distintive dell'azienda specifiche della lingua:

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com) selezionare **Modifica personalizzazione** nella scheda **Configura**.
2. Selezionare **Aggiungi personalizzazione per una lingua specifica**, selezionare la lingua per cui si vuole personalizzare il logo e quindi fare clic su **Avanti**.
3. Modificare solo gli elementi per cui si vuole configurare sostituzioni specifiche della lingua. Notare che tutti i campi sono facoltativi. Se un campo viene lasciato vuoto, al suo posto verrà visualizzato il valore personalizzato predefinito (o quello predefinito Microsoft se non è configurato alcun valore predefinito).
4. Fare clic su **Salva**.

Per rimuovere informazioni personalizzate distintive dell'azienda dalla directory:

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com) selezionare **Modifica personalizzazione** nella scheda **Configura**.
2. Nella pagina Modifica personalizzazione selezionare **Modifica impostazioni di personalizzazione esistenti** e quindi passare alla pagina successiva.
3. A seconda degli elementi che si vuole rimuovere, eseguire una o più delle operazioni seguenti:
	1. Per Logo banner, fare clic sulla casella di controllo accanto a **Rimuovi logo caricato**.
    2. Per Logo icona, fare clic sulla casella di controllo accanto a **Rimuovi logo caricato**.
    3. Per Etichetta nome utente pagina di accesso, cancellare tutto il testo.
    4. Per Testo pagina di accesso, cancellare tutto il testo.
    5. Per Illustrazione pagina di accesso, fare clic sulla casella di controllo accanto a **Rimuovi illustrazione**.
    6. Per Colore di sfondo della pagina di accesso, cancellare tutto il testo.
4. Fare clic su **Salva** per rimuovere gli elementi.
5. Se necessario, fare di nuovo clic su **Modifica personalizzazione** e ripetere gli stessi passaggi per tutte le personalizzazioni specifiche della lingua da rimuovere. Tutte le impostazioni di personalizzazione sono state rimosse quando dopo aver fatto clic su **Modifica personalizzazione**, il modulo **Modifica personalizzazione predefinita** non contiene più alcuna impostazione configurata.

## Test ed esempi

È consigliabile tentare con un tenant di prova prima di apportare modifiche nell'ambiente di produzione. Il modo più semplice per verificare se le personalizzazioni sono state applicate consiste nell'aprire una sessione del browser InPrivate o in incognito e quindi visitare la pagina https://outlook.com/contoso.com, sostituendo contoso.com con il dominio personalizzato. Notare che questo metodo funziona con i domini simili a contoso.onmicrosoft.com.

Per semplificare la creazione di set di personalizzazione efficaci, Microsoft ha personalizzato le due pagine di accesso fittizie seguenti:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Per testare impostazioni specifiche della lingua, sarà necessario modificare le preferenze per la lingua predefinite nel Web browser scegliendo una lingua impostata nella personalizzazione. In Internet Explorer questa opzione viene configurata nel menu **Opzioni Internet**.

## Elementi personalizzabili

Alcuni elementi personalizzabili in Azure AD prevedono più casi di utilizzo. I logo aziendali possono essere configurati una volta per ogni directory e vengono usati sia nella pagina di accesso sia nel pannello di accesso, mentre alcuni elementi personalizzabili sono specifici solo della pagina di accesso. La tabella seguente contiene informazioni per i diversi elementi personalizzabili.

Nome | Descrizione | Vincoli | Consigli
	------------- | ------------- | ------------- | -------------
Logo banner | Il logo del banner viene visualizzato nella pagina di accesso e nel pannello di accesso. | <p>JPG o PNG</p><p>60x280 pixel</p><p>10 KB</p> | <p>Usare il logo completo dell'organizzazione, inclusi il simbolo e il logotipo</p><p>Mantenere un'altezza inferiore a 30 pixel per evitare l'introduzione di barre di scorrimento nei dispositivi mobili</p><p>Mantenere una dimensione inferiore a 4 KB</p><p>Usare un file PNG trasparente (non dare per scontato che la pagina di accesso abbia sempre uno sfondo bianco)</p>
Logo icona | Attualmente non usato nella pagina di accesso. In futuro, potrebbe essere usato per sostituire il simbolo generico dell'account aziendale o dell'istituto di istruzione in punti diversi dell'esperienza. | <p>JPG o PNG</p><p>120x120 pixel</p><p>10 KB</p> | <p>Mantenere semplice (evitare testo troppo piccolo), in quanto l'immagine potrebbe essere ridimensionata fino al 50%
</p> |
Etichetta nome utente pagina di accesso | Attualmente non usata nella pagina di accesso. In futuro, questo testo potrebbe essere usato per sostituire la stringa generica dell'account aziendale o dell'istituto di istruzione in diversi punti dell'esperienza. Il testo può essere impostato su qualcosa come "Account Contoso" o "ID Contoso". | <p>Testo Unicode, fino a 50 caratteri</p><p>Solo testo normale (senza collegamenti o tag HTML)</p> | <p>Mantenere breve e semplice</p><p>Chiedere agli utenti in che modo chiamano in genere l'account aziendale o dell'istituto di istruzione ricevuto.</p>
Testo pagina di accesso | Questo "boilerplate" appare sotto il modulo della pagina di accesso e può essere usato per comunicare istruzioni aggiuntive o indicazioni su come ottenere informazioni e supporto tecnico. | <p>Testo Unicode, fino a 256 caratteri</p><p>Testo normale (senza collegamenti o tag HTML)</p> | Mantenere il numero di caratteri inferiore a 250 (circa tre righe di testo)
Illustrazione pagina di accesso | Questa illustrazione è un'immagine di grandi dimensioni visualizzata nella pagina di accesso, a sinistra del modulo della pagina di accesso. | <p>JPG o PNG</p><p>1420x1200 pixel</p><p>500 KB</p> | <p>1420x1200 pixel</p><p>Importante: mantenere il file quanto più piccolo possibile, idealmente meno di 200 KB. Se l'immagine è troppo grande, avrà impatto sulle prestazioni della pagina di accesso quando non è memorizzata nella cache</p><p>Questa immagine VERRÀ quasi sempre ritagliata, in base alle diverse proporzioni dello schermo. Mantenere gli elementi visivi principali nell'angolo superiore sinistro (superiore destro per le lingue da destra a sinistra), perché il ridimensionamento, man mano che la finestra del browser si riduce, avverrà dall'angolo inferiore destro verso l'angolo superiore sinistro.</p>
Colore di sfondo della pagina di accesso | Il colore di sfondo della pagina di accesso viene usato nell'area a sinistra del modulo della pagina di accesso. Il colore è visibile anche quando non è presente alcuna immagine della pagina di accesso. | Deve essere un colore RGB in formato esadecimale (esempio: #FFFFFF) | <p>Il colore di sfondo può essere visualizzato al posto dell'immagine di grandi dimensioni in caso di connessioni con larghezza di banda ridotta</p><p>È consigliabile scegliere il colore primario del logo del banner</p>


## Passaggi successivi

- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

 

<!---HONumber=62-->