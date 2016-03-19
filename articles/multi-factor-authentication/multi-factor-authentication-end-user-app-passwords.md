<properties 
	pageTitle="Che cosa sono le password per le app in Azure MFA?" 
	description="Questa pagina consente agli utenti di comprendere il ruolo e la funzione delle password per le app in Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>



# Che cosa sono le password per le app in Azure Multi-Factor Authentication?

Alcune applicazioni non basate su browser, come il client di posta elettronica di Apple, utilizzano Exchange Active Sync e attualmente non supportano l'autenticazione a più fattori. L’autenticazione a più fattori viene abilitata per singolo utente. Questo implica che, se un utente è stato abilitato per l'autenticazione a più fattori e tenta di utilizzare applicazioni non basate su browser, non potrà farlo. La password dell’app consente tale operazione.

>[AZURE.NOTE] Autenticazione moderna per i client di Office 2013
>
> I Client di Office 2013 (tra cui Outlook) ora supportano i nuovi Protocolli di autenticazione e possono essere abilitati per supportare la Multi-Factor Authentication. Ciò significa che una volta attivati, le password della app non vengono richieste per l'utilizzo con i Client di Office 2013. Per ulteriori informazioni, vedere l’[anteprima dell’annuncio pubblico per l’autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Come utilizzare le password delle app

Di seguito sono riportate alcune considerazioni da tenere presente per l’utilizzo delle password delle app.

- La password effettiva viene generata in automatico e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
- Al momento esiste un limite di 40 password per utente. Se si tenta di creare una password dopo che è stato raggiunto il limite, verrà richiesto di eliminare una delle password esistenti prima della creazione della nuova.
- È consigliabile creare password delle app per ogni dispositivo e non per ogni applicazione. Ad esempio, è possibile creare una sola password per il computer portatile e usarla per tutte le applicazioni su tale computer.
- Al primo accesso, viene fornita una password per le app. Se sono necessarie password aggiuntive, è possibile crearle.
 
![Impostazione](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Dopo aver creato una password dell’app, è possibile utilizzarla al posto della password originale con le applicazioni non basate su browser. Ad esempio, quando si utilizza l'autenticazione a più fattori e il client di posta elettronica di Apple sul telefono. Utilizzare la password dell’app per ignorare l’autenticazione a più fattori e continuare a lavorare.

## Creazione ed eliminazione delle password di app
Durante l'accesso iniziale, viene fornita una password dell’app che è possibile utilizzare. Inoltre, è inoltre possibile creare ed eliminare le password per le app in un secondo momento. La procedura dipende dalla modalità di utilizzo dell’autenticazione a più fattori. Scegliere l'opzione più appropriata.

Come utilizzare l’autenticazione a più fattori|Descrizione
:------------- | :------------- | 
[Utilizzo con Office 365](#creating-and-deleting-app-passwords-with-office-365)| Significa che si desidera creare le password dell’app tramite il portale di Office 365.
[Non so](#creating-and-deleting-app-passwords-with-myapps-portal)|Significa che è possibile creare password dell’app tramite [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Utilizzarlo con Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Significa che è possibile creare le password dell’app tramite il portale di Azure.

## Creazione ed eliminazione di password delle app con Office 365 

Se si usa Multi-Factor Authentication con Office 365, è consigliabile creare ed eliminare password per le app tramite il portale di Office 365.

### Per creare password per le app nel portale di Office 365
--------------------------------------------------------------------------------

1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell’angolo superiore destro selezionare il widget e scegliere le impostazioni di Office 365.
3. Fare clic su Verifica aggiuntiva di sicurezza.
4. A destra, fare clic sul collegamento con il testo **Aggiorna i numeri di telefono utilizzati per la protezione dell'account**. ![Impostazione](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata la pagina che consentirà di modificare le impostazioni. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**.
7. Fare clic su **Crea**. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**. ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiare la password per l'app negli Appunti, quindi incollarla nell'app. ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### Per eliminare le password di app tramite il portale di Office 365
--------------------------------------------------------------------------------


1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell’angolo superiore destro selezionare il widget e scegliere le impostazioni di Office 365.
3. Fare clic su Verifica aggiuntiva di sicurezza.
4. A destra, fare clic sul collegamento con il testo **Aggiorna i numeri di telefono utilizzati per la protezione dell'account**. ![Impostazione](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata la pagina che consentirà di modificare le impostazioni. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**.
7. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**. ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confermare l'eliminazione facendo clic su **Sì**. ![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**. ![Chiudi](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Creazione ed eliminazione di password delle app con il portale Myapps.
Se non si è certi di come utilizzare Multi-Factor Authentication, è sempre possibile creare ed eliminare le password delle app tramite il portale Myapps.

### Per creare una password di app tramite il portale Myapps

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Nella parte superiore della schermata selezionare il profilo.
3. Selezionare Verifica aggiuntiva di sicurezza. ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata la pagina che consentirà di modificare le impostazioni. ![Impostazione](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**.
6. Fare clic su **Crea**. ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**. ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiare la password per l'app negli Appunti, quindi incollarla nell'app. ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### Per eliminare una password di app tramite il portale Myapps

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Nella parte superiore della schermata selezionare il profilo.
3. Selezionare Verifica aggiuntiva di sicurezza. ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata la pagina che consentirà di modificare le impostazioni. ![Impostazione](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**.
6. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**. ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confermare l'eliminazione facendo clic su **Sì**. ![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**. ![Chiudi](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Creazione di password per le app nel portale di Azure

Se si usa Multi-Factor Authentication con Azure, è consigliabile creare password per le app tramite il portale di Azure.

### Per creare password per le app nel portale di Azure

1. Accedere al portale di gestione di Azure.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore della pagina di verifica selezionare le password per le app.
4. Fare clic su **Crea**.
5. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
6. Copiare la password per l'app negli Appunti, quindi incollarla nell'app. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### Per eliminare password per le app nel portale di Azure

1. Accedere al portale di gestione di Azure.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**.
4. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.
5. Confermare l'eliminazione facendo clic su **Sì**.
6. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**. ![Chiudi](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_0218_2016-->