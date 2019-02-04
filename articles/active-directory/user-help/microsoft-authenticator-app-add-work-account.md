---
title: Aggiungere l'account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Come aggiungere l'account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator per la verifica a due fattori.
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
ms.openlocfilehash: 6d92306b7588eeb74a51b379c139e63555b32ba9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471539"
---
# <a name="add-your-work-or-school-account"></a>Aggiungere l'account aziendale o dell'istituto di istruzione
Se l'organizzazione usa la verifica a due fattori, è possibile impostare l'account aziendale o dell'istituto di istruzione in modo da usare l'app Microsoft Authenticator come uno dei metodi di verifica.

>[!Important]
>Prima di poter aggiungere l'account, è necessario scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, seguire i passaggi descritti nell'articolo [Scaricare e installare l'app](microsoft-authenticator-app-how-to.md).

## <a name="add-your-work-or-school-account"></a>Aggiungere l'account aziendale o dell'istituto di istruzione

1. Nel PC in uso, andare alla pagina [Verifica aggiuntiva di sicurezza](https://aka.ms/mfasetup).

    >[!Note]
    >Se non viene visualizzata la pagina **Verifica aggiuntiva di sicurezza**, è possibile che l'amministratore abbia attivato l'esperienza informazioni di sicurezza (anteprima). In tal caso è necessario seguire le istruzioni descritte nella sezione [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md). Se non è questo il caso, è necessario contattare l'help desk dell'organizzazione per ricevere assistenza. Per altri dettagli sulle informazioni di sicurezza, vedere [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

2. Selezionare la casella accanto ad **App Authenticator** e quindi selezionare **Configura**.

    Viene visualizzata la pagina **Configurare l'app per dispositivi mobili**.
    
    ![Schermata contenente il codice a matrice](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra e quindi selezionare **Account aziendale o dell'istituto di istruzione**.

4. Usare la fotocamera del dispositivo per eseguire la scansione del codice a matrice dalla schermata **Configura l'app per dispositivi mobili** del PC e quindi scegliere **Fine**.

    >[!Note]
    >Se la fotocamera non è in grado di acquisire il codice a matrice, è possibile aggiungere manualmente le informazioni dell'account all'app Microsoft Authenticator per la verifica a due fattori. Per altre informazioni e istruzioni su come eseguire questa operazione, consultare [Manually add your account](microsoft-authenticator-app-add-account-manual.md) (Aggiungere manualmente l'account).

5. Rivedere la schermata **Account** dell'app nel dispositivo per assicurarsi che l'account sia corretto e che sia associato a un codice di verifica a sei cifre. Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

    ![Schermata Account](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver aggiunto gli account all'app, è possibile accedere usando l'app Authenticator nel dispositivo. Per altre informazioni, consultare [Accedere con l'app](microsoft-authenticator-app-phone-signin-faq.md).

- Per i dispositivi che eseguono iOS, è anche possibile eseguire il backup nel cloud delle credenziali dell'account e delle relative impostazioni dell'app, ad esempio l'ordine degli account. Per altre informazioni, consultare [Eseguire il backup e il ripristino con l'app Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md).
