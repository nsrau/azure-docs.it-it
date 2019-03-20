---
title: Come gestire le password di app - Azure Active Directory | Microsoft Docs
description: Informazioni sulle password dell'app e quali operazioni sono usati per quanto riguarda la verifica in due passaggi.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47128bce8dbbee1b28f6b0a3a5783e5ccee501bb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177399"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gestire le password per le app per la verifica in due passaggi

Alcune app non basate su browser, come Outlook 2010, non supportano la verifica in due passaggi. L'assenza di supporto significa che se si usa la verifica in due passaggi, l'app non funzionerà. Per ovviare a questo problema, è possibile creare una password generata automaticamente da usare con ogni app non basata su browser, diversa dalla password normale.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password per le app vengono generate automaticamente e immesse una sola volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

- Usare una sola password per dispositivo, non per app. Ad esempio, creare una singola password per tutte le app del portatile e un'altra per tutte le app sul PC desktop.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="where-to-create-and-delete-your-app-passwords"></a>Dove creare ed eliminare password per le app

Una password per le app viene assegnata durante la registrazione iniziale della verifica in due passaggi. Se è necessaria più di una password, è possibile crearne altre, in base al modo in cui si usa la verifica in due passaggi:

- **La verifica in due passaggi viene usata con l'account aziendale o dell'istituto di istruzione e il portale MyApps.** Creare ed eliminare le password per le app tramite le istruzioni fornite nella sezione [Creare ed eliminare password per le app tramite il portale di MyApps](#create-and-delete-app-passwords-using-the-myapps-portal) di questo articolo. Per altre informazioni sul portale MyApps e su come usarlo, vedere [Informazioni sul portale MyApps in Azure Active Directory](active-directory-saas-access-panel-introduction.md).

- **La verifica in due passaggi viene usata con l'account aziendale o dell'istituto di istruzione e il portale Office 365.** Creare ed eliminare le password per le app tramite le istruzioni fornite nella sezione [Creare ed eliminare password per le app tramite il portale di Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) di questo articolo.

- **La verifica in due passaggi viene usata con l'account Microsoft personale.** Creare ed eliminare le password per le app tramite la pagina [Informazioni di base sulla sicurezza](https://account.microsoft.com/account/) con l'account Microsoft personale. Per altre informazioni, vedere l'articolo [Password per app e verifica in due passaggi](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification).

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Creare ed eliminare password per le app tramite il portale MyApps
È possibile creare ed eliminare password per le app anche tramite il portale MyApps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Per creare una password di app tramite il portale Myapps

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Fare clic sul nome in alto a destra e scegliere **Profilo**.

3. Selezionare **Verifica aggiuntiva di sicurezza**.

   ![Selezionare Verifica aggiuntiva di sicurezza: schermata](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Selezionare **Password dell'app**.

   ![Selezionare le password per le app: schermata](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Fare clic su **Create**(Crea).

6. Digitare un nome per la password e quindi selezionare **Avanti**.

7. Copiare la password per l'app negli Appunti, quindi incollarla nell'app.
   
    ![Creare una password di app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Per eliminare una password di app tramite il portale MyApps

1. Passare al profilo e quindi selezionare **Verifica aggiuntiva di sicurezza**.

2. Selezionare **Password dell'app** e quindi **Elimina** accanto alla password che si vuole eliminare.

   ![Eliminare una password di app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Selezionare **Sì** per confermare l'eliminazione della password e quindi **Chiudi**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Creare ed eliminare password per le app tramite il portale di Office 365

Se si usa la verifica in due passaggi con l'account aziendale o dell'istituto di istruzione e le app di Office 365, è possibile creare ed eliminare le password per le app tramite il portale di Office 365. Il numero massimo di password per le app consentito è 40 per volta. Se è necessaria un'altra password per le app dopo avere raggiunto il limite, sarà necessario eliminare una delle password esistenti.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Per creare password per le app tramite il portale di Office 365

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Passare a [ https://portal.office.com ](https://portal.office.com), selezionare il **impostazioni** icona nell'angolo superiore destro di **portale di Office 365** pagina e quindi espandere **aggiuntiva di sicurezza verifica**.

    ![Portale di Office che mostra l'area Verifica aggiuntiva di sicurezza espansa](media/security-info/security-info-o365password.png)

3. Selezionare il testo **Create and manage app passwords** (Crea e gestisci password per le app) per aprire la pagina **Password dell'app**.

4. Selezionare **Crea**, digitare un nome descrittivo per l'app per cui è necessaria la password e quindi selezionare **Avanti**.

5. Selezionare **Copia password negli Appunti** e quindi **Chiudi**.

6. Usare la password copiata per accedere all'app non basata su browser. Questa password deve essere immessa una sola volta e viene memorizzata per il futuro.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Per eliminare le password di app tramite il portale di Office 365

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Passare a [ https://portal.office.com ](https://portal.office.com), selezionare il **impostazioni** icona nell'angolo superiore destro di **portale di Office 365** pagina e quindi selezionare **aggiuntiva di sicurezza verifica**.

3. Selezionare il testo **Create and manage app passwords** (Crea e gestisci password per le app) per aprire la pagina **Password dell'app**.

4. Selezionare **Elimina** per la password da eliminare, selezionare **Sì** nella casella di conferma e quindi selezionare **Chiudi**.

    La password viene eliminata.

5. Completare i passaggi per la creazione di una password per le app per crearne una nuova.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se le app per le password non funzionano correttamente

Assicurarsi di aver digitato correttamente la password. Se si è certi di aver immesso la password correttamente, è possibile provare ad accedere di nuovo e creare una nuova password. Se nessuna di queste opzioni risolve il problema, contattare il supporto tecnico dell'azienda per richiedere l'eliminazione delle password per le app esistenti, in modo da poterne creare di nuove. 

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

- Provare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) per verificare gli accessi con le notifiche delle app, invece di ricevere messaggi o chiamate.
