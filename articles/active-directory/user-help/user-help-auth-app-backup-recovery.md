---
title: Eseguire il backup e il ripristino degli account con l'app Microsoft Authenticator - Azure AD
description: Informazioni su come eseguire il backup e il ripristino delle credenziali dell'account di cui è stato eseguito il backup usando l'app Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b13de16b2080d28d3b779921456cfb20d99f0af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416000"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Eseguire il backup e il ripristino delle credenziali dell'account usando l'app Microsoft Authenticator

**Si applica a:**

- Dispositivi iOS che eseguono la versione 5.7.0 e successive

- Dispositivi Android che eseguono la versione 6.6.0 e successive

L'app Microsoft Authenticator esegue il backup delle credenziali dell'account e delle impostazioni delle app correlate, ad esempio l'ordine degli account, nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

Ogni percorso di archiviazione di backup richiede un account Microsoft personale, mentre iOS richiede anche un account iCloud. È possibile avere più account archiviati nella stessa posizione. Ad esempio, è possibile avere un account personale, un account aziendale o dell'istituto di istruzione e un account non Microsoft personale come per Facebook, Google e così via.

> [!IMPORTANT]
> Vengono archiviate solo le credenziali degli account personali e di terze parti, che includono il nome utente e il codice di verifica dell'account richiesto per dimostrare la propria identità. Non vengono archiviate altre informazioni associate agli account, inclusi messaggi di posta elettronica o file. Gli account non vengono inoltre associati o condivisi in alcun modo o con qualsiasi altro prodotto o servizio. Infine, l'amministratore IT non otterrà alcuna informazione riguardo a questi account.

## <a name="back-up-your-account-credentials"></a>Eseguire il backup delle credenziali dell'account

Prima di poter eseguire il backup delle proprie credenziali, è necessario avere:

