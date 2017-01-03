---
title: Azure AD Privileged Identity Management | Documentazione Microsoft
description: Argomento che descrive Azure AD Privileged Identity Management e illustra come usare PIM per migliorare la sicurezza del cloud.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 71eb6a2fecbd76c9715b9a556a7ffd31b5b4950e
ms.openlocfilehash: 6b788b3120565895e908bdfc4c9267e715bc5bec


---
# <a name="azure-ad-privileged-identity-management"></a>Gestione identità con privilegi di Azure AD
Con Azure Active Directory (AD) Privileged Identity Management è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. È incluso l'accesso alle risorse in Azure AD e altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.  

> [!NOTE]
> Privileged Identity Management è disponibile solo con Azure Active Directory Premium P2 Edition. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
>
>

Le organizzazioni desiderano ridurre al minimo il numero di utenti che hanno accesso a informazioni o risorse protette per ridurre le probabilità che un utente malintenzionato acceda al sistema. Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi nelle app Azure, Office 365 o SaaS. Le organizzazioni assegnano agli utenti l'accesso con privilegi in Azure AD senza monitorare le attività svolte con i privilegi amministrativi. Gestione identità con privilegi di Azure AD consente di risolvere questo rischio.  

Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:  

* Individuare gli utenti amministratori di Azure AD
* Abilitare l'accesso come amministratore Just-In-Time su richiesta ai Microsoft Online Services, ad esempio Office 365 e Intune
* Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori
* Ricevere avvisi relativi all'accesso a un ruolo con privilegi

Azure AD Privileged Identity Management è in grado di gestire i ruoli aziendali predefiniti di Azure Active Directory che includono:  

* Amministratore globale
* Amministratore fatturazione
* Amministratore del servizio  
* Amministratore utenti
* Amministratore password

## <a name="just-in-time-administrator-access"></a>Accesso amministratore just-in-time
In passato si poteva assegnare un utente a un ruolo amministrativo tramite il portale di Azure classico o Windows PowerShell. Di conseguenza l'utente diventa **amministratore permanente**, sempre attivo nel ruolo assegnato. Azure AD Privileged Identity Management introduce il concetto di **amministratore idoneo**. Gli amministratori idonei devono essere utenti che necessitano dell'accesso con privilegi in modo occasionale, non con cadenza quotidiana. Il ruolo è inattivo fino a quando l'utente che necessita dell'accesso non completa un processo di attivazione e diventa amministratore attivo per un periodo di tempo predeterminato.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Abilitare Privileged Identity Management per la directory
È possibile iniziare a usare Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/).

> [!NOTE]
> Per abilitare Azure AD Privileged Identity Management per una directory, è necessario essere un amministratore globale, ad esempio @yourdomain.com), e non un account Microsoft, ad esempio @outlook.com),.
>
>

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, selezionare il proprio nome utente nell'angolo superiore destro del portale di Azure. Selezionare la directory in cui si userà Azure AD Privileged Identity Management.
3. Selezionare **Altri servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

Se si è la prima persona che usa Azure AD Privileged Identity Management nella directory, la [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) illustrerà in dettaglio l'esperienza di assegnazione iniziale. Al termine della procedura guidata si diventa automaticamente primo **amministratore della sicurezza** e **amministratore dei ruoli con privilegi** della directory.

Solo l'amministratore dei ruoli con privilegi può gestire l'accesso per gli altri amministratori. È possibile [consentire ad altri utenti di eseguire operazioni di gestione in PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Dashboard di Gestione identità con privilegi
In Azure AD Privileged Identity Management è disponibile un dashboard che visualizza informazioni importanti, tra cui:

* Avvisi che segnalano opportunità di miglioramento della sicurezza
* Numero di utenti assegnati a ogni ruolo con privilegi  
* Numero di amministratori permanenti e idonei
* Verifiche di accesso in corso

![Dashboard PIM - Schermata][2]

## <a name="privileged-role-management"></a>Gestione dei ruoli con privilegi
Con Azure AD Privileged Identity Management è possibile gestire gli amministratori tramite l'aggiunta o la rimozione di amministratori permanenti o idonei a ogni ruolo.

![Aggiunta/Rimozione di amministratori PIM - Schermata][3]

## <a name="configure-the-role-activation-settings"></a>Configurare le impostazioni di attivazione del ruolo
L'uso delle [impostazioni del ruolo](active-directory-privileged-identity-management-how-to-change-default-settings.md) consente di configurare le proprietà di attivazione del ruolo idoneo, ad esempio:

* Durata del periodo di attivazione del ruolo
* Notifica di attivazione del ruolo
* Informazioni che l'utente deve specificare durante il processo di attivazione del ruolo  

![Impostazioni di PIM - Attivazione dell'amministratore - Schermata][4]

Nell'immagine notare che i pulsanti per **Multi-Factor Authentication** sono disabilitati. Per determinati ruoli con privilegi elevati, è necessaria l'autenticazione MFA per una protezione avanzata.

## <a name="role-activation"></a>Attivazione del ruolo
Per [attivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md), un amministratore idoneo richiede una "attivazione" con vincoli di tempo per il ruolo. È possibile richiedere l'attivazione usando l'opzione **Attiva il mio ruolo** in Gestione identità con privilegi di Azure AD.

Un amministratore che vuole attivare un ruolo deve inizializzare Azure AD Privileged Identity Management nel portale di Azure.

L'attivazione del ruolo è personalizzabile. Nelle impostazioni di PIM è possibile impostare la durata dell'attivazione e le informazioni che l'amministratore deve specificare per attivare il ruolo.

![Richiesta di attivazione del ruolo dell'amministratore PIM - Schermata][5]

## <a name="review-role-activity"></a>Verificare l'attività del ruolo
È possibile verificare l'uso dei ruoli con privilegi da parte di dipendenti e amministratori in due modi. È prima di tutto possibile usare la [cronologia di controllo](active-directory-privileged-identity-management-how-to-use-audit-log.md). La cronologia di controllo registra le modifiche rilevate nelle assegnazioni dei ruoli con privilegi e nella cronologia di attivazione dei ruoli.

![Cronologia di attivazione PIM - Schermata][6]

La seconda opzione consiste nel configurare [verifiche di accesso](active-directory-privileged-identity-management-how-to-start-security-review.md)regolari. Queste verifiche di accesso possono essere eseguite e assegnate da un revisore, ad esempio il responsabile del team, o dai dipendenti stessi. Questo è il modo migliore per monitorare gli utenti che necessitano ancora dell'accesso e gli utenti che non devono più accedere.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM alla scadenza della sottoscrizione
Prima di essere disponibile a livello generale, Azure AD PIM è stato disponibile in anteprima, senza controlli di licenza per un tenant per la visualizzazione dell'anteprima di Azure AD PIM.  Azure AD PIM è ora disponibile a livello generale ed è quindi necessario che una sottoscrizione della versione di valutazione o a pagamento sia presente nel tenant per continuare a usare PIM dopo il mese di dicembre 2016.  Se l'organizzazione non acquista Azure AD Premium P2 o in caso di scadenza della sottoscrizione, Azure AD PIM non sarà più disponibile nel tenant.  Per altre informazioni, vedere [Azure AD PIM subscription requirements](./privileged-identity-management/subscription-requirements.md) (Requisiti della sottoscrizione di Azure AD PIM)

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png



<!--HONumber=Jan17_HO1-->


