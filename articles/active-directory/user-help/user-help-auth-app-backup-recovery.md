---
title: Eseguire il backup e il ripristino con l'app Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire il backup e il ripristino delle credenziali dell'account usando l'app Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4e45fbe6533832765e90e5cf244f0dcbcf0b72
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035125"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Eseguire il backup e il ripristino delle credenziali dell'account con l'app Microsoft Authenticator

**Si applica a:**

- dispositivi iOS, che eseguono la versione 5.7.0 e successive

- Dispositivi Android, che eseguono la versione 6.6.0 e successive

L'app Microsoft Authenticator esegue il backup delle credenziali dell'account e delle impostazioni delle app correlate, ad esempio l'ordine degli account, nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

Ogni percorso di archiviazione di backup richiede un account Microsoft personale, mentre iOS richiede anche un account iCloud. È possibile avere più account archiviati nella stessa posizione. Ad esempio, è possibile avere un account personale, un account aziendale o dell'Istituto di istruzione e un personale, non account Microsoft come per Facebook, Google e così via.

> [!IMPORTANT]
> Sono archiviate solo le credenziali di account personali e di terze parti, che includono il nome utente e il codice di verifica dell'account necessario per dimostrare la propria identità. Non vengono archiviate altre informazioni associate agli account, inclusi messaggi di posta elettronica o file. Gli account non vengono inoltre associati o condivisi in alcun modo o con qualsiasi altro prodotto o servizio. Infine, l'amministratore IT non otterrà alcuna informazione riguardo a questi account.

## <a name="back-up-your-account-credentials"></a>Eseguire il backup delle credenziali dell'account

Prima di poter eseguire il backup delle credenziali, è necessario disporre di:

