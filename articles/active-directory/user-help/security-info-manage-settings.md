---
title: Gestire le informazioni di sicurezza personali - Azure Active Directory | Microsoft Docs
description: Informazioni su come gestire le informazioni di sicurezza, incluso come usare le impostazioni di verifica in due passaggi.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3b83ded2ef996118ebd8d37b177b0334d3bae
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211003"
---
# <a name="manage-your-security-info-preview"></a>Gestire le informazioni di sicurezza (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

È possibile usare le informazioni di sicurezza per accedere al proprio account aziendale o dell'istituto di istruzione o per reimpostare la password.

Quando si accede, a seconda delle impostazioni dell'organizzazione è possibile che venga visualizzata una casella di controllo con la dicitura **Non chiedermelo più per X giorni**. Questa casella di controllo consente di rimanere connessi al dispositivo per il numero di giorni specificato dall'amministratore, senza dover verificare nuovamente le credenziali.

## <a name="change-your-info"></a>Modificare le informazioni
È possibile aggiornare o aggiungere informazioni di sicurezza o modificarne l'impostazione predefinita, in base a quanto consentito dall'amministratore e dall'organizzazione.

### <a name="to-change-your-info"></a>Per modificare le informazioni

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Passare a myapps.microsoft.com, selezionare il proprio nome nell'angolo superiore destro della pagina e quindi selezionare **Profilo**.

3. Nell'area **Gestisci account** selezionare **Modifica le info di sicurezza**.

    ![Schermata Profilo con il collegamento Modifica le info di sicurezza evidenziato](media/security-info/security-info-profile.png)

4. Usare il metodo predefinito per approvare l'accesso e visualizzare i dettagli delle informazioni di sicurezza correnti, se l'amministratore ha configurato questa esperienza per l'organizzazione.

5. Nella pagina **Proteggi l'account** è possibile:

    - Selezionare **Aggiungi info di sicurezza** per aggiungere altri metodi.

    - Selezionare **Modifica l'impostazione predefinita** per modificare il metodo predefinito.

    - Selezionare l'icona a forma di **matita** accanto a un metodo esistente per aggiornare le informazioni.

    ![Schermata delle informazioni di sicurezza con le informazioni esistenti, modificabili](media/security-info/security-info-edit.png)

6. Dopo aver apportato le modifiche è possibile uscire dalla pagina e salvare automaticamente le modifiche.

Se queste opzioni non vengono visualizzate o non si riesce ad accedere alla pagina myapps.microsoft.com, è possibile che l'organizzazione usi opzioni personalizzate o una pagina personalizzata. Sarà necessario contattare l'amministratore per ulteriore assistenza.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Gestire le informazioni di sicurezza in caso di smarrimento o potenziale compromissione di un dispositivo

In caso di perdita o di compromissione di un dispositivo sarà necessario ripetere il processo di verifica per tutti i dispositivi precedentemente attendibili.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Per gestire le informazioni di sicurezza in caso di smarrimento o potenziale compromissione di un dispositivo

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Passare a myapps.microsoft.com, selezionare il proprio nome nell'angolo superiore destro della pagina e quindi selezionare **Profilo**.

