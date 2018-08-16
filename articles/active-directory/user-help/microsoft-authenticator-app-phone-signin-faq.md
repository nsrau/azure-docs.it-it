---
title: Accesso tramite telefono in Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Contiene informazioni e istruzioni dettagliate su come usare il telefono per accedere all'account aziendale o dell'istituto di istruzione o all'account Microsoft personale invece di digitare la password.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: a6df90a1ae1dddbf7f76a7ef6c7e13d9e645dd9c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528426"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Accedere con il telefono, non con la password

L'app Microsoft Authenticator non solo permette di proteggere gli account eseguendo la verifica in due passaggi dopo aver immesso la password, ma può ora sostituire completamente l'immissione della password per l'account Microsoft.

Questa funzionalità è disponibile su dispositivi iOS e Android 6.0 e versioni successive.

## <a name="how-it-works"></a>Funzionamento

Molti utenti usano l'app Microsoft Authenticator per eseguire la verifica in due passaggi quando si accede all'account personale di Microsoft. Si digita la password e quindi si accede all'app per approvare una notifica o ottenere un codice di verifica. Con l'accesso tramite telefono è possibile evitare di usare la password ed eseguire tutte le operazioni di verifica dell'identità sul telefono. Poiché l'accesso tramite telefono è un tipo di verifica in due passaggi, è comunque necessario verificare la propria identità specificando un'informazione nota e un oggetto di cui si è in possesso. Il telefono è l'oggetto di cui si è in possesso, mentre il PIN o la chiave biometrica del telefono è l'informazione nota.

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>Attivare l'accesso tramite telefono per l'account aziendale o dell'istituto di istruzione

Se l'organizzazione usa Azure Active Directory per la gestione delle identità e l'autenticazione, è possibile usare l'accesso tramite telefono dopo averlo attivato nell'app Microsoft Authenticator. 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>Per attivare l'accesso tramite telefono per l'account

- **Se nel telefono è visualizzata questa icona![Icona di accesso tramite telefono](./media/microsoft-authenticator-app-phone-signin-faq/icon.png).** L'accesso tramite telefono è già stato configurato. Potrebbe essere necessario aggiornare l'account per aggiungere le notifiche push in modo da poter ricevere notifiche relative alle richieste di autenticazione esternamente all'app.

- **Se si usa già l'app Microsoft Authenticator per la verifica in due passaggi.** Passare alla schermata **Account** dell'app, selezionare la freccia a discesa per l'account aziendale o dell'istituto di istruzione e quindi selezionare **Abilita l'accesso tramite telefono**.

