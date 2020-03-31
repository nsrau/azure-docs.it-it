---
title: Eseguire il backup e il ripristino degli account con l'app Microsoft Authenticator - Azure ADBack up and recover accounts with the Microsoft Authenticator app - Azure AD
description: Informazioni su come eseguire il backup e il ripristino delle credenziali dell'account di cui è stato eseguito il backup usando l'app Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297985"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Eseguire il backup e recuperare le credenziali dell'account tramite l'app Microsoft Authenticator

**Si applica a:**

- Dispositivi iOS, con la versione 5.7.0 e successive

- Dispositivi Android, con la versione 6.6.0 e successive

L'app Microsoft Authenticator esegue il backup delle credenziali dell'account e delle impostazioni delle app correlate, ad esempio l'ordine degli account, nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

Ogni posizione di archiviazione di backup richiede un account Microsoft personale, mentre iOS richiede anche un account iCloud. È possibile archiviare più account in un'unica posizione. Ad esempio, puoi avere un account personale, un account aziendale o dell'istituto di istruzione e un account personale non Microsoft come per Facebook, Google e così via.

> [!IMPORTANT]
> Vengono archiviate solo le credenziali dell'account personale e di terze parti, inclusi il nome utente e il codice di verifica dell'account necessari per dimostrare la propria identità. Non memorizziamo altre informazioni associate ai tuoi account, incluse email o file. Inoltre, non associamo o condividiamo i tuoi account in alcun modo o con altri prodotti o servizi. Infine, l'amministratore IT non otterrà alcuna informazione su nessuno di questi account.

## <a name="back-up-your-account-credentials"></a>Eseguire il backup delle credenziali dell'account

Prima di poter eseguire il backup delle credenziali, è necessario disporre di:

