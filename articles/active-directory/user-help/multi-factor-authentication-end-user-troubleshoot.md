---
title: Risolvere i problemi della verifica in due passaggi - Azure AD | Microsoft Docs
description: Questo articolo contiene istruzioni sulle operazioni da eseguire se si verifica un problema con Azure Multi-Factor Authentication e la verifica in due passaggi.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: ec79adcaf7cbef778b8c9e572ae932d5ceb47aa6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090378"
---
# <a name="get-help-with-two-step-verification"></a>Informazioni sulla verifica in due passaggi

La verifica in due passaggi è una funzionalità di sicurezza che l'organizzazione usa per proteggere gli account. Si tratta di un metodo più sicuro rispetto alla sola password perché si basa su due forme di autenticazione, ovvero un elemento noto e un elemento che l'utente ha con sé. L'elemento noto è la password, mentre l'elemento che l'utente ha con sé è un dispositivo o il telefono. L'uso della verifica in due passaggi consente di impedire ai pirati informatici di eseguire l'accesso come utente, anche se vengono in possesso della relativa password.

Microsoft offre la verifica in due passaggi, ma è l'organizzazione che decide se usare tale funzionalità. L'utente non può rifiutare l'uso della funzionalità se l'organizzazione lo richiede, proprio come non è possibile rifiutare esplicitamente l'uso di una password per proteggere l'account.

>[!Note]
>Per altre informazioni sull'uso della verifica in due passaggi con l'account Microsoft personale, vedere l'articolo [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="why-do-i-need-to-use-another-verification-method"></a>Perché è necessario usare un altro metodo di verifica?

I motivi per cui potrebbe essere necessario usare un metodo di verifica alternativo con l'account sono diversi. Ad esempio: 

- **Telefono o dispositivo dimenticato.** A volte si lascia il telefono a casa, ma si ha lo stesso la necessità di accedere alle risorse di lavoro. In primo luogo è consigliabile provare a eseguire l'accesso con un metodo diverso per cui non sia necessario il telefono.

- **Perdita del telefono o presenza di un nuovo numero di telefono.** Se si perde il telefono o se è stato ricevuto un nuovo numero, è possibile eseguire l'accesso usando un metodo diverso o chiedere all'amministratore di cancellare le impostazioni. È consigliabile far sapere all'amministratore se il telefono è stato smarrito o rubato, in modo che all'account possano essere apportati gli aggiornamenti appropriati. Dopo che le impostazioni sono state cancellate, all'accesso successivo viene chiesto di [eseguire la registrazione per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

- **Mancata ricezione del messaggio di testo o della chiamata telefonica di autenticazione.** I motivi per cui si potrebbe non ricevere il messaggio di testo o una chiamata telefonica sono diversi. Se in precedenza i messaggi di testo o le chiamate telefoniche sono stati ricevuti in modo corretto, è probabile che il problema sia dovuto al provider e non all'account. Se si verificano spesso ritardi a causa della scarsa qualità del segnale, è consigliabile usare l'[app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) sul dispositivo mobile. Questa app può generare codici di sicurezza casuali per l'accesso, senza richiedere alcun segnale di rete né alcuna connessione Internet.<br><br>Se si tenta di ricevere un messaggio di testo, chiedere a un amico di inviare un messaggio per verificare che sia possibile riceverne uno e se sono stati ricevuti più messaggi, verificare di usare il codice relativo a quello più attuale.

- **Mancato funzionamento delle password dell'app.** Le password dell'app sostituiscono la normale password per le applicazioni desktop precedenti che non supportano la verifica in due passaggi. Verificare prima che la password sia stata digitata correttamente. Se il problema non viene risolto, provare a eseguire l'accesso per [creare una nuova password dell'app](multi-factor-authentication-end-user-app-passwords.md) o contattare l'amministratore per [eliminare le password dell'app esistenti](../authentication/howto-mfa-userdevicesettings.md) in modo che sia possibile crearne una nuova.

## <a name="sign-in-using-another-verification-method"></a>Accedere con un altro metodo di verifica

1. Eseguire l'accesso al proprio account in modo normale e scegliere il collegamento **Sign in another way** (Accedi in altro modo) nella pagina **Verifica in due passaggi**.

    ![Modificare il metodo di verifica dell'accesso](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se il collegamento **Sign in another way** (Accedi in altro modo) non viene visualizzato, non è stato impostato alcun altro metodo di verifica. È necessario contattare l'amministratore per ottenere assistenza sull'accesso all'account. Dopo aver eseguito l'accesso, assicurarsi di aggiungere altri metodi di verifica. Per altre informazioni sui metodi di verifica, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).<br><br>Se il collegamento è visualizzato, ma non viene visualizzato alcun altro metodo di verifica, contattare l'amministratore per ottenere assistenza sull'accesso all'account.

2. Scegliere il metodo di verifica alternativo e continuare il processo di verifica in due passaggi.

3. Dopo aver eseguito l'accesso al proprio account, è possibile aggiornare i metodi di verifica (se necessario). Per altre informazioni su come aggiungere o modificare i propri metodi, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato

Se i problemi persistono anche dopo l'esecuzione delle operazioni descritte, contattare l'amministratore per ottenere assistenza.

## <a name="related-topics"></a>Argomenti correlati

* [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

* [Domande frequenti sull'applicazione Microsoft Authenticator](microsoft-authenticator-app-faq.md)