3. Nell'area **Gestisci account** selezionare **Ignora MFA nei dispositivi memorizzati**.
    
    Questa opzione prevede la necessità di eseguire nuovamente il processo di autenticazione a più fattori (MFA) dopo avere eseguito l'accesso.

    ![Schermata Profilo con il collegamento Ignora evidenziato](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Problemi comuni e soluzioni relativi alle informazioni di sicurezza

Questo articolo aiuta a risolvere i problemi relativi alle informazioni di sicurezza, inclusi i problemi correlati alla verifica in due passaggi.

|Problema|Soluzione|
|-------|--------|
|Il telefono non è al momento disponibile|Capita di non avere il proprio telefono a disposizione in un dato momento, ma che sia comunque necessario accedere al proprio account di lavoro o dell'istituto di istruzione. Per risolvere il problema, è possibile accedere usando un metodo di autenticazione diverso che non richiede l'uso del telefono personale, ad esempio il numero di telefono dell'ufficio. Per aggiungere altri metodi alle informazioni di sicurezza, è possibile seguire i passaggi descritti nella sezione [Modificare le informazioni](#change-your-info).|
|Il telefono è stato perso o rubato|Sfortunatamente può succedere che il telefono venga perso o rubato. Si consiglia vivamente in questo caso di informare l'organizzazione in modo che il personale IT possa reimpostare le password delle app e cancellare eventuali dispositivi memorizzati dall'elenco dei dispositivi attendibili. È anche possibile ignorare i dispositivi attendibili attenendosi alla procedura descritta nella sezione [Gestire le informazioni di sicurezza in caso di smarrimento o potenziale compromissione di un dispositivo](#manage-your-security-info-for-a-lost-or-potentially-compromised-device).|
|Nuovo numero di telefono|È possibile procedere in due modi per risolvere questo problema. È possibile accedere usando un metodo di autenticazione alternativo che non richiede l'inserimento del numero di telefono, ad esempio la posta elettronica, oppure, se ciò non è possibile, si può contattare il personale IT della propria organizzazione e chiedere di cancellare le impostazioni. Per aggiungere altri metodi alle informazioni di sicurezza, è possibile seguire i passaggi descritti nella sezione [Modificare le informazioni](#change-your-info).|
|Il metodo predefinito è errato|È possibile aggiornare il metodo predefinito nelle opzioni di sicurezza. Per i dettagli specifici, vedere la sezione [Modificare le informazioni](#change-your-info).|
|Non si ricevono messaggi o chiamate sul telefono|Se in precedenza si ricevevano messaggi e telefonate senza problemi, si tratta molto probabilmente di un problema del provider del servizio di telefonia, non dell'account. Assicurarsi di avere una copertura del segnale adeguata e che sia possibile ricevere SMS e telefonate. Chiedere a un amico di provare a chiamare o a inviare un SMS.<br><br>Se si ricevono gli SMS e le telefonate, ma non si riceve ancora la notifica, provare a usare un metodo diverso. Per aggiungere altri metodi alle informazioni di sicurezza, seguire i passaggi descritti nella sezione [Modificare le informazioni](#change-your-info). Se non si può aggiungere un altro metodo, contattare il supporto tecnico aziendale e chiedere di cancellare le impostazioni in modo da poter configurare i metodi al successivo accesso.<br><br>Se si verificano spesso problemi a causa della scarsa qualità del segnale, è consigliabile usare l'app Microsoft Authenticator sul dispositivo mobile. L'app può generare codici di sicurezza casuali che consentono di accedere e che non richiedono segnale telefonico o connessione Internet. Per altre informazioni sull'app Microsoft Authenticator, vedere l'articolo [Introduzione all'app Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/user-help-auth-app-download-install).|
|Nessuna delle opzioni in questa tabella ha risolto il problema|Se dopo aver provato queste procedure i problemi persistono, contattare il supporto tecnico aziendale per assistenza.|

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle informazioni di sicurezza sono disponibili in [Panoramica delle informazioni di sicurezza (anteprima)](user-help-security-info-overview.md).

- Informazioni sulla verifica in due passaggi sono disponibili nell'articolo [Panoramica della verifica in due passaggi](user-help-two-step-verification-overview.md). 

- Per informazioni su come configurare i dispositivi nell'area delle informazioni di sicurezza, attenersi a uno di questi articoli procedurali:

    - [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md)

    - [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md)

    - [Set up security info to use text messages](security-info-setup-text-msg.md) (Configurare le informazioni di sicurezza per l'uso di SMS)

    - [Configurare le informazioni di sicurezza per l'uso di e-mail](security-info-setup-email.md)

    - [Set up security info to use security questions](security-info-setup-questions.md) (Configurare le informazioni di sicurezza per l'uso delle domande di sicurezza)

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