- **Se si usa l'app Microsoft Authenticator per l'account personale, ma non per l'account aziendale o dell'istituto di istruzione.** Seguire le istruzioni per la verifica in due passaggi dell'organizzazione e quindi attivare l'accesso tramite telefono. Per altre informazioni, vedere [Introduzione all'app Microsoft Authenticator](microsoft-authenticator-app-how-to.md)

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>Per usare l'accesso tramite telefono per accedere all'account aziendale o dell'istituto di istruzione

1.  Accedere all'account aziendale o dell'istituto di istruzione normalmente.

    Dopo aver digitato il nome utente, verrà visualizzata una pagina con un numero a due cifre, in cui viene richiesto di approvare l'accesso tramite l'app Microsoft Authenticator. Se non si vuole usare questo metodo di accesso, è possibile selezionare **Usa la tua password** e quindi accedere usando la password.

    ![Pagina di accesso](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. Nell'app Microsoft Authenticator verrà visualizzata una notifica che chiede **Approvare l'accesso?**. Scegliere **Approva** se si riconosce il tentativo di accesso. In caso contrario, scegliere **Nega**.

3. Toccare lo stesso numero visualizzato nella schermata **Approvare l'accesso?**. Usare il PIN del telefono o la chiave biometrica per completare l'autenticazione.
 
    ![Pagina Approvare l'accesso?](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>Attivare l'accesso tramite telefono per l'account personale

È possibile attivare l'accesso tramite telefono per l'account Microsoft personale, ad esempio l'account usato per accedere a Outlook.com, Xbox o Skype.

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>Per attivare l'accesso tramite telefono per l'account personale

- **Se non si usa l'app Microsoft Authenticator.** Scaricare e installare l'app Microsoft Authenticator e quindi aggiungere l'account Microsoft personale accedendo all'account dalla schermata **Aggiungi account** dell'app Microsoft Authenticator. Per altre informazioni, vedere [Introduzione all'app Microsoft Authenticator](microsoft-authenticator-app-how-to.md).
Poiché i nuovi account vengono attivati automaticamente, è possibile iniziare immediatamente a usare l'accesso tramite telefono.

- **Se si usa già l'app Microsoft Authenticator per la verifica in due passaggi.** Passare alla schermata **Account** dell'app, selezionare la freccia a discesa per l'account aziendale o dell'istituto di istruzione e quindi selezionare **Abilita l'accesso tramite telefono**.

>[!NOTE]
>Per proteggere l'account, l'app richiede un PIN o un blocco biometrico sul dispositivo. Se il telefono resta sbloccato, l'app chiede di configurare un blocco di sicurezza prima di attivare l'accesso tramite telefono.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>Per usare l'accesso tramite telefono per accedere all'account personale

1.  Passare alla pagina di accesso dell'account Microsoft personale e quindi selezionare il collegamento **Usare l'app Microsoft Authenticator** invece di digitare la password.

    Microsoft invia una notifica al telefono cellulare dell'utente. 

2.  Approvare la notifica.

## <a name="phone-sign-in-faq"></a>Domande frequenti sull'accesso tramite telefono

|**Domanda**|**Soluzione**|
|--------------|-------------|
|**Perché l'accesso tramite telefono è più sicuro dell'immissione di una password?**|Oggi la maggior parte delle persone accede a siti Web o alle app usando un nome utente e una password. Sfortunatamente, le password possono essere smarrite, rubate o individuate da pirati informatici.<br><br>Una volta installata, l'app Microsoft Authenticator crea una chiave nel telefono per sbloccare l'account protetto dal PIN o dal blocco biometrico del telefono. Questa chiave viene quindi usata per dimostrare l'identità dell'utente durante l'accesso.<br><br>**Importante**<br>I dati vengono usati solo per proteggere la chiave in locale. Non vengono mai inviati né archiviati nel cloud.|
|**Questa funzionalità sostituisce la verifica in due passaggi? È consigliabile disattivare la verifica in due passaggi?**| Microsoft sta lavorando per espandere l'ambito dell'accesso tramite telefono, ma al momento questa funzionalità non è supportata in alcuni casi all'interno dell'ecosistema Microsoft. In questi casi, viene usata ancora la verifica in due passaggi per eseguire un accesso sicuro. Per questo motivo, no, non è consigliabile disattivare la verifica in due passaggi per l'account.|
|**Se la verifica in due passaggi resta attiva per l'account, è necessario approvare due notifiche?**| No. L'accesso all'account Microsoft tramite il telefono viene considerato una verifica in due passaggi e di conseguenza non è necessaria una seconda approvazione.|
|**Che cosa succede se si perde il telefono o se il telefono non è a portata di mano? Come si accede all'account in questi casi?**| È sempre possibile selezionare il collegamento Usa la tua password nella pagina di accesso per tornare a usare la password. Tuttavia, se si usa la verifica in due passaggi, sarà comunque necessario usare un secondo metodo per verificare l'identità.<br><br>**Importante**<br>È consigliabile usare più di un metodo di verifica aggiornato associato all'account.<br><br>È possibile gestire i metodi di verifica per gli account personali dalla pagina [Impostazioni di sicurezza](https://account.live.com/proofs/manage). Per gli account aziendali o dell'istituto di istruzione, è possibile passare alla pagina [Verifica aggiuntiva di sicurezza](https://aka.ms/MFASetup) dell'organizzazione o alla pagina **Proteggi l'account** se l'amministratore ha attivato le informazioni di sicurezza. Per altri dettagli sulle informazioni di sicurezza, vedere [Gestire le informazioni di sicurezza](security-info-manage-settings.md).<br><br>Se non si è in grado di gestire i metodi di verifica, è necessario contattare l'amministratore.|
|**Come si smette di usare questa funzionalità per tornare a usare la password?**|Per gli account personali, selezionare il collegamento **Usa la tua password** durante l'accesso. La scelta più recente viene memorizzata dal sistema e proposta per impostazione predefinita al successivo accesso. Se si vuole tornare all'accesso tramite telefono, selezionare il collegamento **Usa un'app** durante l'accesso.<br><br>Per gli account aziendali o dell'istituto di istruzione, è necessario rimuovere l'account aprendo l'app Microsoft Authenticator, selezionando il menu **Modifica account** e quindi eliminando l'account.|
|**Perché non è possibile usare più di un account aziendale o dell'istituto di istruzione per l'accesso tramite telefono?**| Un telefono deve essere registrato in un solo account aziendale o dell'istituto di istruzione. Se si vuole attivare l'accesso tramite telefono per un account aziendale o dell'istituto di istruzione diverso, è innanzitutto necessario annullare la registrazione dell'account precedente tramite la pagina **Impostazioni**.|
|**È possibile accedere al PC tramite il telefono?**| Per il PC, è consigliabile accedere con Windows Hello in Windows 10. Windows Hello permette di usare il viso, le impronte digitali o un PIN per accedere.|
|**È possibile usare l'accesso tramite telefono con un dispositivo Windows Phone?**| No. Questa funzionalità non è supportata dall'app Microsoft Authenticator su Windows Phone.|

## <a name="next-steps"></a>Passaggi successivi

-   In caso di domande generali sull'app, vedere [Domande frequenti su Microsoft Authenticator](microsoft-authenticator-app-faq.md)

-   Per altre informazioni sulla verifica in due passaggi, vedere [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

-   Per altri dettagli sulle informazioni di sicurezza, vedere [Gestire le informazioni di sicurezza](security-info-manage-settings.md)
