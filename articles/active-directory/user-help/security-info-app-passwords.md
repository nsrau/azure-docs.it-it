---
title: Configurare le password delle app usando le informazioni di sicurezza - Azure Active Directory | Microsoft Docs
description: Usare le informazioni di sicurezza per configurare password generate automaticamente (password delle app), distinte dalle password normali, da usare con ognuna delle app non basate su browser.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 211e282dc4334753b90d050dc82c8bf35ad145cd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826208"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Gestire le password delle app usando le informazioni di sicurezza (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Alcune app non basate su browser, come Outlook 2010, non supportano la verifica in due passaggi. L'assenza di supporto significa che se si usa la verifica in due passaggi, l'app non funzionerà. Per ovviare a questo problema, è possibile creare una password generata automaticamente da usare con ogni app non basata su browser, diversa dalla password normale.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password per le app vengono generate automaticamente e immesse una sola volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

- Usare una sola password per dispositivo, non per app. Ad esempio, creare una singola password per tutte le app del portatile e un'altra per tutte le app sul PC desktop.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-and-delete-app-passwords-using-security-info"></a>Creare ed eliminare password delle app tramite le informazioni di sicurezza

Se si usa la verifica in due passaggi con l'account aziendale o dell'istituto di istruzione e l'amministratore ha disattivato l'esperienza delle informazioni di sicurezza, è possibile creare ed eliminare le password delle app tramite il portale App personali.

Se l'amministratore non ha attivato l'esperienza delle informazioni di sicurezza, è necessario seguire le istruzioni e le informazioni nella sezione [Gestire le password delle app per la verifica in due passaggi](multi-factor-authentication-end-user-app-passwords.md).

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Per creare password delle app tramite il portale App personali

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Passare a myapps.microsoft.com, selezionare il proprio nome nell'angolo superiore destro della pagina e quindi selezionare **Profilo**.

3. Nell'area **Gestisci account** selezionare **Modifica le info di sicurezza**.

    ![Schermata Profilo con il collegamento Modifica le info di sicurezza evidenziato](media/security-info/security-info-profile.png)

4. Nella schermata **Proteggi l'account** selezionare **Aggiungi info di sicurezza**.

    ![Schermata delle informazioni di sicurezza con le informazioni esistenti, modificabili](media/security-info/security-info-edit-add-info.png)

5. Nella schermata **Aggiungi info di sicurezza** selezionare **Password dell'app**.

6. Nella schermata **Crea la password dell'app** digitare un nome per la password dell'app e quindi selezionare **Avanti**.

    ![Schermata in cui si assegna il nome alla password dell'app](media/security-info/security-info-name-app-password.png)

7. Selezionare **Copia** per copiare la password negli Appunti e quindi selezionare **Avanti**.

    ![Schermata con la password dell'app per la copia](media/security-info/security-info-create-app-password.png)
    
8. Assicurarsi che la password dell'app sia visualizzata nella schermata **Proteggi l'account**.

    ![Schermata Proteggi l'account con la password dell'app](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Per eliminare password delle app tramite il portale App personali

1. Nella schermata **Proteggi l'account** selezionare la **X** accanto alla password dell'app da eliminare.

    ![Schermata Proteggi l'account, eliminare la password dell'app](media/security-info/security-info-keep-secure-delete-app-password.png)

2. Nella schermata **Elimina la password dell'applicazione** selezionare **Elimina**.

    ![Schermata Elimina la password dell'applicazione](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Per informazioni più generali sulle informazioni di sicurezza e sulle operazioni possibili, vedere [Security info overview](user-help-security-info-overview.md) (Panoramica delle informazioni di sicurezza) 