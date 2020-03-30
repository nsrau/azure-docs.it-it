---
title: Configurare una chiave di sicurezza come metodo di verifica - Azure ADSet up a security key as your verification method - Azure AD
description: Come configurare la pagina Informazioni di sicurezza (anteprima) per verificare la propria identità per l'utilizzo di una chiave di sicurezza FIDO2 (Fast Identity Online) come metodo di verifica.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062337"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Impostare una chiave di sicurezza come metodo di verifica

È possibile utilizzare le chiavi di sicurezza come metodo di accesso senza password all'interno dell'organizzazione. Una chiave di sicurezza è un dispositivo fisico utilizzato con un PIN univoco per accedere all'account aziendale o dell'istituto di istruzione. Poiché le chiavi di sicurezza richiedono di avere il dispositivo fisico e qualcosa che solo tu conosci, è considerato un metodo di verifica più forte di un nome utente e una password.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se non vedi l'opzione della chiave di sicurezza, è possibile che la tua organizzazione non ti consenta di utilizzare questa opzione per la verifica. In questo caso, è necessario scegliere un altro metodo o contattare l'help desk dell'organizzazione per ulteriore assistenza.

## <a name="security-verification-versus-password-reset-authentication"></a>Confronto tra verifica di sicurezza e autenticazione per la reimpostazione della password

I metodi di gestione delle informazioni di sicurezza vengono usati sia per la verifica di sicurezza a due fattori sia per la reimpostazione della password. Non tutti i metodi possono tuttavia essere usati per entrambe.

| Metodo | Utilizzo |
| ------ | -------- |
| App di autenticazione | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| SMS | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Telefonate | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Chiave di sicurezza | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Account di posta elettronica | Solo autenticazione per la reimpostazione della password. Per la verifica a due fattori sarà necessario scegliere un altro metodo. |
| Domande di sicurezza | Solo autenticazione per la reimpostazione della password. Per la verifica a due fattori sarà necessario scegliere un altro metodo. |

## <a name="what-is-a-security-key"></a>Che cos'è una chiave di sicurezza?

