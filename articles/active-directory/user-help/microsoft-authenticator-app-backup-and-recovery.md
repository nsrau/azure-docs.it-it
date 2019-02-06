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
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 56ea48accea135f79e4c8984238002f2eec5167b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167411"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Eseguire il backup e il ripristino delle credenziali dell'account con l'app Microsoft Authenticator

**Si applica a:**

- Dispositivi iOS

L'app Microsoft Authenticator esegue il backup delle credenziali dell'account e delle impostazioni delle app correlate, ad esempio l'ordine degli account, nel cloud. Al termine del backup, è anche possibile usare l'app per ripristinare le informazioni in un nuovo dispositivo, evitando potenzialmente di rimanere bloccati senza accesso al dispositivo o di dover ricreare gli account.

>[!IMPORTANT]
> Sono necessari un account Microsoft personale e un account iCloud per ogni posizione di archiviazione di backup. All'interno di tale posizione di archiviazione, è però possibile eseguire il backup di vari account. Ad esempio, è possibile avere un account personale, un account dell'istituto di istruzione e un account di terze parti come Facebook, Google e così via.<br><br>Vengono archiviate solo le credenziali degli account personali e di terze parti, che includono il nome utente e il codice di verifica dell'account richiesto per dimostrare la propria identità. Non vengono archiviate altre informazioni associate agli account, inclusi messaggi di posta elettronica o file. Gli account non vengono inoltre associati o condivisi in alcun modo o con qualsiasi altro prodotto o servizio. Infine, l'amministratore IT non otterrà alcuna informazione riguardo a questi account.

## <a name="back-up-your-account-credentials"></a>Eseguire il backup delle credenziali dell'account
Prima di poter eseguire il backup delle proprie credenziali, è necessario avare:

- Un [account Microsoft](https://account.microsoft.com/account) personale da usare come account di ripristino.

- Un [account iCloud](https://www.icloud.com/) per la posizione di archiviazione effettiva. 

La richiesta di effettuare l'accesso a entrambi gli account insieme offre una maggiore sicurezza per le informazioni del backup.

**Per attivare il backup di iCloud**
-   Nel dispositivo iOS selezionare **Impostazioni**, selezionare **Backup** e quindi attivare il **backup iCloud**.

    Viene eseguito il backup delle credenziali dell'account nell'account iCloud.

    ![Schermata delle impostazioni di iOS, che mostra la posizione delle impostazioni per il backup iCloud](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Ripristinare le credenziali dell'account nel nuovo dispositivo
È possibile ripristinare le credenziali dell'account dal proprio account iCloud, usando lo stesso account di ripristino di Microsoft configurato quando è stato eseguito il backup delle informazioni.

### <a name="to-recover-your-information"></a>Per ripristinare le informazioni
1.  Nel dispositivo iOS aprire l'app Microsoft Authenticator e selezionare **Inizia il ripristino** nella parte inferiore della schermata.

    ![App Microsoft Authenticator che mostra dove fare clic su Inizia il ripristino](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Accedere all'account di ripristino, usando lo stesso account Microsoft personale usato durante il processo di backup.

    Le credenziali dell'account vengono ripristinate nel nuovo dispositivo.

Dopo aver completato il ripristino, è possibile notare che i codici di verifica dell'account Microsoft personale nell'app Microsoft Authenticator sono diversi nel telefono vecchio e in quello nuovo. I codici sono diversi perché ogni dispositivo ha credenziali univoche, ma sono entrambi validi e funzionanti durante l'accesso con il telefono associato.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Ripristinare altri account che richiedono ulteriori verifiche
Se si usano le notifiche push con gli account personali, aziendali o dell'istituto di istruzione, verrà visualizzato un avviso su schermo che indica che è necessario fornire un'ulteriore verifica prima che sia possibile recuperare le informazioni. Dato che le notifiche push richiedono l'uso di credenziali associate al dispositivo specifico e che non vengono mai inviate in rete, è necessario dimostrare la propria identità prima della creazione delle credenziali nel dispositivo.

Per gli account Microsoft personali, è possibile provare la propria identità immettendo la password insieme a un numero di telefono o posta elettronica alternativo. Per gli account aziendali o dell'istituto di istruzione, è necessario eseguire la scansione di un codice a matrice fornito dal provider dell'account.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Per fornire un'ulteriore verifica per gli account personali
1.  Nella schermata **Account** dell'app Microsoft Authenticator selezionare la freccia in giù accanto all'account da ripristinare.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Selezionare **Accedi per ripristinare**, digitare la password e quindi confermare l'indirizzo di posta elettronica o il numero di telefono come ulteriore verifica.

    ![App Microsoft Authenticator che consente di immettere le informazioni di accesso](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Per fornire un'ulteriore verifica per gli account aziendali o dell'istituto di istruzione
1.  Nella schermata **Account** dell'app Microsoft Authenticator selezionare la freccia in giù accanto all'account da ripristinare.

    ![App Microsoft Authenticator che mostra gli account disponibili con le frecce in giù associate](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-additional-accts.png)

2.  Selezionare **Scansione codice QR per ripristinare** e quindi eseguire la scansione del codice a matrice.

    ![App Microsoft Authenticator che consente di eseguire la scansione del codice a matrice](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Per altre informazioni su come ottenere un codice a matrice, vedere [Introduzione all'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-how-to#add-accounts-to-the-app) o [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app#to-use-the-microsoft-authenticator-app), a seconda che l'amministratore abbia attivato o meno le informazioni di sicurezza.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Risoluzione dei problemi di backup e ripristino
Esistono diversi motivi per cui il backup potrebbe non essere disponibile:

-   **Modifica dei sistemi operativi.** Il backup viene archiviato nell'opzione di archiviazione cloud specificata dal sistema operativo del telefono e questo significa che il backup non è disponibile se si passa tra Android e iOS. In questo caso, è necessario ricreare manualmente l'account all'interno dell'app.

-   **Problemi di rete o di password.** Assicurarsi di essere connessi a una rete e di avere eseguito l'accesso all'account iCloud personale usando lo stesso AppleID usato nell'ultimo iPhone.

-   **Eliminazione accidentale.** È possibile che l'account di backup sia stato eliminato dal dispositivo precedente o durante la gestione dell'account di archiviazione nel cloud. In questo caso, è necessario ricreare manualmente l'account all'interno dell'app.

-   **Account Microsoft Authenticator esistenti.** Se sono già stati configurati account nell'app Microsoft Authenticator, l'app non sarà in grado di recuperare gli account di backup. Impedendo il ripristino ci si assicura che i dettagli dell'account non vengano sovrascritti con informazioni non aggiornate. In questo caso, è necessario rimuovere eventuali informazioni sugli account esistenti dagli account esistenti configurati nell'app Authenticator prima di poter ripristinare il backup.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver eseguito il backup e il ripristino delle credenziali dell'account nel nuovo dispositivo, è possibile continuare a usare l'app Microsoft Authenticator per verificare l'identità.

## <a name="related-topics"></a>Argomenti correlati
- [Introduzione all'app Microsoft Authenticator](microsoft-authenticator-app-how-to.md)  
- [Domande frequenti sull'app Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
