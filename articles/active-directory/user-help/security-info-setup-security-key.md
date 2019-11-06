---
title: Configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza (anteprima)-Azure Active Directory | Microsoft Docs
description: Come configurare le info di sicurezza per verificare la propria identità usando una chiave di sicurezza FIDO2 (Fast Identity online).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47ae7de87efd787b7d3cba14a33688c4b29637e6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160717"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Configurare le info di sicurezza per l'uso di una chiave di sicurezza (anteprima)

È possibile usare le chiavi di sicurezza come metodo di accesso senza password all'interno dell'organizzazione. Una chiave di sicurezza è un dispositivo fisico usato con un PIN univoco per accedere all'account aziendale o dell'Istituto di istruzione. Poiché le chiavi di sicurezza richiedono che il dispositivo fisico e un elemento sia solo noto, viene considerato un metodo di autenticazione più sicuro rispetto a un nome utente e una password.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire l'ambiente Azure Active Directory (Azure AD) nella [documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="what-is-a-security-key"></a>Che cos'è una chiave di sicurezza?

Attualmente sono supportati diversi progetti e provider di chiavi di sicurezza usando i protocolli di autenticazione con password FIDO2 ( [Fast Identity online](https://fidoalliance.org/fido2/) ). Queste chiavi consentono di accedere all'account aziendale o dell'Istituto di istruzione per accedere alle risorse basate sul cloud dell'organizzazione quando si usa un dispositivo supportato e un Web browser.

L'amministratore o l'organizzazione fornirà una chiave di sicurezza, se necessaria per l'account aziendale o dell'Istituto di istruzione. Esistono diversi tipi di chiavi di sicurezza che è possibile usare, ad esempio una chiave USB che si collega al dispositivo o una chiave NFC che si tocca su un lettore NFC. È possibile trovare altre informazioni sulla chiave di sicurezza, tra cui il tipo di dati, dalla documentazione del produttore.

> [!Note]
> Se non si è in grado di usare una chiave di sicurezza FIDO2, è possibile usare altri metodi di autenticazione con password, ad esempio l'app Microsoft Authenticator o Windows Hello. Per altre informazioni sull'app Microsoft Authenticator, vedere [che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md). Per ulteriori informazioni su Windows Hello, vedere [Panoramica di Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter registrare la chiave di sicurezza, è necessario che siano soddisfatte le condizioni seguenti:

- L'amministratore ha attivato questa funzionalità per l'uso nell'organizzazione.

- Si trova in un dispositivo che esegue l'aggiornamento di Windows 10 2019 maggio e che usa un browser supportato.

- Si dispone di una chiave di sicurezza fisica approvata dall'amministratore o dall'organizzazione. La chiave di sicurezza deve essere FIDO2 e conforme a Microsoft. In caso di domande sulla chiave di sicurezza e se è compatibile, contattare la help desk dell'organizzazione.

## <a name="register-your-security-key"></a>Registra la chiave di sicurezza

È necessario creare la chiave di sicurezza e assegnarle un PIN univoco prima di poter accedere all'account aziendale o dell'Istituto di istruzione usando la chiave. È possibile che si disponga di un massimo di 10 chiavi registrate con l'account. 

1. Passare alla pagina **profilo personale** in https://myprofile.microsoft.com e accedere se non è già stato fatto.

2. Selezionare **info di sicurezza**, selezionare **Aggiungi metodo**, quindi selezionare **chiave di sicurezza** dall'elenco a discesa **Aggiungi un metodo** .

    ![Casella Aggiungi metodo con chiave di sicurezza selezionata](media/security-info/security-info-security-key-add-method.png)

3. Selezionare **Aggiungi**e quindi selezionare il tipo di chiave di sicurezza, ovvero il **dispositivo USB** o il **dispositivo NFC**.

    ![Scegliere se si dispone di un tipo di chiave di sicurezza USB o NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se non si è certi del tipo di chiave di sicurezza, fare riferimento alla documentazione del produttore. Se non si è certi del produttore, contattare la help desk dell'organizzazione per richiedere assistenza.

4. Disporre della chiave di sicurezza fisicamente disponibile e quindi nella casella **chiave di sicurezza** selezionare **Avanti**.

    ![Casella di registrazione iniziale della chiave di sicurezza](media/security-info/security-info-security-key-start-setup.png)

    Viene visualizzata una nuova finestra che aiuta a configurare il nuovo metodo di accesso.

5. Nella casella **impostazione del nuovo metodo di accesso** selezionare **Avanti**, quindi:

    - Se la chiave di sicurezza è un dispositivo USB, inserire la chiave di sicurezza nella porta USB del dispositivo.

    - Se la chiave di sicurezza è un dispositivo NFC, toccare la chiave di sicurezza per il lettore.

6. Digitare il PIN della chiave di sicurezza univoca nella casella **sicurezza di Windows** e quindi fare clic su **OK**.

    Si tornerà alla casella **impostazione del nuovo metodo di accesso** .

7. Selezionare **Avanti**.

8. Tornare alla pagina **info sicurezza** , digitare un nome che verrà riconosciuto in seguito per la nuova chiave di sicurezza e quindi fare clic su **Avanti**.

    ![Pagina delle informazioni di sicurezza, denominazione della chiave di sicurezza](media/security-info/security-info-security-key-name.png)

    La chiave di sicurezza è registrata ed è pronta per essere usata per accedere all'account aziendale o dell'Istituto di istruzione.

9. Selezionare **fine** per chiudere la casella **chiave di sicurezza** .

    La pagina **informazioni di sicurezza** viene aggiornata con le informazioni sulla chiave di sicurezza.

    ![Pagina delle informazioni di sicurezza con tutti i metodi registrati visualizzati](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Eliminare una chiave di sicurezza dalle info di sicurezza

Se non si desidera più usare una chiave di sicurezza, è possibile eliminare la chiave dalle info di sicurezza. Anche se in questo modo viene arrestata l'uso della chiave di sicurezza con l'account aziendale o dell'Istituto di istruzione, la chiave di sicurezza continua a archiviare i dati e le informazioni sulle credenziali. Per eliminare i dati e le informazioni sulle credenziali dalla chiave di sicurezza stessa, è necessario seguire le istruzioni riportate nella sezione [reimpostare una chiave di sicurezza compatibile con Microsoft](#reset-your-security-key) di questo articolo.

1. Selezionare il collegamento **Elimina** dalla chiave di sicurezza da rimuovere.

2. Selezionare **OK** nella casella **Elimina chiave di sicurezza** .

    La chiave di sicurezza è stata eliminata e non sarà più possibile usarla per accedere all'account aziendale o dell'Istituto di istruzione.

>[!Important]
>Se si elimina una chiave di sicurezza per errore, è possibile registrarla di nuovo seguendo le istruzioni riportate nella sezione [come registrare la chiave di sicurezza](#register-your-security-key) di questo articolo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gestire le impostazioni della chiave di sicurezza da impostazioni di Windows

È possibile gestire le impostazioni della chiave di sicurezza dall'app **impostazioni di Windows** , inclusa la reimpostazione della chiave di sicurezza e la creazione di un nuovo PIN della chiave di sicurezza.

### <a name="reset-your-security-key"></a>Reimposta la chiave di sicurezza

Per eliminare tutte le informazioni sull'account archiviate nella chiave di sicurezza fisica, è necessario ripristinare le impostazioni predefinite della chiave. La reimpostazione della chiave di sicurezza Elimina tutti gli elementi dalla chiave, consentendo di ricominciare.

>[!IMPORTANT]
>La reimpostazione della chiave di sicurezza Elimina tutti gli elementi dalla chiave, reimpostando le impostazioni predefinite.
>
> **Tutti i dati e le credenziali verranno cancellati.**

#### <a name="to-reset-your-security-key"></a>Per reimpostare la chiave di sicurezza

1. Aprire l'app impostazioni di Windows, selezionare **account**, **Opzioni di accesso**, selezionare **chiave di sicurezza**e quindi fare clic su **Gestisci**.

2. Inserire la chiave di sicurezza nella porta USB oppure toccare il lettore NFC per verificare la propria identità.

3. Seguire le istruzioni visualizzate in base al produttore della chiave di sicurezza specifico. Se il produttore della chiave non è elencato nelle istruzioni visualizzate, consultare il sito del produttore per ulteriori informazioni.

4. Selezionare **Chiudi** per chiudere la schermata **Gestisci** .

### <a name="create-a-new-security-key-pin"></a>Creare un nuovo PIN della chiave di sicurezza

È possibile creare un nuovo PIN della chiave di sicurezza per la chiave di sicurezza.

#### <a name="to-create-a-new-security-key-pin"></a>Per creare un nuovo PIN della chiave di sicurezza

1. Aprire l'app impostazioni di Windows, selezionare **account**, **Opzioni di accesso**, selezionare **chiave di sicurezza**e quindi fare clic su **Gestisci**.

2. Inserire la chiave di sicurezza nella porta USB oppure toccare il lettore NFC per verificare la propria identità.
3. Selezionare **Aggiungi** nell'area del **pin della chiave di sicurezza** , digitare e confermare il nuovo PIN della chiave di sicurezza e quindi fare clic su **OK**.

     La chiave di sicurezza viene aggiornata con il nuovo PIN della chiave di sicurezza da usare con l'account aziendale o dell'Istituto di istruzione. Se si decide di modificare di nuovo il PIN, è possibile selezionare il pulsante **Cambia** .
4. Selezionare **Chiudi** per chiudere la schermata **Gestisci** .

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

Per registrare una chiave di sicurezza, è necessario avere almeno un metodo di verifica di sicurezza aggiuntivo registrato. Per ulteriori informazioni, vedere la [sezione Panoramica](security-info-add-update-methods-overview.md) . 

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui metodi di autenticazione con password, leggere l' [Azure ad Microsoft avvia l'anteprima pubblica delle chiavi di sicurezza FIDO2, abilitando gli account di accesso](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) con [Articoli introduttivi su Windows Hello](user-help-auth-app-overview.md) [password o leggere l'app Microsoft Authenticator? ](https://www.microsoft.com/windows/windows-hello).

- Per informazioni più dettagliate sulle [chiavi di sicurezza conformi a Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