Attualmente supportiamo diversi progetti e fornitori di chiavi di sicurezza utilizzando i protocolli di autenticazione [FIDO2 (Fast Identity Online)](https://fidoalliance.org/fido2/) senza password. Queste chiavi consentono di accedere all'account aziendale o dell'istituto di istruzione per accedere alle risorse basate su cloud dell'organizzazione in un dispositivo e un Web browser supportati.

L'amministratore o l'organizzazione fornirà una chiave di sicurezza se è necessaria per l'account aziendale o dell'istituto di istruzione. Ci sono diversi tipi di chiavi di sicurezza che è possibile utilizzare, ad esempio una chiave USB che si collega al dispositivo o una chiave NFC che si tocca su un lettore NFC. È possibile trovare ulteriori informazioni sulla chiave di sicurezza, incluso il tipo, dalla documentazione del produttore.

> [!Note]
> Se non riesci a usare una chiave di sicurezza FIDO2, puoi usare altri metodi di verifica senza password, ad esempio l'app Microsoft Authenticator o Windows Hello. Per ulteriori informazioni sull'app Microsoft Authenticator, vedere [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md). Per ulteriori informazioni su Windows Hello, vedere [Panoramica](https://www.microsoft.com/windows/windows-hello)di Windows Hello .

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter registrare la chiave di sicurezza, è necessario che siano le seguenti condizioni:

- L'amministratore ha attivato questa funzionalità per l'utilizzo all'interno dell'organizzazione.

- Sei su un dispositivo che esegue l'aggiornamento di Windows 10 maggio 2019 e si utilizza un browser supportato.

- Si dispone di una chiave di protezione fisica approvata dall'amministratore o dall'organizzazione. La chiave di protezione deve essere compatibile sia con FIDO2 che su Microsoft. In caso di domande sulla chiave di sicurezza e sulla compatibilità, contattare l'help desk dell'organizzazione.

## <a name="register-your-security-key"></a>Registrare la chiave di sicurezza

È necessario creare la chiave di sicurezza e assegnarle un PIN univoco prima di poter accedere all'account aziendale o dell'istituto di istruzione utilizzando la chiave. Potresti avere fino a 10 chiavi registrate con il tuo account. 

1. Vai alla pagina Il https://myprofile.microsoft.com mio **profilo** e accedi se non l'hai già fatto.

2. Selezionare **Security Info (Informazioni di sicurezza),** **selezionare** Add **method (Aggiungi metodo)** e quindi dall'elenco a discesa **Add a method (Aggiungi metodo).**

    ![Casella Aggiungi metodo con la chiave di sicurezza selezionata](media/security-info/security-info-security-key-add-method.png)

3. Selezionare **Aggiungi**, quindi selezionare il tipo di chiave di sicurezza in disporre, **Dispositivo USB** o **Dispositivo NFC**.

    ![Scegliere se si dispone di un tipo di chiave di sicurezza USB o NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se non si è certi del tipo di chiave di sicurezza in si dispone, fare riferimento alla documentazione del produttore. Se non si è sicuri del produttore, contattare l'help desk dell'organizzazione per assistenza.

4. Disporre fisicamente della chiave di protezione, quindi nella casella Chiave di **protezione** selezionare **Avanti**.

    ![Casella di registrazione per l'avvio della chiave di sicurezza](media/security-info/security-info-security-key-start-setup.png)

    Viene visualizzata una nuova casella che consente di eseguire la configurazione del nuovo metodo di accesso.

5. Nella casella **Impostazione del nuovo metodo di accesso** selezionare **Avanti**e quindi:

    - Se la chiave di sicurezza è un dispositivo USB, inserire la chiave di sicurezza nella porta USB del dispositivo.

    - Se la chiave di sicurezza è un dispositivo NFC, toccare la chiave di sicurezza per il lettore.

6. Digitare il PIN univoco della chiave di sicurezza nella casella di sicurezza di **Windows** e quindi scegliere **OK**.

    Si tornerà alla casella Impostazione del nuovo metodo di **accesso.**

7. Fare clic su **Avanti**.

8. Tornare alla pagina **Informazioni di sicurezza,** digitare un nome riconosciuto in un secondo momento per la nuova chiave di sicurezza e quindi selezionare **Avanti**.

    ![Pagina delle informazioni di sicurezza, denominazione della chiave di sicurezza](media/security-info/security-info-security-key-name.png)

    La chiave di sicurezza è registrata e pronta per l'accesso all'account aziendale o dell'istituto di istruzione.

9. Selezionare **Fine** per chiudere la casella **Chiave di sicurezza.**

    La pagina Informazioni di **sicurezza** viene aggiornata con le informazioni sulla chiave di sicurezza.

    ![Pagina delle informazioni di sicurezza, con tutti i metodi registrati mostrati](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Eliminare una chiave di sicurezza dalle informazioni di sicurezzaDelete a security key from your security info

Se si smarrisce o non si desidera più utilizzare una chiave di sicurezza, è possibile eliminare la chiave dalle informazioni di sicurezza. Mentre in questo modo la chiave di sicurezza viene utilizzata con l'account aziendale o dell'istituto di istruzione, la chiave di sicurezza continua a archiviare i dati e le informazioni sulle credenziali. Per eliminare i dati e le informazioni sulle credenziali dalla chiave di sicurezza stessa, è necessario seguire le istruzioni nella sezione Reimpostare una chiave di [sicurezza compatibile con Microsoft](#reset-your-security-key) di questo articolo.

1. Selezionare il collegamento **Elimina** dalla chiave di sicurezza da rimuovere.

2. Selezionare **OK** dalla casella **Elimina chiave di sicurezza.**

    La chiave di sicurezza viene eliminata e non sarà più possibile utilizzarla per accedere all'account aziendale o dell'istituto di istruzione.

>[!Important]
>Se si elimina una chiave di sicurezza per errore, è possibile registrarla nuovamente seguendo le istruzioni nella sezione [Come registrare la chiave](#register-your-security-key) di sicurezza di questo articolo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gestire le impostazioni della chiave di sicurezza da Impostazioni di Windows

Puoi gestire le impostazioni della chiave di sicurezza dall'app **Impostazioni di Windows,** inclusa la reimpostazione della chiave di sicurezza e la creazione di un nuovo PIN della chiave di sicurezza.

### <a name="reset-your-security-key"></a>Reimpostare la chiave di sicurezza

Se si desidera eliminare tutte le informazioni sull'account archiviate nella chiave di sicurezza fisica, è necessario ripristinare le impostazioni predefinite di fabbrica. La reimpostazione della chiave di sicurezza elimina tutto dalla chiave, consentendo di ricominciare da capo.

>[!IMPORTANT]
>La reimpostazione della chiave di sicurezza elimina tutto dalla chiave, reimpostandola sui valori predefiniti di fabbrica.
>
> **Tutti i dati e le credenziali verranno cancellati.**

#### <a name="to-reset-your-security-key"></a>Per reimpostare la chiave di sicurezza

1. Aprire l'app Impostazioni di Windows, selezionare **Account**, Opzioni **di**accesso , quindi **Chiave di protezione**e quindi **Gestisci**.

2. Inserisci la chiave di sicurezza nella porta USB o tocca il tuo lettore NFC per verificare la tua identità.

3. Seguire le istruzioni visualizzate, in base al produttore specifico della chiave di sicurezza. Se il produttore della chiave non è elencato nelle istruzioni visualizzate, fare riferimento al sito del produttore per ulteriori informazioni.

4. Selezionare **Chiudi** per chiudere la schermata **Gestisci.**

### <a name="create-a-new-security-key-pin"></a>Creare un nuovo PIN della chiave di sicurezza

È possibile creare un nuovo PIN della chiave di sicurezza per la chiave di sicurezza.

#### <a name="to-create-a-new-security-key-pin"></a>Per creare un nuovo PIN della chiave di sicurezza

1. Aprire l'app Impostazioni di Windows, selezionare **Account**, Opzioni **di**accesso , quindi **Chiave di protezione**e quindi **Gestisci**.

2. Inserisci la chiave di sicurezza nella porta USB o tocca il tuo lettore NFC per verificare la tua identità.
3. Selezionare **Aggiungi** nell'area **PIN chiave** di sicurezza , digitare e confermare il nuovo PIN della chiave di sicurezza, quindi scegliere **OK**.

     La chiave di sicurezza viene aggiornata con il nuovo PIN della chiave di sicurezza da utilizzare con l'account aziendale o dell'istituto di istruzione. Se si decide di modificare nuovamente il PIN, è possibile selezionare il pulsante **Cambia.**
4. Selezionare **Chiudi** per chiudere la schermata **Gestisci.**

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

Per registrare una chiave di sicurezza, è necessario che sia registrato almeno un metodo di verifica della sicurezza aggiuntivo. Per altre informazioni, vedere la [sezione Panoramica.See](security-info-add-update-methods-overview.md) the Overview section for more information. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui metodi di verifica senza password, leggere il blog di panoramica di Azure AD di [Microsoft inizia, abilita accessi senza password](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) o gli articoli di panoramica di Microsoft [Authenticator?](user-help-auth-app-overview.md) e [Windows Hello.](https://www.microsoft.com/windows/windows-hello)

- Per informazioni più dettagliate sulle chiavi di [protezione conformi a Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
