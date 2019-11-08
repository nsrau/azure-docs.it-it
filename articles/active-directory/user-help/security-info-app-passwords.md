---
title: Creare password di app in info di sicurezza (anteprima)-Azure AD
description: Configurare le password generate automaticamente (password dell'app) da usare con qualsiasi app non basate su browser o qualsiasi app che non supporta la verifica a due fattori nell'organizzazione. Questa password dell'app è distinta da una password normale e può essere configurata dalla pagina Info di sicurezza.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb26d90772fc9e3e3e506946363c76cf02e6b2ef
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820333"
---
# <a name="create-app-passwords-from-your-security-info-preview-page"></a>Creare password di app dalla pagina delle info di sicurezza (anteprima)

Alcune app, come Outlook 2010, non supportano la verifica in due passaggi. L'assenza di supporto significa che se si usa la verifica in due passaggi nell'organizzazione, l'app non funzionerà. Per ovviare a questo problema, è possibile creare una password generata automaticamente da usare con ogni app non basata su browser, diversa dalla password normale.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>L'amministratore potrebbe non consentire di usare le password delle app. Se l'opzione **Password dell'app** non è visibile, non è disponibile nell'organizzazione.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password dell'app vengono generate automaticamente e devono essere create e immesse una volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Creare nuove password per l'app

Se si usa la verifica in due passaggi con l'account aziendale o dell'istituto di istruzione e l'amministratore ha disattivato l'esperienza delle informazioni di sicurezza, è possibile creare ed eliminare le password delle app usando la pagina **Info di sicurezza**.

>[!Note]
>Se l'amministratore non ha attivato l'esperienza delle informazioni di sicurezza, è necessario seguire le istruzioni e le informazioni nella sezione [Gestire le password delle app per la verifica in due passaggi](multi-factor-authentication-end-user-app-passwords.md).

### <a name="to-create-a-new-app-password"></a>Per creare una nuova password per l'app

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myprofile.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Informazioni di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **Password dell'app** dall'elenco a discesa e quindi selezionare **Aggiungi**.

    ![Finestra di dialogo Aggiungi un metodo, con Password dell'app selezionata](media/security-info/securityinfo-myprofile-addpassword.png)

4. Digitare il nome dell'app che richiede la password e quindi selezionare **Avanti**.

    ![Pagina Password dell'app, con il nome dell'app](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copiare il testo dalla casella **Password**, incollare la password nell'apposita area dell'app (in questo esempio Outlook 2010) e quindi selezionare **Fine**.

    ![Pagina Password dell'app, con il nome dell'app](media/security-info/securityinfo-myprofile-password-copytext.png)

    La password viene aggiunta ed è possibile accedere all'app a partire da questo momento.

## <a name="delete-your-app-passwords"></a>Elimina le password dell'app

Se non è più necessario usare un'app che richiede una password, è possibile eliminare la password dell'app associata. L'eliminazione della password dell'app libera uno dei posti disponibili per la password dell'app da usare in futuro.

>[!Important]
>Se si elimina per errore una password dell'app, non è possibile annullare l'azione. Sarà necessario creare una nuova password dell'app e reimmetterla nell'app, seguendo la procedura illustrata nella sezione [Creare nuove password per l'app](#create-new-app-passwords) di questo articolo.

### <a name="to-delete-an-app-password"></a>Per eliminare una password di app

1. Nella pagina **Info di sicurezza** selezionare il collegamento **Elimina** accanto all'opzione **Password dell'app** per l'app specifica.

    ![Collegamento per eliminare il metodo basato sulle password dell'app dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selezionare **Sì** nella finestra di dialogo di conferma per eliminare la **password dell'app**. La password dell'app, dopo essere stata eliminata, viene rimossa dalle informazioni di sicurezza e non viene più visualizzata nella pagina **Info di sicurezza**.

## <a name="for-more-information"></a>Per altre informazioni

- Per altre informazioni sulla pagina **Info di sicurezza** e su come configurarla, vedere [Panoramica delle informazioni di sicurezza](user-help-security-info-overview.md)