- Un [account Microsoft](https://account.microsoft.com/account) personale da usare come account di ripristino.

- **Solo per iOS,** è necessario disporre di un [account iCloud](https://www.icloud.com/) per il percorso di archiviazione effettivo.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Per attivare il backup sul cloud per i dispositivi iOS

- Nel dispositivo iOS selezionare **Impostazioni**, selezionare **Backup** e quindi attivare il **backup iCloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account iCloud.

    ![Schermata delle impostazioni di iOS, che mostra la posizione delle impostazioni per il backup iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Per attivare il backup su cloud per i dispositivi Android

- Nel dispositivo Android selezionare **Impostazioni**, selezionare **backup**, quindi eseguire il **backup nel cloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account cloud.

    ![Schermata delle impostazioni di Android, che mostra il percorso delle impostazioni di backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Ripristinare le credenziali dell'account nel nuovo dispositivo

È possibile recuperare le credenziali dell'account dall'account cloud, ma è prima necessario assicurarsi che l'account che si sta ripristinando non esista nell'app Microsoft Authenticator. Se, ad esempio, si sta recuperando il proprio account Microsoft personale, è necessario assicurarsi di non avere un account Microsoft personale già configurato nell'app Authenticator. Questo controllo è importante, pertanto possiamo assicurarci che non si stia sovrascrivendo o cancellando un account esistente per errore.

### <a name="to-recover-your-information"></a>Per ripristinare le informazioni

1. Nel dispositivo mobile aprire l'app Microsoft Authenticator e selezionare **inizia ripristino** nella parte inferiore della schermata.

    ![App Microsoft Authenticator che mostra dove fare clic su Inizia il ripristino](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Accedere all'account di ripristino, usando lo stesso account Microsoft personale usato durante il processo di backup.

    Le credenziali dell'account vengono ripristinate nel nuovo dispositivo.

Dopo aver completato il ripristino, è possibile notare che i codici di verifica dell'account Microsoft personale nell'app Microsoft Authenticator sono diversi nel telefono vecchio e in quello nuovo. I codici sono diversi perché ogni dispositivo ha credenziali univoche, ma sono entrambi validi e funzionanti durante l'accesso con il telefono associato.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Ripristinare altri account che richiedono ulteriori verifiche

Se si usano le notifiche push con gli account personali, aziendali o dell'Istituto di istruzione, verrà visualizzato un avviso su schermo che indica che è necessario fornire una verifica aggiuntiva prima di poter recuperare le informazioni. Dato che le notifiche push richiedono l'uso di credenziali associate al dispositivo specifico e che non vengono mai inviate in rete, è necessario dimostrare la propria identità prima della creazione delle credenziali nel dispositivo.

Per gli account Microsoft personali, è possibile provare la propria identità immettendo la password insieme a un numero di telefono o posta elettronica alternativo. Per gli account aziendali o dell'istituto di istruzione, è necessario eseguire la scansione di un codice a matrice fornito dal provider dell'account.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Per fornire un'ulteriore verifica per gli account personali

1. Nella schermata **Account** dell'app Microsoft Authenticator selezionare la freccia in giù accanto all'account da ripristinare.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Selezionare **Accedi per ripristinare**, digitare la password e quindi confermare l'indirizzo di posta elettronica o il numero di telefono come ulteriore verifica.

    ![App Microsoft Authenticator che consente di immettere le informazioni di accesso](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Per fornire un'ulteriore verifica per gli account aziendali o dell'istituto di istruzione

1. Nella schermata **Account** dell'app Microsoft Authenticator selezionare la freccia in giù accanto all'account da ripristinare.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Selezionare **Scansione codice QR per ripristinare** e quindi eseguire la scansione del codice a matrice.

    ![App Microsoft Authenticator che consente di eseguire la scansione del codice a matrice](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Per altre informazioni sui codici QR e su come ottenerne uno, vedere [Introduzione all'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) o [configurare le informazioni di sicurezza per l'uso di un'app di autenticazione, a](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)seconda che l'amministratore abbia attivato le info di sicurezza.
    >
    >Se si configura l'app Microsoft Authenticator per la prima volta, è possibile che venga visualizzato un messaggio in cui viene richiesto se consentire all'app di accedere alla fotocamera (iOS) o se consentire all'app di scattare foto e registrare video (Android). Selezionare **Consenti** per consentire all'app Authenticator di accedere alla fotocamera per scattare una foto del codice a matrice nel passaggio successivo. Se non si consente l'accesso alla fotocamera, è comunque possibile configurare l'app Authenticator, ma sarà necessario aggiungere le informazioni sul codice manualmente. Per altre informazioni su come aggiungere manualmente un codice, vedere [Aggiungere manualmente un account all'app](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Risolvere i problemi di backup e ripristino

Esistono diversi motivi per cui il backup potrebbe non essere disponibile:

- **Modifica dei sistemi operativi.** Il backup viene archiviato in iCloud per iOS e nel provider di archiviazione cloud di Microsoft per Android. Questo significa che il backup non è disponibile se si passa tra dispositivi Android e iOS. Se si esegue l'opzione, è necessario ricreare manualmente gli account all'interno dell'app Microsoft Authenticator.

- **Problemi di rete.** Se si verificano problemi relativi alla rete, assicurarsi di essere connessi alla rete e di avere eseguito l'accesso al proprio account.

- **Problemi relativi agli account.** Se si verificano problemi relativi all'account, assicurarsi di essere connessi correttamente all'account. Per iOS questo significa che è necessario essere connessi a iCloud usando lo stesso account AppleID dell'iPhone.

- **Eliminazione accidentale.** È possibile che l'account di backup sia stato eliminato dal dispositivo precedente o durante la gestione dell'account di archiviazione nel cloud. In questo caso, è necessario ricreare manualmente l'account all'interno dell'app.

- **Account Microsoft Authenticator esistenti.** Se sono già stati configurati account nell'app Microsoft Authenticator, l'app non sarà in grado di recuperare gli account di backup. Impedendo il ripristino ci si assicura che i dettagli dell'account non vengano sovrascritti con informazioni non aggiornate. In questo caso, è necessario rimuovere eventuali informazioni sugli account esistenti dagli account esistenti configurati nell'app Authenticator prima di poter ripristinare il backup.

- **Il backup è obsoleto.** Se le informazioni di backup non sono aggiornate, è possibile che venga richiesto di aggiornare le informazioni eseguendo di nuovo l'accesso all'account Microsoft Recovery. L'account di ripristino è il account Microsoft personale usato inizialmente per archiviare il backup. Se è necessario un accesso, verrà visualizzato un punto rosso sul menu o sulla barra delle azioni. Dopo aver selezionato il punto rosso, verrà chiesto di eseguire di nuovo l'accesso per aggiornare le informazioni.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il backup e il ripristino delle credenziali dell'account nel nuovo dispositivo, è possibile continuare a usare l'app Microsoft Authenticator per verificare l'identità. Per altre informazioni, vedere [accedere agli account usando l'app Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Articoli correlati

- [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
