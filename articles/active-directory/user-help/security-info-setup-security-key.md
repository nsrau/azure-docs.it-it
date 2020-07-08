---
title: Configurare una chiave di sicurezza come metodo di verifica - Azure AD
description: Come configurare la pagina Informazione di sicurezza (anteprima) per verificare l'identità per usare una chiave di sicurezza FIDO2 (Fast Identity Online) come metodi di verifica.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: 91138f104c787148042b187f4cf59a60bc1448d3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744423"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Configurare una chiave di sicurezza come metodi di verifica

È possibile usare le chiavi di sicurezza come metodo di accesso senza password all'interno dell'organizzazione. Una chiave di sicurezza è un dispositivo fisico usato con un PIN univoco per accedere all'account aziendale o dell'istituto di istruzione. Poiché per le chiavi di sicurezza è necessario avere il dispositivo fisico e un'informazione nota solo all'utente, questo metodo di verifica è considerato più sicuro rispetto a un nome utente e una password.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se l'opzione relativa alla chiave di sicurezza non viene visualizzata, è possibile che l'organizzazione non consenta l'uso di questa opzione per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'help desk dell'organizzazione per ricevere assistenza.

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

Attualmente sono supportati diversi tipi e provider di chiavi di sicurezza con i protocolli di autenticazione senza password [FIDO2 (Fast Identity Online)](https://fidoalliance.org/fido2/). Queste chiavi consentono di accedere all'account aziendale o dell'istituto di istruzione per accedere alle risorse basate sul cloud dell'organizzazione con un dispositivo e un Web browser supportati.

L'amministratore o l'organizzazione fornirà una chiave di sicurezza, se necessaria per l'account aziendale o dell'istituto di istruzione. Esistono diversi tipi di chiavi di sicurezza che è possibile usare, ad esempio una chiave USB che si collega al dispositivo o una chiave NFC che si tocca su un lettore NFC. Per altre informazioni sulla chiave di sicurezza, tra cui il tipo, vedere la documentazione del produttore.

> [!Note]
> Se non è possibile usare una chiave di sicurezza FIDO2, sono disponibili altri metodi di verifica senza password, ad esempio l'app Microsoft Authenticator o Windows Hello. Per altre informazioni sull'app Microsoft Authenticator, vedere l'articolo [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md) Per ulteriori informazioni su Windows Hello, vedere [Panoramica di Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di registrare la chiave di sicurezza, è necessario che siano soddisfatte le condizioni seguenti:

- L'amministratore ha attivato questa funzionalità per l'uso nell'organizzazione.

- Si usa un dispositivo che esegue l'Aggiornamento di Windows 10 (maggio 2019) e un browser supportato.

- Si ha una chiave di sicurezza fisica approvata dall'amministratore o dall'organizzazione. La chiave di sicurezza deve essere conforme a FIDO2 e a Microsoft. In caso di domande sulla chiave di sicurezza e sulla relativa compatibilità, contattare l'help desk dell'organizzazione.

## <a name="register-your-security-key"></a>Registrare la chiave di sicurezza

È necessario creare la chiave di sicurezza e assegnarle un PIN univoco prima usarla per accedere all'account aziendale o dell'istituto di istruzione. Per l'account è possibile registrare fino a 10 chiavi. 

1. Passare alla pagina **Profilo personale** all'indirizzo https://myaccount.microsoft.com ed effettuare l'accesso, se non è già stato fatto.

2. Selezionare **Informazione di sicurezza**, selezionare **Aggiungi metodo**, quindi selezionare **Chiave di sicurezza** nell'elenco a discesa **Aggiungi un metodo**.

    ![Finestra di dialogo Aggiungi metodo, con l'opzione Chiave di sicurezza selezionata](media/security-info/security-info-security-key-add-method.png)

3. Selezionare **Aggiungi**, quindi selezionare il tipo di chiave di sicurezza, ossia **Dispositivo USB** o **Dispositivo NFC**.

    ![Scegliere se la chiave di sicurezza è di tipo USB o NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > In caso di dubbi sul tipo di chiave di sicurezza disponibile, consultare la documentazione del produttore. Se non si è certi del produttore, contattare la help desk dell'organizzazione per assistenza.

4. Con la chiave di sicurezza a portata di mano, selezionare **Avanti** nella finestra di dialogo **Chiave di sicurezza**.

    ![Finestra di dialogo di avvio della registrazione della chiave di sicurezza](media/security-info/security-info-security-key-start-setup.png)

    Viene visualizzata una nuova finestra di dialogo che illustra come configurare il nuovo metodo di accesso.

5. Nella finestra di dialogo **Configurazione del nuovo metodo di accesso** selezionare **Avanti** e quindi:

    - Se la chiave di sicurezza è un dispositivo USB, inserirla nella porta USB del dispositivo.

    - Se la chiave di sicurezza è un dispositivo NFC, toccarla sul lettore.

6. Digitare il PIN univoco della chiave di sicurezza univoca nella casella **Sicurezza di Windows**, quindi selezionare **OK**.

    Si tornerà nella finestra di dialogo **Configurazione del nuovo metodo di accesso**.

7. Selezionare **Avanti**.

8. Tornare nella pagina **Informazione di sicurezza**, digitare un nome riconoscibile in seguito per la nuova chiave di sicurezza e quindi selezionare **Avanti**.

    ![Pagina Informazione di sicurezza, in cui assegnare un nome alla chiave di sicurezza](media/security-info/security-info-security-key-name.png)

    La chiave di sicurezza è registrata ed è possibile usarla per accedere all'account aziendale o dell'istituto di istruzione.

9. Selezionare **Fine** e chiudere la finestra di dialogo **Chiave di sicurezza**.

    La pagina **Informazione di sicurezza** viene aggiornata con l'informazione sulla chiave di sicurezza.

    ![Pagina Informazione di sicurezza, con tutti i metodi registrati visualizzati](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Eliminare una chiave di sicurezza dall'informazione di sicurezza

Se la chiave di sicurezza va persa non la si vuole più usare, è possibile eliminarla dall'informazione di sicurezza. Anche se in questo modo se ne impedisce l'uso futuro per l'account aziendale o dell'istituto di istruzione, la chiave di sicurezza continua ad archiviare dati e informazioni sulle credenziali. Per eliminare i dati e le informazioni sulle credenziali dalla chiave di sicurezza stessa, è necessario seguire le istruzioni riportate nella sezione [Reimpostare la chiave di sicurezza](#reset-your-security-key) di questo articolo.

1. Selezionare il collegamento **Elimina** nella chiave di sicurezza da rimuovere.

2. Selezionare **OK** nella finestra di dialogo **Elimina la chiave di sicurezza**.

    La chiave di sicurezza viene eliminata e non sarà più possibile usarla per accedere all'account aziendale o dell'istituto di istruzione.

>[!Important]
>Se si elimina una chiave di sicurezza per errore, è possibile registrarla di nuovo seguendo le istruzioni riportate nella sezione [Registrare la chiave di sicurezza](#register-your-security-key) di questo articolo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gestire le impostazioni della chiave di sicurezza da Impostazioni di Windows

È possibile gestire le impostazioni della chiave di sicurezza dall'app **Impostazioni di Windows**, inclusa la reimpostazione della chiave di sicurezza e la creazione di un nuovo PIN.

### <a name="reset-your-security-key"></a>Reimpostare la chiave di sicurezza

Per eliminare tutte le informazioni dell'account archiviate nella chiave di sicurezza fisica, è necessario ripristinare le impostazioni predefinite della chiave. La reimpostazione della chiave di sicurezza ne elimina tutto il contenuto, consentendo di ricominciare.

>[!IMPORTANT]
>La reimpostazione della chiave di sicurezza ne elimina tutto il contenuto, ripristinando le impostazioni predefinite.
>
> **Tutti i dati e le credenziali verranno cancellati.**

#### <a name="to-reset-your-security-key"></a>Per reimpostare la chiave di sicurezza

1. Aprire l'app Impostazioni di Windows, quindi selezionare **Account**, **Opzioni di accesso**, **Chiave di sicurezza** e infine **Gestisci**.

2. Inserire la chiave di sicurezza nella porta USB oppure toccare il lettore NFC per verificare la propria identità.

3. Seguire le istruzioni visualizzate, in base al produttore della chiave di sicurezza specifica. Se il produttore della chiave non è elencato nelle istruzioni visualizzate, consultare il relativo sito per altre informazioni.

4. Selezionare **Chiudi** per chiudere la schermata **Gestisci**.

### <a name="create-a-new-security-key-pin"></a>Creare un nuovo PIN per la chiave di sicurezza

È possibile creare un nuovo PIN per la chiave di sicurezza.

#### <a name="to-create-a-new-security-key-pin"></a>Per creare un nuovo PIN per la chiave di sicurezza

1. Aprire l'app Impostazioni di Windows, quindi selezionare **Account**, **Opzioni di accesso**, **Chiave di sicurezza** e infine **Gestisci**.

2. Inserire la chiave di sicurezza nella porta USB oppure toccare il lettore NFC per verificare la propria identità.
3. Selezionare **Aggiungi** nell'area **PIN della chiave di sicurezza**, digitare e confermare il nuovo PIN, quindi selezionare **OK**.

     La chiave di sicurezza viene aggiornata con il nuovo PIN per l'uso con l'account aziendale o dell'istituto di istruzione. Se si decide di cambiare di nuovo il PIN, è possibile selezionare il pulsante **Cambia**.
4. Selezionare **Chiudi** per chiudere la schermata **Gestisci**.

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

Per registrare una chiave di sicurezza, è necessario avere almeno un metodo di verifica aggiuntiva di sicurezza registrato. Per altre informazioni, vedere la sezione [Panoramica](security-info-add-update-methods-overview.md). 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui metodi di verifica senza password, vedere il [blog Microsoft's Azure AD begins public preview of FIDO2 security keys, enabling passwordless logins](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) oppure gli articoli [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md) e [Panoramica di Windows Hello](https://www.microsoft.com/windows/windows-hello).

- Leggere informazioni più dettagliate sulle [chiavi di sicurezza compatibili con Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
