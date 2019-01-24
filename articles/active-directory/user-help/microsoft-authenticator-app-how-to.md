---
title: Introduzione all'app Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire l'aggiornamento alla versione più recente di Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 43c09bc01e0ed5bd9093242db9183883e402a6f1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825579"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introduzione all'app Microsoft Authenticator

L'app Microsoft Authenticator impedisce l'accesso non autorizzato agli account e permette di arrestare transazioni fraudolente offrendo un livello di sicurezza aggiuntivo per l'account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, o per l'account Microsoft personale, ad esempio alain@outlook.com.

Quando si usa l'app per la verifica in due passaggi, questa può operare in due modi diversi:

- **Notifica.** L'app invia una notifica al dispositivo. Assicurarsi che la notifica sia corretta e quindi selezionare **Verifica**. Se non si riconosce la notifica, selezionare **Nega**. Dopo aver fatto clic su **Nega** è anche possibile contrassegnare la richiesta come fraudolenta.

- **Codice di verifica.** Dopo aver digitato il nome utente e la password, è possibile aprire l'app e copiare il codice di verifica fornito nella schermata **Account** nella schermata di accesso. Il codice di verifica funge da seconda forma di autenticazione.

## <a name="opt-in-for-two-step-verification"></a>Fornire il consenso esplicito per la verifica in due passaggi

L'organizzazione stabilisce se permettere l'uso della verifica in due passaggi con l'account aziendale o dell'istituto di istruzione. L'amministratore comunicherà i metodi di verifica che è necessario configurare e usare. Per altre informazioni, vedere [Quali sono i vantaggi di Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md).

Per l'account Microsoft personale, è possibile configurare la verifica in due passaggi personalmente. Per informazioni e istruzioni più dettagliate, vedere [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

È possibile usare l'app Microsoft Authenticator anche con account non Microsoft. Con questi account la funzionalità potrebbe avere un nome diverso, ma dovrebbe essere facile da individuare tra le impostazioni di sicurezza o di accesso. Per altre informazioni su come configurare questi account non Microsoft, vedere i [video del supporto tecnico Microsoft](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Installare l'app

L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071). Per ottenere un'esperienza ottimale, è consigliabile attivare la ricezione di notifiche nell'app quando viene richiesto. 

## <a name="add-accounts-to-the-app"></a>Aggiungere account all'app

È possibile aggiungere account aziendali o dell'istituto di istruzione o account personali all'app Microsoft Authenticator. 

### <a name="add-a-personal-microsoft-account"></a>Aggiungere un account Microsoft personale

Per un account Microsoft personale (che consente di accedere ad Outlook.com, Xbox, Skype e così via), è sufficiente accedere al proprio account nell'app Microsoft Authenticator.

### <a name="add-a-work-or-school-account"></a>Aggiungere un account aziendale o dell'istituto di istruzione

1. Se possibile, passare alla schermata [Verifica aggiuntiva di sicurezza](https://aka.ms/mfasetup) in un altro PC o dispositivo. Per informazioni su come visualizzare questa schermata, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) oppure contattare l'amministratore.

    >[!Note]
    >Se l'amministratore ha attivato l'esperienza di anteprima delle informazioni di sicurezza, è possibile seguire le istruzioni contenute nella sezione [Configurare le informazioni di sicurezza per usare un'app di autenticazione](security-info-setup-auth-app.md).

2. Selezionare la casella accanto ad **App Authenticator** e quindi selezionare **Configura**.

    ![Pulsante Configura nella schermata delle impostazioni di verifica della sicurezza](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    Viene visualizzata la schermata **Configurare l'app per dispositivi mobili** con un codice a matrice di cui eseguire la scansione con l'app di autenticazione.

    ![Schermata contenente il codice a matrice](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Aprire l'app Microsoft Authenticator. Nella schermata **Account** selezionare **Aggiungi account** e quindi selezionare **Account aziendale o dell'istituto di istruzione**.

4. Usare la fotocamera del dispositivo per eseguire la scansione del codice a matrice e quindi selezionare **Fine** per chiudere la schermata del codice a matrice.

    >[!Note]
    >Se la fotocamera non funziona correttamente, è possibile [immettere manualmente il codice a matrice e l'URL](#add-an-account-to-the-app-manually).

    La schermata **Account** dell'app mostra il nome dell'account e un codice di verifica a sei cifre. Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi, per impedirne l'uso una seconda volta.  

    ![Schermata Account](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Aggiungere manualmente un account all'app

1. Passare alla schermata **Verifica aggiuntiva di sicurezza**. Per informazioni su come accedere a questa schermata, vedere [Changing your security settings](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) (Modifica delle impostazioni di sicurezza).

2. Selezionare la casella accanto ad **App Authenticator** e quindi selezionare **Configura**.

    Viene visualizzata la schermata **Configurare l'app per dispositivi mobili**.

3. Copiare le informazioni relative a codice e URL dalla schermata **Configurare l'app per dispositivi mobili** in modo da poterle digitare manualmente nello scanner a matrice.

4. Aprire l'app Microsoft Authenticator. Nella schermata **Account** selezionare **Aggiungi account** e quindi selezionare **Account aziendale o dell'istituto di istruzione**.

5. Nella schermata dello scanner a matrice selezionare **In alternativa, immettere il codice manualmente**.

    ![Schermata per la scansione di un codice a matrice](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Digitare il codice e l'URL dalla schermata con il codice a matrice nella schermata **Aggiungi un account** e quindi selezionare **Fine**.

    ![Schermata per l'immissione del codice e dell'URL](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    La schermata **Account** dell'app mostra il nome dell'account e un codice di verifica a sei cifre. Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi, per impedirne l'uso una seconda volta.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Uso delle funzionalità di riconoscimento facciale o dell'impronta digitale del dispositivo

L'organizzazione potrebbe richiedere un PIN per completare la verifica dell'identità. È possibile configurare l'app Microsoft Authenticator in modo da usare le funzionalità di riconoscimento facciale o dell'impronta digitale del dispositivo invece di un PIN. È possibile configurare questa impostazione la prima volta che si usa l'app di autenticazione per verificare l'account, selezionando l'opzione per l'uso delle funzionalità biometriche del dispositivo come identificazione al posto del PIN.

## <a name="use-the-app-when-you-sign-in"></a>Usare l'app durante l'accesso

Dopo aver aggiunto gli account all'app, è possibile usare l'app per accedere agli account.

Se si è scelto di usare codici di verifica nell'app, questi verranno visualizzati nella pagina **Account**. Questi codici cambiano ogni 30 secondi, in modo da riceverne uno nuovo ogni volta che è necessario. Tuttavia, non è necessario eseguire altre operazioni finché non si accede e non viene chiesto di immettere un codice di verifica.

## <a name="next-steps"></a>Passaggi successivi

- In caso di domande generali sull'app, vedere [Domande frequenti su Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- Per altre informazioni sulla verifica in due passaggi, vedere [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- Per altri dettagli sulle informazioni di sicurezza, vedere [Gestire le informazioni di sicurezza](security-info-manage-settings.md)