- Un [account Microsoft](https://account.microsoft.com/account) personale da usare come account di ripristino.

- **Solo per iOS,** è necessario disporre di un [account iCloud](https://www.icloud.com/) per il percorso di archiviazione effettivo.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Per attivare il backup cloud per i dispositivi iOS

- Nel dispositivo iOS selezionare **Impostazioni**, selezionare **Backup** e quindi attivare il **backup iCloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account iCloud.

    ![Schermata delle impostazioni di iOS, che mostra la posizione delle impostazioni per il backup iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Per attivare il backup cloud per dispositivi Android

- Sul dispositivo Android selezionare **Impostazioni**, **Backup**e quindi attivare **Backup cloud**.

    Il backup delle credenziali dell'account viene eseguito nell'account cloud.

    ![Schermata delle impostazioni di Android, che mostra la posizione delle impostazioni di backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Ripristinare le credenziali dell'account nel nuovo dispositivo

È possibile recuperare le credenziali dell'account dall'account cloud, ma è necessario innanzitutto assicurarsi che l'account che si sta ripristinando non esista nell'app Microsoft Authenticator. Ad esempio, se stai ripristinando il tuo account Microsoft personale, devi assicurarti di non avere un account Microsoft personale già configurato nell'app di autenticazione. Questo controllo è importante, quindi possiamo essere sicuri di non sovrascrivere o cancellare un account esistente per errore.

### <a name="to-recover-your-information"></a>Per ripristinare le informazioni

1. Nel dispositivo mobile apri l'app Microsoft Authenticator e seleziona **Avvia ripristino** nella parte inferiore dello schermo.

    ![App Microsoft Authenticator che mostra dove fare clic su Inizia il ripristino](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Accedere all'account di ripristino, usando lo stesso account Microsoft personale usato durante il processo di backup.

    Le credenziali dell'account vengono ripristinate nel nuovo dispositivo.

Dopo aver completato il ripristino, è possibile notare che i codici di verifica dell'account Microsoft personale nell'app Microsoft Authenticator sono diversi nel telefono vecchio e in quello nuovo. I codici sono diversi perché ogni dispositivo ha credenziali univoche, ma sono entrambi validi e funzionanti durante l'accesso con il telefono associato.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Ripristinare altri account che richiedono ulteriori verifiche

Se utilizzi le notifiche push con i tuoi account personali, aziendali o dell'istituto di istruzione, riceverai un avviso sullo schermo che indica che devi fornire ulteriori verifiche prima di poter recuperare le tue informazioni. Poiché le notifiche push richiedono l'utilizzo di una credenziale collegata al dispositivo specifico e mai inviata in rete, è necessario dimostrare la propria identità prima che la credenziale venga creata nel dispositivo.

Per gli account Microsoft personali, è possibile provare la propria identità immettendo la password insieme a un numero di telefono o posta elettronica alternativo. Per gli account aziendali o dell'istituto di istruzione, è necessario eseguire la scansione di un codice a matrice fornito dal provider dell'account.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Per fornire un'ulteriore verifica per gli account personali

1. Nella schermata **Account** dell'app Microsoft Authenticator selezionare l'account da ripristinare. Su un dispositivo Android, seleziona la freccia accanto all'account che desideri recuperare.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Su un dispositivo iOS, tocca l'account che vuoi recuperare per aprire la visualizzazione a schermo intero dell'account.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Accedi per recuperare l'account. Su un dispositivo Android, selezionare **Accedi per recuperare**.

    ![App Microsoft Authenticator per immettere le informazioni di accesso su Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Su un dispositivo iOS, tocca il riquadro dell'account che desideri recuperare, quindi tocca l'opzione per accedere e recuperare. Quindi digitare la password e quindi confermare l'indirizzo di posta elettronica o il numero di telefono come ulteriore verification.unt.

    ![App Microsoft Authenticator per immettere le informazioni di accesso su iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Per fornire un'ulteriore verifica per gli account aziendali o dell'istituto di istruzione

1. Accedi per recuperare l'account. Su un dispositivo Android, selezionare **Accedi per recuperare**.

    ![L'app Microsoft Authenticator consente di ripristinare un account aziendale o dell'istituto di istruzione in Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Su un dispositivo iOS, tocca l'account che desideri recuperare per aprire la visualizzazione a schermo intero dell'account.

    ![L'app Microsoft Authenticator consente di ripristinare un account aziendale o dell'istituto di istruzione in iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. È possibile utilizzare un codice QR per recuperare un account. Selezionare **Scansione codice QR per ripristinare** e quindi eseguire la scansione del codice a matrice.

    In Android:

    ![App Microsoft Authenticator su Android, che consente di eseguire la scansione del codice QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    Su iOS:

    ![App Microsoft Authenticator su iOS, che consente di eseguire la scansione del codice QR](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Per altre info sui codici QR e su come ottenerne uno, vedi [Introduzione all'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) o Configurare le informazioni di [sicurezza per usare un'app di autenticazione,](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)a seconda che l'amministratore abbia attivato le informazioni di sicurezza.
    >
    >Se si configura l'app Microsoft Authenticator per la prima volta, è possibile che venga visualizzato un messaggio in cui viene richiesto se consentire all'app di accedere alla fotocamera (iOS) o se consentire all'app di scattare foto e registrare video (Android). Selezionare **Consenti** per consentire all'app Authenticator di accedere alla fotocamera per scattare una foto del codice a matrice nel passaggio successivo. Se non si consente l'accesso alla fotocamera, è comunque possibile configurare l'app Authenticator, ma sarà necessario aggiungere le informazioni sul codice manualmente. Per altre informazioni su come aggiungere manualmente un codice, vedere [Aggiungere manualmente un account all'app](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Risolvere i problemi di backup e ripristino

Esistono alcuni motivi per cui il backup potrebbe non essere disponibile

- **Cambio di sistema operativo**: Il backup viene archiviato in iCloud per iOS e nel provider di archiviazione cloud di Microsoft per Android. Ciò significa che il backup non è disponibile se si passa da un dispositivo Android a un dispositivo iOS. Se si effettua il passaggio, è necessario ricreare manualmente gli account all'interno dell'app Microsoft Authenticator.

- **Problemi di rete:** se si verificano problemi relativi alla rete, assicurarsi di essere connessi alla rete e di aver eseguito correttamente l'accesso all'account.

- **Problemi relativi all'account:** se riscontri problemi relativi all'account, assicurati di aver eseguito correttamente l'accesso al tuo account. Per iOS questo significa che devi aver effettuato l'accesso a iCloud utilizzando lo stesso account AppleID del tuo iPhone.

- **Cancellazione accidentale**: È possibile che l'account di backup sia stato eliminato dal dispositivo precedente o durante la gestione dell'account di archiviazione cloud. In questo caso, è necessario ricreare manualmente l'account all'interno dell'app.

- **Account Microsoft Authenticator esistenti:** se hai già configurato gli account nell'app Microsoft Authenticator, l'app non sarà in grado di ripristinare gli account di cui è stato eseguito il backup. Impedendo il ripristino ci si assicura che i dettagli dell'account non vengano sovrascritti con informazioni non aggiornate. In questo caso, è necessario rimuovere eventuali informazioni sugli account esistenti dagli account esistenti configurati nell'app Authenticator prima di poter ripristinare il backup.

- **Backup non aggiornato:** se le informazioni di backup non sono aggiornate, potrebbe essere richiesto di aggiornare nuovamente le informazioni accedendo nuovamente all'account Microsoft Recovery. L'account di ripristino è l'account Microsoft personale utilizzato inizialmente per archiviare il backup. Se è necessario un accesso, sul menu o nella barra delle azioni verrà visualizzato un punto rosso oppure verrà visualizzato un punto esclamativo che richiede di accedere per completare il ripristino dal backup. Dopo aver selezionato l'icona, ti verrà richiesto di accedere di nuovo per aggiornare le informazioni.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il backup e il ripristino delle credenziali dell'account nel nuovo dispositivo, è possibile continuare a usare l'app Microsoft Authenticator per verificare l'identità. Per altre informazioni, vedere [Accedere agli account usando l'app Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Articoli correlati

- [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
