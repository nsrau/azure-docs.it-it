---
title: Che cosa sono le password per le app in Azure MFA? | Documentazione Microsoft
description: Questa pagina consente agli utenti di comprendere il ruolo e la funzione delle password per le app in Azure MFA.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: c05377a2fd837a8b477e7bce3db7b4df61846f8e


---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Che cosa sono le password per le app in Azure Multi-Factor Authentication?
Alcune applicazioni non basate su browser, come il client di posta elettronica di Apple, utilizzano Exchange Active Sync e attualmente non supportano l'autenticazione a più fattori. L’autenticazione a più fattori viene abilitata per singolo utente. Questo implica che, se un utente è stato abilitato per l'autenticazione a più fattori e tenta di utilizzare applicazioni non basate su browser, non potrà farlo. La password dell’app consente tale operazione.

> [!NOTE]
> Autenticazione Moderna per i Client di Office 2013
>
> I Client di Office 2013 (tra cui Outlook) ora supportano i nuovi Protocolli di autenticazione e possono essere abilitati per supportare la Multi-Factor Authentication.  Ciò significa che una volta attivati, le password della app non vengono richieste per l'utilizzo con i Client di Office 2013.  Per ulteriori informazioni, vedere l’ [anteprima dell’annuncio pubblico per l’autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>
>

## <a name="how-to-use-app-passwords"></a>Come utilizzare le password delle app
Di seguito sono riportate alcune considerazioni da tenere presente per l’utilizzo delle password delle app.

* La password effettiva viene generata in automatico e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
* Al momento esiste un limite di 40 password per utente. Se si tenta di creare una password dopo che è stato raggiunto il limite, verrà richiesto di eliminare una delle password esistenti prima della creazione della nuova.
* È consigliabile creare password delle app per ogni dispositivo e non per ogni applicazione. Ad esempio, è possibile creare una sola password per il computer portatile e usarla per tutte le applicazioni su tale computer.
* Al primo accesso, viene fornita una password per le app.  Se sono necessarie password aggiuntive, è possibile crearle.

![Configurazione](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Dopo aver creato una password dell’app, è possibile utilizzarla al posto della password originale con le applicazioni non basate su browser.  Ad esempio, quando si utilizza l'autenticazione a più fattori e il client di posta elettronica di Apple sul telefono.  Utilizzare la password dell’app per ignorare l’autenticazione a più fattori e continuare a lavorare.

## <a name="creating-and-deleting-app-passwords"></a>Creazione ed eliminazione delle password di app
Durante l'accesso iniziale, viene fornita una password dell’app che è possibile utilizzare.  Inoltre, è inoltre possibile creare ed eliminare le password per le app in un secondo momento.  La procedura dipende dalla modalità di utilizzo dell’autenticazione a più fattori.  Scegliere l'opzione più appropriata.

| Come utilizzare l’autenticazione a più fattori | Descrizione |
|:--- |:--- |
| [Utilizzo con Office 365](#creating-and-deleting-app-passwords-with-office-365) |Significa che si desidera creare le password dell’app tramite il portale di Office 365. |
| [Non so](#creating-and-deleting-app-passwords-with-myapps-portal) |Significa che è possibile creare password dell’app tramite [https://myapps.microsoft.com](https://myapps.microsoft.com) |
| [Utilizzarlo con Microsoft Azure](#create-app-passwords-in-the-azure-portal) |Significa che è possibile creare le password dell’app tramite il portale di Azure. |

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Creazione ed eliminazione di password delle app con Office 365
Se si usa Multi-Factor Authentication con Office 365, è consigliabile creare ed eliminare password per le app tramite il portale di Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Per creare password dell'app nel portale di Office 365
- - -
1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell’angolo superiore destro selezionare il widget e scegliere le impostazioni di Office 365.
3. Fare clic su Verifica aggiuntiva di sicurezza.
4. A destra, fare clic sul collegamento con il testo **Aggiorna i numeri di telefono utilizzati per la protezione dell'account.**
   ![Setup](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata una pagina che consente di modificare le impostazioni.
   ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**
7. Fare clic su **Crea**.
   ![Cloud](./media/multi-factor-authentication-end-user-app-passwords/apppass.png)
8. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
   ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.
   ![Creare la password di un'app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Per eliminare le password di app tramite il portale di Office 365
- - -
1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell’angolo superiore destro selezionare il widget e scegliere le impostazioni di Office 365.
3. Fare clic su Verifica aggiuntiva di sicurezza.
4. A destra, fare clic sul collegamento con il testo **Aggiorna i numeri di telefono utilizzati per la protezione dell'account.**
   ![Setup](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata una pagina che consente di modificare le impostazioni.
   ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**
7. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.
   ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confermare l'eliminazione facendo clic su **Sì**.
   ![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Creazione ed eliminazione di password delle app con il portale Myapps.
Se non si è certi di come utilizzare Multi-Factor Authentication, è sempre possibile creare ed eliminare le password delle app tramite il portale Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Per creare una password di app tramite il portale Myapps
1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore della schermata selezionare il profilo.
3. Selezionare Verifica aggiuntiva di sicurezza.
   ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata una pagina che consente di modificare le impostazioni.
   ![Configurazione](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**
6. Fare clic su **Crea**.
   ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
   ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.
   ![Creare la password di un'app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Per eliminare una password di app tramite il portale Myapps
1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore della schermata selezionare il profilo.
3. Selezionare Verifica aggiuntiva di sicurezza.
   ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata una pagina che consente di modificare le impostazioni.
   ![Configurazione](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**
6. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.
   ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confermare l'eliminazione facendo clic su **Sì**.
   ![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="create-app-passwords-in-the-azure-portal"></a>Creazione di password per le app nel portale di Azure
Se si usa Multi-Factor Authentication con Azure, è consigliabile creare password per le app tramite il portale di Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Per creare password per le app nel portale di Azure
1. Accedere al portale di gestione di Azure.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore della pagina di verifica selezionare le password dell'app.
4. Fare clic su **Crea**.
5. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
6. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.
   ![Cloud](./media/multi-factor-authentication-end-user-app-passwords/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Per eliminare password per le app nel portale di Azure
1. Accedere al portale di gestione di Azure.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore, accanto a verifica aggiuntiva di sicurezza, fare clic su **password delle app**
4. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.
5. Confermare l'eliminazione facendo clic su **Sì**.
6. Una volta che la password dell’app viene eliminata, è possibile fare clic su **Chiudi**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)



<!--HONumber=Dec16_HO4-->