- Un [account Microsoft](https://account.microsoft.com/account) personale da usare come account di ripristino.

- **Solo per iOS,** è necessario avere un [account iCloud](https://www.icloud.com/) per la posizione di archiviazione effettiva.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Per attivare il backup sul cloud per i dispositivi iOS

- Nel dispositivo iOS selezionare **Impostazioni**, selezionare **Backup** e quindi attivare il **backup iCloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account iCloud.

    ![Schermata delle impostazioni di iOS, che mostra la posizione delle impostazioni per il backup iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Per attivare il backup sul cloud per i dispositivi Android

- Nel dispositivo Android selezionare **Impostazioni**, selezionare **Backup** e quindi attivare il **Backup sul cloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account cloud.

    ![Schermata delle impostazioni di Android che mostra la posizione delle impostazioni per il backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Ripristinare le credenziali dell'account nel nuovo dispositivo

È possibile recuperare le credenziali dell'account dall'account cloud, ma è prima necessario assicurarsi che l'account che si sta ripristinando non esista nell'app Microsoft Authenticator. Se, ad esempio, si sta recuperando il proprio account Microsoft personale, è necessario assicurarsi di non avere un account Microsoft personale già configurato nell'app Authenticator. Questo controllo è importante per assicurarsi che non si stia sovrascrivendo o cancellando un account esistente per errore.

### <a name="to-recover-your-information"></a>Per ripristinare le informazioni

1. Nel dispositivo mobile aprire l'app Microsoft Authenticator e selezionare **Inizia il ripristino** nella parte inferiore della schermata.

    ![App Microsoft Authenticator che mostra dove fare clic su Inizia il ripristino](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Accedere all'account di ripristino, usando lo stesso account Microsoft personale usato durante il processo di backup.

    Le credenziali dell'account vengono ripristinate nel nuovo dispositivo.

Dopo aver completato il ripristino, è possibile notare che i codici di verifica dell'account Microsoft personale nell'app Microsoft Authenticator sono diversi nel telefono vecchio e in quello nuovo. I codici sono diversi perché ogni dispositivo ha credenziali univoche, ma sono entrambi validi e funzionanti durante l'accesso con il telefono associato.

## <a name="recover-accounts-requiring-more-verification"></a>Ripristinare gli account che richiedono una maggiore verifica

Se si usano le notifiche push con gli account personali, aziendali o dell'istituto di istruzione, verrà visualizzato un avviso su schermo che indica che è necessario fornire un'ulteriore verifica prima che sia possibile recuperare le informazioni. Dato che le notifiche push richiedono l'uso di credenziali associate al dispositivo specifico e che non vengono mai inviate in rete, è necessario dimostrare la propria identità prima della creazione delle credenziali nel dispositivo.

Per gli account Microsoft personali, è possibile provare la propria identità immettendo la password insieme a un numero di telefono o posta elettronica alternativo. Per gli account aziendali o dell'istituto di istruzione, è necessario eseguire la scansione di un codice a matrice fornito dal provider dell'account.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Per fornire una maggiore verifica per gli account personali

1. Nella schermata **account** dell'app Microsoft Authenticator toccare l'account che si vuole ripristinare per aprire la visualizzazione a schermo intero dell'account.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="App Microsoft Authenticator, che mostra i riquadri degli account disponibili" border="true":::

1. Toccare il riquadro per l'account che si sta ripristinando e quindi toccare l'opzione per eseguire l'accesso per il ripristino. Immettere la password e quindi confermare l'indirizzo di posta elettronica o il numero di telefono come verifica aggiuntiva.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="L'autenticatore presenta una password monouso come codice di verifica" border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Per fornire una maggiore verifica per gli account aziendali o dell'Istituto di istruzione

1. Nella schermata **account** dell'app Microsoft Authenticator toccare l'account che si vuole ripristinare per aprire la visualizzazione a schermo intero dell'account.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="App Microsoft Authenticator, che mostra i riquadri degli account disponibili" border="true":::

1. Nella visualizzazione a schermo intero toccare l'opzione per eseguire la scansione di un codice a matrice per il ripristino completo.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="L'autenticatore presenta una password monouso come codice di verifica" border="true":::

>[!NOTE]
>Per altre informazioni sui codici a matrice e su come ottenerne uno, vedere [Introduzione all'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) o [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), a seconda che l'amministratore abbia attivato o meno le informazioni di sicurezza.
>
>Se si configura l'app Microsoft Authenticator per la prima volta, è possibile che venga visualizzato un messaggio in cui viene richiesto se consentire all'app di accedere alla fotocamera (iOS) o se consentire all'app di scattare foto e registrare video (Android). Selezionare **Consenti** per consentire all'app Authenticator di accedere alla fotocamera per scattare una foto del codice a matrice nel passaggio successivo. Se non si consente l'accesso alla fotocamera, è comunque possibile configurare l'app Authenticator, ma sarà necessario aggiungere le informazioni sul codice manualmente. Per altre informazioni su come aggiungere manualmente un codice, vedere [Aggiungere manualmente un account all'app](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Risoluzione dei problemi di backup e ripristino

Esistono diversi motivi per cui il backup potrebbe non essere disponibile

- **Modifica dei sistemi operativi**: il backup viene archiviato in iCloud per iOS e nel provider di archiviazione cloud di Microsoft per Android. Questo significa che il backup non è disponibile se si passa da dispositivi Android a dispositivi iOS o viceversa. Se si esegue il passaggio, è necessario ricreare manualmente gli account all'interno dell'app Microsoft Authenticator.

- **Problemi relativi alla rete**: se si verificano problemi relativi alla rete, assicurarsi di essere connessi alla rete e di avere eseguito l'accesso al proprio account.

- **Problemi relativi all'account**: se si verificano problemi relativi all'account, assicurarsi di aver eseguito correttamente l'accesso all'account. Per iOS questo significa che è necessario aver eseguito l'accesso a iCloud usando lo stesso account AppleID dell'iPhone.

- **Eliminazione accidentale**: è possibile che l'account di backup sia stato eliminato dal dispositivo precedente o durante la gestione dell'account di archiviazione nel cloud. In questo caso, è necessario ricreare manualmente l'account all'interno dell'app.

- **Account Microsoft Authenticator esistenti**: Se sono già stati configurati account nell'app Microsoft Authenticator, l'app non sarà in grado di recuperare gli account di backup. Impedendo il ripristino ci si assicura che i dettagli dell'account non vengano sovrascritti con informazioni non aggiornate. In questo caso, è necessario rimuovere eventuali informazioni sugli account esistenti dagli account esistenti configurati nell'app Authenticator prima di poter ripristinare il backup.

- **Backup non aggiornato**: se le informazioni di backup non sono aggiornate, è possibile che venga richiesto di aggiornare le informazioni eseguendo di nuovo l'accesso all'account Microsoft Recovery. L'account di ripristino è l'account Microsoft personale usato inizialmente per archiviare il backup. Se è necessario un accesso, verrà visualizzato un punto rosso sul menu o sulla barra delle azioni oppure verrà visualizzata un'icona con il punto esclamativo che richiede di eseguire l'accesso per completare il ripristino dal backup. Dopo aver selezionato l'icona appropriata, verrà chiesto di eseguire di nuovo l'accesso per aggiornare le informazioni.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il backup e il ripristino delle credenziali dell'account nel nuovo dispositivo, è possibile continuare a usare l'app Microsoft Authenticator per verificare l'identità. Per altre informazioni, vedere [Accedere agli account con l'app Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Articoli correlati

- [Che cos'è l'app Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
