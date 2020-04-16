---
title: Usare il personale personale personale per delegare la gestione degli utenti (anteprima) - Azure AD Documenti Microsoft
description: Delegare la gestione degli utenti utilizzando Personale personale e le unità amministrative
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394215"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gestire gli utenti con Il mio personale (anteprima)

Il personale consente di delegare a una figura di autorità, ad esempio un responsabile del punto vendita o un responsabile del team, le autorizzazioni per garantire che i membri del personale siano in grado di accedere ai propri account Azure AD. Anziché basarsi su un helpdesk centrale, le organizzazioni possono delegare attività comuni, ad esempio la reimpostazione delle password o la modifica dei numeri di telefono in un team manager. Con Il personale personale personale, un utente che non può accedere al proprio account può riottenere l'accesso in un paio di clic, senza richiedere alcun helpdesk o personale IT.

Prima di configurare Il personale per l'organizzazione, è consigliabile consultare questa documentazione e la [documentazione dell'utente](../user-help/my-staff-team-manager.md) per assicurarsi di comprendere le funzionalità e l'impatto di questa funzionalità sugli utenti. È possibile utilizzare la documentazione utente per formare e preparare gli utenti per la nuova esperienza e garantire un'implementazione di successo.

## <a name="how-my-staff-works"></a>Come funziona il mio staff

Il personale è basato su unità amministrative (AUs), che sono un contenitore di risorse che possono essere utilizzate per limitare l'ambito del controllo amministrativo di un'assegnazione di ruolo. In Personale personale, le unità di analisi vengono utilizzate per definire un sottoinsieme degli utenti di un'organizzazione, ad esempio un punto vendita o un reparto. Quindi, ad esempio, un manager del team può essere assegnato a un ruolo il cui ambito è una o più di un'unica volta. Nell'esempio seguente, all'utente è stato concesso il ruolo amministrativo di autenticazione e le tre unità di controllo dell'utente sono l'ambito del ruolo. Per altre informazioni sulle unità amministrative, vedere Gestione delle [unità amministrative in Azure Active Directory.For](directory-administrative-units.md)more information about administrative units, see Administrative units management in Azure Active Directory .

## <a name="how-to-enable-my-staff"></a>Come abilitare il mio staff

Dopo aver configurato le aree di analisi, è possibile applicare questo ambito agli utenti che accedono a Personale personale personale personale. Solo gli utenti a cui è assegnato un ruolo amministrativo possono accedere a Personale personale personale. La procedura seguente illustra come abilitare Il mio staff:

1. Accedere al portale di Azure come amministratore utente.
2. Passare alle**impostazioni** > utente di **Azure Active Directory** > **Anteprime** > delle funzionalità**utente Gestisci impostazioni di anteprima funzionalità utente**.
3. In **Gli amministratori possono accedere a Personale**personale , è possibile scegliere di abilitare per tutti gli utenti, gli utenti selezionati o nessun accesso utente.

> [!Note]
> Solo gli utenti a cui è stato assegnato un ruolo di amministratore possono accedere a Personale personale personale. Se si abilita Personale personale per un utente a cui non è assegnato un ruolo di amministratore, non potrà accedere a Personale personale.

## <a name="using-my-staff"></a>Utilizzo del personale personale

Quando un utente passa a Personale personale, gli vengono visualizzati i nomi delle [unità amministrative](directory-administrative-units.md) per le quali dispone di autorizzazioni amministrative. Nella [documentazione utente My Staff](../user-help/my-staff-team-manager.md), utilizziamo il termine "posizione" per fare riferimento alle unità amministrative. Se le autorizzazioni di un amministratore non dispongono di un ambito AU, le autorizzazioni vengono applicate all'interno dell'organizzazione. Dopo l'abilitazione del personale, gli utenti abilitati e a cui [https://mystaff.microsoft.com](https://mystaff.microsoft.com)è stato assegnato un ruolo amministrativo possono accedervi tramite . Possono selezionare un'Unione africana per visualizzare gli utenti in tale AU e selezionare un utente per aprire il proprio profilo.

## <a name="licenses"></a>Licenze

Ogni utente abilitato in Personale personale personale deve essere concesso in licenza, anche se non utilizza il portale Personale personale. Ogni utente abilitato deve disporre di una delle seguenti licenze di Azure AD o Microsoft 365:

- Azure AD Premium P1 o P2
- Microsoft 365 F1 o F3

## <a name="reset-a-users-password"></a>Reimpostare la password di un utente

I ruoli seguenti dispongono dell'autorizzazione per reimpostare la password di un utente:

- [Amministratore dell'autenticazione](directory-assign-admin-roles.md#authentication-administrator)
- [Amministratore dell'autenticazione con privilegi](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Amministratore globale](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Amministratore dell'help desk](directory-assign-admin-roles.md#helpdesk-administrator)
- [Amministratore utenti](directory-assign-admin-roles.md#user-administrator)
- [Amministratore password](directory-assign-admin-roles.md#password-administrator)

Da **Personale personale**, aprire il profilo di un utente. Selezionare **Reimposta password**.

- Se l'utente è solo cloud, è possibile visualizzare una password temporanea che è possibile fornire all'utente.
- Se l'utente viene sincronizzato da Active Directory locale, è possibile immettere una password che soddisfi i criteri di Active Directory locali. È quindi possibile assegnare tale password all'utente.

    ![Indicatore di stato di reimpostazione della password e notifica di esito positivo](media/my-staff-configure/reset-password.png)

L'utente deve modificare la password al successivo accesso.

## <a name="manage-a-phone-number"></a>Gestire un numero di telefono

Da **Personale personale**, aprire il profilo di un utente.

- Selezionare la sezione **Aggiungi numero** di telefono per aggiungere un numero di telefono per l'utente
- Selezionare **Modifica numero** di telefono per modificare il numero di telefono
- Selezionare **Rimuovi numero** di telefono per rimuovere il numero di telefono dell'utente

A seconda delle impostazioni, l'utente può quindi utilizzare il numero di telefono configurato per accedere con SMS, eseguire l'autenticazione a più fattori ed eseguire la reimpostazione della password self-service.

Per gestire il numero di telefono di un utente, è necessario disporre di uno dei seguenti ruoli:

- [Amministratore dell'autenticazione](directory-assign-admin-roles.md#authentication-administrator)
- [Amministratore dell'autenticazione con privilegi](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Amministratore globale](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Ricerca

È possibile cercare unità di analisi e utenti dell'organizzazione utilizzando la barra di ricerca in Personale personale personale personale. È possibile eseguire ricerche in tutte le unità di analisi e gli utenti dell'organizzazione, ma è possibile apportare modifiche solo agli utenti che si trovano in un'Unione africana per le quali sono state concesse le autorizzazioni di amministratore.

È inoltre possibile cercare un utente all'interno di un'Unione africana. A tale scopo, utilizzare la barra di ricerca nella parte superiore dell'elenco utenti.

## <a name="audit-logs"></a>Log di controllo

È possibile visualizzare i log di controllo per le azioni eseguite in Personale personale personale nel portale di Azure Active Directory.You can view audit logs for actions taken in My Staff in the Azure Active Directory portal. Se un log di controllo è stato generato da un'azione eseguita in Personale personale personale personale personale, verrà visualizzato in DETTAGLI ADDITIONAL nell'evento di controllo.

## <a name="next-steps"></a>Passaggi successivi

[Documentazione utente My Staff Documentazione](../user-help/my-staff-team-manager.md)
[sulle unità amministrative](directory-administrative-units.md)
