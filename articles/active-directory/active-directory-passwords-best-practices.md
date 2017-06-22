---
title: 'Implementazione: Reimpostazione password self-service di Azure AD | Microsoft Docs'
description: Consigli per l&quot;implementazione della reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 6cc1589f2ebad9267334f70926be369b32c8e075
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="roll-out-password-reset-for-users"></a>Implementare la reimpostazione della password per gli utenti

La maggior parte dei clienti segue questa procedura per garantire un'implementazione semplice della reimpostazione password self-service.

1. [Abilitare la reimpostazione della password nella directory](active-directory-passwords-getting-started.md)
2. [Configurare le autorizzazioni AD locali per il writeback delle password](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Configurare il writeback delle password](active-directory-passwords-writeback.md#configuring-password-writeback) per riscrivere le password da Azure AD alla directory locale
4. [Assegnare e verificare le licenze necessarie](active-directory-passwords-licensing.md)
5. Per un'implementazione graduale nel tempo è possibile limitare la reimpostazione della password a un gruppo di utenti. A tale scopo impostare l'interruttore **Reimpostazione password self-service abilitata** da **Tutti** a **Un gruppo** e selezionare un gruppo di sicurezza da abilitare per la reimpostazione della password. I membri di questo gruppo devono tutti avere licenze assegnate ed è consigliabile abilitare le [licenze basate sui gruppi](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Popolare il set minimo di [dati di autenticazione](active-directory-passwords-data.md) in base ai criteri.
7. Illustrare agli utenti l'uso della reimpostazione password self-service inviando istruzioni per la registrazione e la reimpostazione.
    > [!NOTE]
    > Testare la reimpostazione password self-service con un utente e non con un amministratore, perché Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo di approfondimento](active-directory-passwords-how-it-works.md).

8. È possibile scegliere di imporre la registrazione in qualsiasi momento e richiedere agli utenti di riconfermare le informazioni di autenticazione dopo un certo periodo di tempo. Se non si vuole che gli utenti eseguano la registrazione, è possibile [distribuire la reimpostazione della password senza richiedere la registrazione dell'utente finale](active-directory-passwords-data.md).
9. Nel tempo, esaminare gli utenti che eseguono la registrazione e usano la funzionalità tramite i [report messi a disposizione da Azure AD](active-directory-passwords-reporting.md).

## <a name="email-based-rollout"></a>Implementazione basata sulla posta elettronica

Molti clienti ritengono che una campagna di posta elettronica con istruzioni semplici sia il modo più facile per invitare gli utenti a usare la reimpostazione password self-service. Sono stati creati tre semplici messaggi di posta elettronica che possono essere usati come modelli per agevolare l'implementazione.

* Modello di messaggio **Presto disponibile** da usare nelle settimane o nei giorni precedenti l'implementazione per informare gli utenti che sarà necessario eseguire alcune operazioni.
* Modello di messaggio **Ora disponibile** da usare il giorno del lancio per invitare gli utenti a eseguire la registrazione e verificare i dati di autenticazione per usare la reimpostazione password self-service quando necessario.
* Modello di messaggio **Promemoria di registrazione** da usare per alcuni giorni o settimane dopo l'implementazione per ricordare agli utenti di eseguire la registrazione e verificare i dati di autenticazione.

## <a name="creating-your-own-password-portal"></a>Creazione del portale delle password

Molti dei nostri clienti più grandi scelgono di ospitare una pagina Web e creare una voce DNS radice, ad esempio https://passwords.contoso.com. Popolano questa pagina con collegamenti per la reimpostazione della password di Azure AD, la registrazione per la reimpostazione della password e i portali di modifica della password, oltre ad altre informazioni specifiche dell'organizzazione. In qualsiasi comunicazione cartacea o di posta elettronica inviata è possibile includere un URL personalizzato facile da ricordare al quale gli utenti possono accedere quando devono usare i servizi.

* Portale di reimpostazione della password: https://passwordreset.microsoftonline.com/
* Portale di registrazione per la reimpostazione della password: http://aka.ms/ssprsetup
* Portale di modifica della password: https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>Uso della registrazione applicata

Se si vuole che gli utenti eseguano la registrazione per la reimpostazione della password, è possibile imporre la registrazione quando accedono usando Azure AD. È possibile abilitare questa opzione dal pannello **Reimpostazione password** della directory, selezionando l'opzione **Richiedere agli utenti di registrarsi all'accesso?** nella scheda **Registrazione**.

Gli amministratori possono richiedere agli utenti di eseguire nuovamente la registrazione dopo un periodo di tempo impostando **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su un valore compreso tra 0 e 730 giorni.

Dopo aver abilitato questa opzione, quando gli utenti accedono visualizzeranno un messaggio che li informa che l'amministratore ha richiesto loro di verificare le informazioni di autenticazione.

## <a name="populate-authentication-data"></a>Popolare i dati di autenticazione

Se si [popolano i dati di autenticazione per conto degli utenti](active-directory-passwords-data.md), questi non dovranno eseguire la registrazione per usare la reimpostazione password self-service. Se hanno dati di autenticazione che soddisfano i criteri di reimpostazione della password definiti, gli utenti potranno reimpostare le proprie password.

## <a name="disabling-self-service-password-reset"></a>Disabilitazione della reimpostazione self-service della password

Per disabilitare la reimpostazione self-service della password è sufficiente aprire il tenant di Azure AD e passare a **Reimpostazione password**, **Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - Iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md): configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Personalizzazione**](active-directory-passwords-customize.md): personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Criteri**](active-directory-passwords-policy.md): comprendere e impostare i criteri password di Azure AD
* [**Writeback delle password**](active-directory-passwords-writeback.md): funzionamento del writeback delle password con la directory locale
* [**Creazione di report**](active-directory-passwords-reporting.md) - verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service
