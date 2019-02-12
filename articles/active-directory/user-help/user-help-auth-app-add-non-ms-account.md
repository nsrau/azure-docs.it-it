---
title: Aggiungere gli account non Microsoft all'app Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Come aggiungere gli account non Microsoft, ad esempio per Google, Facebook o GitHub, all'app Microsoft Authenticator per la verifica a due fattori.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: afcac73af84010f37eccd018f39852712e495b84
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772231"
---
# <a name="add-your-non-microsoft-accounts"></a>Aggiungere gli account non Microsoft
Aggiungere gli account non Microsoft, ad esempio per Google, Facebook o GitHub, all'app Microsoft Authenticator per la verifica a due fattori. L'app Microsoft Authenticator funziona con qualsiasi app che usa la verifica a due fattori e qualsiasi account che supporta gli standard di password monouso basate sul tempo (Time-Based One-Time Passwords, TOTP).

>[!Important]
>Prima di poter aggiungere l'account, è necessario scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, seguire i passaggi descritti nell'articolo [Scaricare e installare l'app](user-help-auth-app-download-install.md).

## <a name="add-personal-accounts"></a>Aggiungere gli account personali
In generale, per tutti gli account personali, è necessario:

1. Accedere all'account personale e quindi attivare la verifica a due fattori tramite il dispositivo o PC.

2. Aggiungere l'account all'app Microsoft Authenticator. Potrebbe essere necessario eseguire una scansione di un codice a matrice come parte di questo processo.

Viene fornito il processo per gli account di Facebook, Google, GitHub e Amazon, ma il processo è lo stesso anche per qualsiasi altra app, ad esempio Instagram, Netflix o Adobe.

## <a name="add-your-google-account"></a>Aggiungere l'account Google
Per aggiungere l'account Google attivare la verifica a due fattori e quindi aggiungere l'account all'app.

### <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nei PC, andare a https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, selezionare **Configurazione iniziale**, quindi verificare la propria identità.

2. Seguire i passaggi nella pagina per attivare la verifica in due passaggi per l'account Google personale.

### <a name="add-your-google-account-to-the-app"></a>Aggiungere l'account Google all'app

1. Nella pagina Google sul PC, andare alla sezione **Configurare secondo passaggio alternativo**, scegliere **Configurazione** dalla sezione dell'**app Authenticator**.

2. Nela pagina **Ottenere i codici dall'app Authenticator**, selezionare **Android** o **iPhone** in base al tipo di dispositivo, quindi fare clic su **Avanti**.

    Si riceve un codice a matrice che è possibile usare per associare automaticamente il proprio account con l'app Microsoft Authenticator. Non chiudere questa finestra.

3. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra e quindi selezionare **Altro account (Google, Facebook, ecc.)**.

4. Usare la fotocamera del dispositivo per eseguire la scansione del codice a matrice dalla pagina **Configura Authenticator** nel PC.

    >[!Note]
    >Se la fotocamera non funziona correttamente, è possibile [immettere manualmente il codice a matrice e l'URL](#add-an-account-to-the-app-manually).

5. Rivedere la pagina **Account** dell'app Microsoft Authenticator nel dispositivo per assicurarsi che le informazioni dell'account siano corrette e che quest'ultimo sia associato a un codice di verifica a sei cifre.

    Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

6. Selezionare **Avanti** nella pagina **Configurare Authenticator** sul PC, digitare il codice di verifica a sei cifre fornito nell'app per l'account Google, quindi selezionare **Verifica**.

7. L'account è verificato ed è possibile selezionare **Operazione completata** per chiudere la pagina **Configurare Authenticator**.

    >[!NOTE]
    >Per altre informazioni sulla verifica a due fattori e l'account Google, vedere [Attivare la verifica in due passaggi](https://support.google.com/accounts/answer/185839) e [Ulteriori informazioni sulla verifica in due passaggi](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Aggiungere il proprio account Facebook
Per aggiungere l'account Facebook attivare la verifica a due fattori e quindi aggiungere l'account all'app.

### <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nel PC, aprire Facebook, selezionare il menu a discesa nell'angolo superiore destro e quindi andare a **Impostazioni** > **Sicurezza e accesso**.

    Verrà visualizzata la pagina **Sicurezza e accesso**.

2. Andare all'opzione **Usa l'autenticazione a due fattori** nella sezione **Autenticazione a due fattori** e quindi selezionare **Modifica**.

    Verrà visualizzata la pagina **Autenticazione a due fattori**.

3. Selezionare **Attiva**.

### <a name="add-your-facebook-account-to-the-app"></a>Aggiungere il proprio account Facebook all'app

1. Nella pagina di Facebook nel PC, passare alla sezione **Aggiungi un backup** e quindi scegliere **Configurazione** dall'area **app Authentication**.

    Si riceve un codice a matrice che è possibile usare per associare automaticamente il proprio account con l'app Microsoft Authenticator. Non chiudere questa finestra.

2. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra e quindi selezionare **Altro account (Google, Facebook, ecc.)**.

3. Usare la fotocamera del dispositivo per eseguire la scansione del codice a matrice dalla pagina **Autenticazione a due fattori** nel PC.

    >[!Note]
    >Se la fotocamera non funziona correttamente, è possibile [immettere manualmente il codice a matrice e l'URL](#add-an-account-to-the-app-manually).

4. Rivedere la pagina **Account** dell'app Microsoft Authenticator nel dispositivo per assicurarsi che le informazioni dell'account siano corrette e che quest'ultimo sia associato a un codice di verifica a sei cifre.

    Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

5. Selezionare **Avanti** nella pagina **Autenticazione a due fattori** sul PC, quindi digitare il codice di verifica a sei cifre fornito nell'app per l'account Facebook.

    L'account è verificato ed è ora possibile usare l'app per verificare l'account.

    >[!NOTE]
    >Per altre informazioni sulla verifica a due fattori e l'account di Facebook, vedere [Cos'è l'autenticazione a due fattori e come funziona?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Aggiungere l'account GitHub
Per aggiungere l'account GitHub attivare la verifica a due fattori e quindi aggiungere l'account all'app.

### <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nel PC, aprire GitHub, selezionare l'immagine dall'angolo superiore destro e quindi selezionare **Impostazioni**.

    Verrà visualizzata la pagina **Autenticazione a due fattori**.

2. Selezionare **Sicurezza** dalla barra laterale **Impostazioni personali** e quindi selezionare **Abilita l'autenticazione a due fattori** dall'area **Autenticazione a due fattori**.

### <a name="add-your-github-account-to-the-app"></a>Aggiungere l'account GitHub all'app

1. Nella pagina **Autenticazione a due fattori** sul PC, selezionare **Configura tramite un'app**.

2. Salvare i codici di ripristino in modo da poter tornare all'account se si perde l'accesso, quindi selezionare **Avanti**. 

    È possibile salvare i codici personali scaricandoli sul proprio dispositivo, stampando una copia cartacea o copiandoli in uno strumento di gestione delle password.

3. Nella pagina **Autenticazione a due fattori**, selezionare **Configura tramite un'app**.

    La pagina cambia per visualizzare un codice a matrice. Non chiudere questa pagina.

4. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra, selezionare **Altro account (Google, Facebook, ecc.)**, quindi selezionare **inserisci questo codice** dal testo nella parte superiore della pagina.

    L'app Microsoft Authenticator non riesce a eseguire la scansione del codice a matrice, pertanto è necessario immettere manualmente il codice.

5. Immettere un **Nome dell'account** (ad esempio, GitHub) e digitare la **Chiave privata** del passaggio 4, quindi selezionare **Fine**.

4. Nella pagina **Autenticatore a due fattori** sul PC, digitare il codice di verifica a sei cifre fornito nell'app per l'account GitHub, quindi selezionare **Abilita**.

    La pagina **Account** dell'app mostra il nome dell'account e un codice di verifica a sei cifre. Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

    >[!NOTE]
    >Per altre informazioni sulla verifica a due fattori e l'account di GitHub, vedere [About two-factor authentication](https://help.github.com/articles/about-two-factor-authentication/) (Informazioni sull'autenticazione a due fattori).

## <a name="add-your-amazon-account"></a>Aggiungere l'account Amazon
Aggiungere l'account Amazon attivando la verifica a due fattori e quindi aggiungendo l'account all'app.

### <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nel PC, aprire Amazon, selezionare **Account ed elenchi** dal menu a discesa e quindi selezionare **Account personale**.

2. Selezionare **Accesso e sicurezza**, accedere al proprio account Amazon e quindi selezionare **Modifica** nell'area **Impostazioni di sicurezza avanzate**.

    Verrà visualizzata la pagina **Impostazioni di sicurezza avanzate**.

3. Selezionare **Attività iniziali**.

4. Selezionare **App Authenticator** dalla pagina **Scegliere come ricevere i codici**.

    La pagina cambia per visualizzare un codice a matrice. Non chiudere questa pagina.

5. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra e quindi selezionare **Altro account (Google, Facebook, ecc.)**.

6. Usare la fotocamera del dispositivo per eseguire la scansione del codice a matrice dalla pagina **Scegli come ricevere i codici** sul PC.

    >[!Note]
    >Se la fotocamera non funziona correttamente, è possibile [immettere manualmente il codice a matrice e l'URL](#add-an-account-to-the-app-manually).

5. Rivedere la pagina **Account** dell'app Microsoft Authenticator nel dispositivo per assicurarsi che le informazioni dell'account siano corrette e che quest'ultimo sia associato a un codice di verifica a sei cifre.

    Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

6. Nella pagina **Scegli come ricevere i codici** sul PC, digitare il codice di verifica a sei cifre fornito nell'app per l'account Amazon, quindi selezionare **Verifica il codice e continua**.

7. Completare il processo di iscrizione, inclusa l'aggiunta di un metodo di verifica di sicurezza, ad esempio un SMS e quindi selezionare **Invia codice**.

8. Nella pagina **Aggiungi un metodo di verifica di sicurezza** sul PC, digitare il codice di verifica a sei cifre fornito dal metodo di verifica di sicurezza per l'account di Amazon e quindi selezionare **Verifica il codice e continua**.

9. Nella pagina Quasi terminato, decidere se rendere il PC un dispositivo attendibile e quindi selezionare **Ho capito. Attiva la verifica in due passaggi**.

    Viene visualizzata la pagina **Impostazioni di sicurezza avanzate**, che mostra i dettagli aggiornati della verifica a due fattori.

    >[!NOTE]
    >Per altre informazioni sulla verifica a due fattori e l'account Amazon, vedere [About Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) (Informazioni sulla verifica in due passaggi) e [Signing in with Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440) (Accedere con la verifica in due passaggi).


## <a name="next-steps"></a>Passaggi successivi

- Dopo aver aggiunto gli account all'app, è possibile accedere usando l'app Authenticator nel dispositivo. Per altre informazioni, consultare [Accedere con l'app](user-help-auth-app-sign-in.md).

- Per i dispositivi che eseguono iOS, è anche possibile eseguire il backup nel cloud delle credenziali dell'account e delle relative impostazioni dell'app, ad esempio l'ordine degli account. Per altre informazioni, consultare [Eseguire il backup e il ripristino con l'app Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
