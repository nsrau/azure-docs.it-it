---
title: 'Procedura: usare le password per le app in Azure MFA | Microsoft Docs'
description: Questa pagina consente agli utenti di comprendere il ruolo e la funzione delle password per le app in Azure MFA.
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
editor: yossib
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: d3b9dd38c993b7f04dfc419ba06be0783cb66eb2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Che cosa sono le password per le app in Azure Multi-Factor Authentication?
Alcune applicazioni non basate su browser, come il client di posta elettronica di Apple, utilizzano Exchange Active Sync e attualmente non supportano l'autenticazione a più fattori. L’autenticazione a più fattori viene abilitata per singolo utente.  Ciò significa che l'utente non può usare l'autenticazione a più fattori se:

- L'utente è stato abilitato per la modalità Multi-Factor Authentication.
- L'utente sta tentando di usare un'app non basata su browser.

Una password di app consente all'utente di usare l'app.

Dopo avere creato una password dell'app, è possibile usarla al posto della password originale con le applicazioni non basate su browser. Quando ci si registra per la verifica in due passaggi, si indica a Microsoft di non consentire l'accesso con la password a un utente che non possa eseguire anche la seconda verifica. Il client di posta elettronica di Apple sul telefono non può accedere perché per questo client non è possibile richiedere la verifica in due passaggi. Per risolvere questo problema, creare una password di app più sicura che non si usa quotidianamente. Le password di app sono destinate solo a tutte le app che non supportano la verifica in due passaggi. Usare la password per l'app per ignorare l’autenticazione a più fattori e proseguire.


> [!NOTE]
> I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Le password di app non vengono richieste per l'uso con i client Office 2013.  Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Come utilizzare le password delle app
Informazioni importanti sulle password di app:

* La password per l'app non viene creata dall'utente, Vengono generate automaticamente.
* Al momento esiste un limite di 40 password per utente. 
* Se si tenta di creare una password di app dopo avere raggiunto il limite, sarà necessario eliminare una delle password di app esistenti prima di crearne una nuova.
* Usare una sola password per dispositivo, non per applicazione. Ad esempio, è possibile creare una sola password per il computer portatile e usarla per tutte le applicazioni su tale computer. Quindi, creare una seconda password per le app da usare per tutte le app sul desktop. 
* La prima volta che si esegue la registrazione per la verifica in due passaggi viene comunicata una sola password per le app.  Se sono necessarie password aggiuntive, è possibile crearle.



## <a name="creating-and-deleting-app-passwords"></a>Creazione ed eliminazione delle password di app
Durante l'accesso iniziale, viene fornita una password dell'app che è possibile usare.  È possibile anche creare ed eliminare le password per le app in un secondo momento. La procedura di eliminazione delle password di app dipende dalla modalità di utilizzo dell'autenticazione a più fattori. Rispondere alle domande seguenti per determinare il luogo in cui è possibile gestire le password per le app: 

1. La verifica in due passaggi viene usata per l'account Microsoft personale? Se sì, è consigliabile consultare l'articolo [Password per app e verifica in due passaggi](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) per avere altre informazioni. Altrimenti passare alla seconda domanda.

2. Pertanto, la verifica in due passaggi viene usata per un account aziendale o dell'istituto di istruzione. È usata per accedere alle applicazioni di Office 365? Se sì, è consigliabile consultare [Creare una password dell'app per Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) per avere maggiori informazioni. Altrimenti passare alla terza domanda. 

3. La verifica in due passaggi viene usata con Microsoft Azure? Se sì, passare alla sezione [Gestione delle password per le app nel Portale di Azure](#manage-app-passwords-in-the-Azure-portal) di questo articolo. Altrimenti passare alla quarta domanda.

4. Non si è sicuri su dove venga usata la verifica in due passaggi? Passare alla sezione [Gestione delle password per le app nel Portale MyApps](#manage-app-passwords-with-the-myapps-portal) di questo articolo. 


## <a name="manage-app-passwords-in-the-azure-portal"></a>Gestione delle password per le app nel Portale di Azure
Se si usa la verifica in due passaggi con Azure, è consigliabile creare password per le app tramite il Portale di Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Per creare password dell'app nel portale di Azure
1. Accedere al portale di Microsoft Azure classico.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore della pagina di verifica selezionare le password dell'app.
4. Fare clic su **Crea**.
5. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
6. Copiare la password per l'app negli Appunti, quindi incollarla nell'app.
   
   ![Cloud](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Per eliminare password per le app nel portale di Azure
1. Accedere al portale di Microsoft Azure classico.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore, accanto alla verifica aggiuntiva di sicurezza, fare clic su **Password dell'app.**
4. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.
5. Confermare l'eliminazione facendo clic su **Sì**.
6. Dopo che la password dell’app è stata eliminata, è possibile fare clic su **Chiudi**.


## <a name="manage-app-passwords-with-the-myapps-portal"></a>Gestione delle password per le app nel portale MyApps.
Se non si è certi di come utilizzare Multi-Factor Authentication, è sempre possibile creare ed eliminare le password per le app tramite il portale Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Per creare una password di app tramite il portale Myapps
1. Effettuare l'accesso ad [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Fare clic sul nome in alto a destra e scegliere **Profilo**.
3. Selezionare **Verifica aggiuntiva di sicurezza**.
   ![Selezionare Verifica aggiuntiva di sicurezza: schermata](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selezionare **Password delle app**.
   ![Selezionare le password per le app: schermata](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Fare clic su **Crea**.
6. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
7. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.
   ![Creare la password di un'app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Per eliminare una password di app tramite il portale Myapps
1. Effettuare l'accesso ad [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore selezionare il profilo.
3. Selezionare **Verifica aggiuntiva di sicurezza**.

   ![Selezionare Verifica aggiuntiva di sicurezza: schermata](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selezionare **Password delle app**.

   ![Selezionare le password per le app: schermata](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Accanto alla password dell’app che si desidera rimuovere, selezionare **Elimina**.

   ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Confermare che si desidera eliminare la password facendo clic su **Sì**.
7. Dopo che la password dell’app è stata eliminata, è possibile fare clic su **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

- Provare l'[app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) per verificare gli accessi con le notifiche delle app, invece di ricevere messaggi o chiamate. 
