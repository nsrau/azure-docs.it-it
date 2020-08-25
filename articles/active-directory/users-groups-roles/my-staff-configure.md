---
title: Usare il personale per delegare la gestione degli utenti (anteprima)-Azure AD | Microsoft Docs
description: Delegare la gestione degli utenti tramite il personale e le unità amministrative
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: da358ecb126dda21d18120bbe698fec234c85e4f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798959"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gestisci gli utenti con il personale (anteprima)

Il personale IT consente di delegare a una figura di autorità, ad esempio un responsabile del negozio o un responsabile del team, le autorizzazioni per garantire che i membri del personale siano in grado di accedere ai propri account Azure AD. Anziché affidarsi a un helpdesk centrale, le organizzazioni possono delegare attività comuni, come la reimpostazione delle password o la modifica dei numeri di telefono a un Team Manager. Con il personale, un utente che non può accedere al proprio account può riottenere l'accesso con pochi clic, senza richiedere supporto tecnico o personale IT.

Prima di configurare il personale per la propria organizzazione, è consigliabile consultare questa documentazione e la [documentazione dell'utente](../user-help/my-staff-team-manager.md) per assicurarsi di comprendere la funzionalità e l'effetto di questa funzionalità per gli utenti. È possibile sfruttare la documentazione dell'utente per eseguire il training e preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione.

L'autenticazione basata su SMS per gli utenti è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="how-my-staff-works"></a>Funzionamento del personale

Il personale si basa su unità amministrative (AUs), ovvero un contenitore di risorse che possono essere usate per limitare l'ambito del controllo amministrativo di un'assegnazione di ruolo. Nel personale, gli AUs vengono usati per definire un subset di utenti di un'organizzazione, ad esempio uno Store o un reparto. Quindi, ad esempio, un Team Manager può essere assegnato a un ruolo il cui ambito è uno o più AUs. Nell'esempio seguente all'utente è stato concesso il ruolo di amministratore dell'autenticazione e le tre unità di gestione sono l'ambito del ruolo. Per ulteriori informazioni sulle unità amministrative, vedere [gestione delle unità amministrative in Azure Active Directory](directory-administrative-units.md).

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.

  * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione.

  * Se necessario, [creare un tenant di Azure Active Directory](../fundamentals/sign-up-organization.md) o [associare una sottoscrizione di Azure al proprio account](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Per abilitare l'autenticazione basata su SMS, è necessario disporre dei privilegi di *amministratore globale* nel tenant di Azure ad.
* A ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concessa una licenza, anche a chi non usa questo metodo. Ogni utente abilitato deve avere una delle seguenti Azure AD o licenze Microsoft 365:

  * [Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 o F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 o E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) o [Microsoft 365 (M365) E3 o E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Come abilitare il personale

Una volta configurata la funzionalità AUs, è possibile applicare questo ambito agli utenti che accedono al personale. Solo gli utenti a cui è assegnato un ruolo amministrativo possono accedere al personale. Per abilitare il personale, attenersi alla procedura seguente:

1. Accedere al portale di Azure come amministratore utente.
2. Passare a **Azure Active Directory**  >  **impostazioni utente le**  >  **anteprime**delle funzionalità utente  >  **Gestisci le impostazioni di anteprima delle funzionalità utente**.
3. In gli **amministratori possono accedere al personale**, è possibile scegliere di abilitare per tutti gli utenti, gli utenti selezionati o nessun accesso utente.

> [!Note]
> Solo gli utenti a cui è stato assegnato un ruolo di amministratore possono accedere al personale. Se si Abilita il personale per un utente a cui non è assegnato un ruolo di amministratore, questi non saranno in grado di accedere al personale.

## <a name="conditional-access"></a>Accesso condizionale

È possibile proteggere il portale personale usando Azure AD i criteri di accesso condizionale. Usare questa funzionalità per attività come la richiesta di autenticazione a più fattori prima di accedere al personale.

Si consiglia vivamente di proteggere il personale utilizzando [Azure ad criteri di accesso condizionale](../conditional-access/index.yml). Per applicare un criterio di accesso condizionale al personale, è necessario creare manualmente l'entità servizio personale con PowerShell.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>Applicare un criterio di accesso condizionale al personale

1. Installare i [cmdlet di PowerShell per Microsoft Graph beta](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Eseguire i comandi seguenti:

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. Creare criteri di accesso condizionale che si applicano all'applicazione cloud personale.

    ![Creare un criterio di accesso condizionale per l'app personale](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Uso del personale

Quando un utente passa al personale, vengono visualizzati i nomi delle [unità amministrative](directory-administrative-units.md) su cui dispongono di autorizzazioni amministrative. Nella [documentazione dell'utente personale](../user-help/my-staff-team-manager.md)viene usato il termine "location" per fare riferimento a unità amministrative. Se le autorizzazioni di un amministratore non hanno un ambito AU, le autorizzazioni si applicano all'intera organizzazione. Una volta abilitato il personale, gli utenti che sono abilitati e a cui è stato assegnato un ruolo amministrativo possono accedervi tramite [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Possono selezionare un AU per visualizzare gli utenti in tale AU e selezionare un utente per aprire il proprio profilo.

## <a name="reset-a-users-password"></a>Reimpostare la password di un utente

I ruoli seguenti sono autorizzati a reimpostare la password di un utente:

- [Amministratore autenticazione](directory-assign-admin-roles.md#authentication-administrator)
- [Amministratore autenticazione con privilegi](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Amministratore globale](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Amministratore dell'help desk](directory-assign-admin-roles.md#helpdesk-administrator)
- [Amministratore utenti](directory-assign-admin-roles.md#user-administrator)
- [Amministratore password](directory-assign-admin-roles.md#password-administrator)

Dal **personale**, aprire il profilo di un utente. Selezionare **Reimposta password**.

- Se l'utente è solo cloud, è possibile visualizzare una password temporanea che è possibile assegnare all'utente.
- Se l'utente viene sincronizzato dall'Active Directory locale, è possibile immettere una password che soddisfi i criteri AD locali. È quindi possibile assegnare la password all'utente.

    ![Indicatore di stato della reimpostazione della password e notifica](media/my-staff-configure/reset-password.png)

L'utente dovrà modificare la password al successivo accesso.

## <a name="manage-a-phone-number"></a>Gestisci un numero di telefono

Dal **personale**, aprire il profilo di un utente.

- Selezionare la sezione **Aggiungi numero di telefono** per aggiungere un numero di telefono per l'utente
- Selezionare **modifica numero di telefono** per modificare il numero di telefono
- Selezionare **Rimuovi numero di telefono** per rimuovere il numero di telefono dell'utente

A seconda delle impostazioni, l'utente può quindi usare il numero di telefono configurato per accedere con SMS, eseguire l'autenticazione a più fattori ed eseguire la reimpostazione della password self-service.

Per gestire il numero di telefono di un utente, è necessario disporre di uno dei ruoli seguenti:

- [Amministratore autenticazione](directory-assign-admin-roles.md#authentication-administrator)
- [Amministratore autenticazione con privilegi](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Amministratore globale](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Cerca

È possibile cercare gli AUs e gli utenti dell'organizzazione usando la barra di ricerca del personale. È possibile eseguire ricerche in tutte le unità di analisi e tutti gli utenti dell'organizzazione, ma è possibile apportare modifiche solo agli utenti che si trovano in un'unità di analisi per cui sono state concesse le autorizzazioni di amministratore.

È anche possibile cercare un utente all'interno di un AU. A tale scopo, usare la barra di ricerca nella parte superiore dell'elenco di utenti.

## <a name="audit-logs"></a>Log di controllo

È possibile visualizzare i log di controllo per le azioni eseguite nel personale nel portale di Azure Active Directory. Se un log di controllo è stato generato da un'azione eseguita nel personale, questo verrà visualizzato in dettagli aggiuntivi nell'evento di controllo.

## <a name="next-steps"></a>Passaggi successivi

Documentazione per gli [utenti del personale](../user-help/my-staff-team-manager.md) 
 [Documentazione sulle unità amministrative](directory-administrative-units.md)