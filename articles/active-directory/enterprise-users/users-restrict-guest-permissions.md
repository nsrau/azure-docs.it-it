---
title: Limitare le autorizzazioni di accesso utente Guest-Azure Active Directory | Microsoft Docs
description: Limitare le autorizzazioni di accesso degli utenti guest usando il portale di Azure, PowerShell o Microsoft Graph in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/17/2020
ms.topic: how-to
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51dc4ff61069bffadc4f95caee9e0f3c76d88ec
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488471"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Limitare le autorizzazioni di accesso Guest (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) consente di limitare le informazioni che gli utenti Guest esterni possono visualizzare nella propria organizzazione in Azure AD. Per impostazione predefinita, gli utenti Guest sono impostati su un livello di autorizzazione limitato in Azure AD, mentre l'impostazione predefinita per gli utenti membro è il set completo di autorizzazioni utente predefinite. Si tratta di un'anteprima di un nuovo livello di autorizzazione utente guest nelle impostazioni di collaborazione esterna dell'organizzazione Azure AD per un accesso ancora più limitato, quindi le scelte di accesso ai guest ora sono:

Livello di autorizzazione         | Livello di accesso
----------------         | ------------
Uguale agli utenti membro     | I Guest hanno lo stesso accesso alle risorse Azure AD come utenti membro
Accesso limitato (impostazione predefinita) | I guest possono visualizzare l'appartenenza di tutti i gruppi non nascosti
**Accesso limitato (nuovo)**  | **I guest non possono visualizzare l'appartenenza di gruppi**

Quando l'accesso guest viene limitato, i guest possono visualizzare solo il proprio profilo utente. L'autorizzazione per la visualizzazione di altri utenti non è consentita anche se il Guest esegue la ricerca in base al nome dell'entità utente o all'objectId L'accesso limitato limita anche gli utenti Guest a visualizzare l'appartenenza dei gruppi in cui si trovano. Per ulteriori informazioni sulle autorizzazioni utente predefinite complessive, incluse le autorizzazioni utente Guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

Per configurare le impostazioni di collaborazione esterna, è necessario avere il ruolo di amministratore globale. Non sono previsti requisiti di licenza aggiuntivi per limitare l'accesso guest.

## <a name="update-in-the-azure-portal"></a>Aggiornamento nella portale di Azure

Sono state apportate modifiche ai controlli portale di Azure esistenti per le autorizzazioni utente Guest.

1. Accedere al centro di [amministrazione Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore globale.
1. Nella pagina Panoramica **Azure Active Directory** dell'organizzazione selezionare **impostazioni utente**.
1. In **utenti esterni** selezionare **Gestisci impostazioni di collaborazione esterna**.
1. Nella pagina **impostazioni di collaborazione esterna** selezionare **accesso utente Guest è limitato alle proprietà e alle appartenenze dell'opzione oggetti directory** .

    ![Pagina impostazioni di collaborazione esterna Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Selezionare **Salva**. Per rendere effettive le modifiche per gli utenti guest possono essere necessari fino a 15 minuti.

## <a name="update-with-the-microsoft-graph-api"></a>Aggiornare con l'API Microsoft Graph

È stata aggiunta una nuova API Microsoft Graph per configurare le autorizzazioni guest nell'organizzazione Azure AD. È possibile effettuare le chiamate API seguenti per assegnare qualsiasi livello di autorizzazione. Il valore di guestUserRoleId usato in questo articolo è illustrare l'impostazione utente guest più limitata. Per ulteriori informazioni sull'utilizzo del Microsoft Graph per impostare le autorizzazioni Guest, vedere [tipo di risorsa authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Configurazione per la prima volta

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La risposta dovrebbe essere Success 204.

### <a name="updating-the-existing-value"></a>Aggiornamento del valore esistente

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La risposta dovrebbe essere Success 204.

### <a name="view-the-current-value"></a>Visualizza il valore corrente

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Esempio di risposta:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Aggiornare con i cmdlet di PowerShell

Con questa funzionalità è stata aggiunta la possibilità di configurare le autorizzazioni limitate tramite i cmdlet di PowerShell v2. I cmdlet Get e set di PowerShell sono stati pubblicati nella versione 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Comando Get: Get-AzureADMSAuthorizationPolicy

Esempio:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Comando set: Set-AzureADMSAuthorizationPolicy

Esempio:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Quando richiesto, è necessario immettere authorizationPolicy come ID.

## <a name="supported-microsoft-365-services"></a>Servizi di Microsoft 365 supportati

### <a name="supported-services"></a>Servizi supportati

Supportato, significa che l'esperienza è come previsto. in particolare, è uguale all'esperienza Guest corrente.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>Servizi attualmente non supportati

Il servizio senza supporto tecnico corrente potrebbe avere problemi di compatibilità con la nuova impostazione di restrizione Guest.

- Moduli
- Pianificazione nei team
- App Planner
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

Domanda | Risposta
-------- | ------
Dove si applicano queste autorizzazioni? | Queste autorizzazioni a livello di directory vengono applicate tra Azure AD servizi e i portali, tra cui il Microsoft Graph, PowerShell V2, il portale di Azure e il portale app personali. Sono interessati anche i servizi Microsoft 365 che sfruttano i gruppi di Microsoft 365 per gli scenari di collaborazione, in particolare Outlook, Microsoft teams e SharePoint.
In che modo le autorizzazioni limitate influiscono sui gruppi che possono essere visualizzati dagli utenti Guest? | Indipendentemente dalle autorizzazioni Guest predefinite o limitate, i guest non possono enumerare l'elenco di gruppi o utenti. I guest possono visualizzare i gruppi di cui sono membri sia nel portale di Azure che nel portale app personali, a seconda delle autorizzazioni:<li>**Autorizzazioni predefinite**: per trovare i gruppi di cui sono membri nel portale di Azure, il guest deve cercare l'ID oggetto nell'elenco **tutti gli utenti** e quindi selezionare **gruppi**. Qui è possibile visualizzare l'elenco dei gruppi di cui sono membri, inclusi tutti i dettagli del gruppo, inclusi il nome, la posta elettronica e così via. Nel portale app personali è possibile visualizzare un elenco di gruppi di cui sono proprietari e i gruppi di cui sono membri.</li><li>**Autorizzazioni Guest limitate**: nella portale di Azure possono comunque trovare l'elenco dei gruppi di cui sono membri cercando l'ID oggetto nell'elenco tutti gli utenti e quindi selezionare gruppi. Possono visualizzare solo dettagli molto limitati sul gruppo, in particolare l'ID oggetto. Per impostazione predefinita, le colonne nome e indirizzo di posta elettronica sono vuote e il tipo di gruppo non è riconosciuto. Nel portale app personali non è possibile accedere all'elenco dei gruppi di cui sono proprietari o ai gruppi di cui è membro.</li><br>Per un confronto più dettagliato delle autorizzazioni della directory che provengono dalla API Graph, vedere [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md#member-and-guest-users).
Quali parti del portale delle app personali avranno effetto su questa funzionalità? | Le funzionalità dei gruppi nel portale app personali rispetteranno queste nuove autorizzazioni. Sono inclusi tutti i percorsi per visualizzare l'elenco dei gruppi e le appartenenze ai gruppi nelle app personali. Non sono state apportate modifiche alla disponibilità dei riquadri del gruppo. La disponibilità dei riquadri di gruppo è ancora controllata dall'impostazione del gruppo esistente nell'portale di Azure.
Queste autorizzazioni eseguono l'override delle impostazioni guest di SharePoint o Microsoft Teams? | No. Le impostazioni esistenti controllano ancora l'esperienza e l'accesso a tali applicazioni. Se, ad esempio, si riscontrano problemi in SharePoint, verificare le impostazioni di condivisione esterna.
Quali sono i problemi di compatibilità noti in Planner e Yammer? | <li>Con le autorizzazioni impostate su' restricted ', i guest registrati nell'app Planner o l'accesso a Planner in Microsoft teams non saranno in grado di accedere ai piani o alle attività.<li>Con le autorizzazioni impostate su' restricted ', i guest registrati in Yammer non saranno in grado di uscire dal gruppo.
Le autorizzazioni Guest esistenti verranno modificate nel tenant? | Non sono state apportate modifiche alle impostazioni correnti. Manteniamo la compatibilità con le impostazioni esistenti. Decidere quando si desidera apportare modifiche.
Queste autorizzazioni verranno impostate per impostazione predefinita? | No. Le autorizzazioni predefinite esistenti rimangono invariate. Facoltativamente, è possibile impostare le autorizzazioni in modo che siano più restrittive.
Sono previsti requisiti di licenza per questa funzionalità? | No, non sono previsti nuovi requisiti di licenza per questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle autorizzazioni Guest esistenti in Azure AD, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Per visualizzare i metodi API Microsoft Graph per limitare l'accesso guest, vedere [tipo di risorsa authorizationPolicy](/graph/api/resources/authorizationpolicy)
- Per revocare tutti gli accessi per un utente, vedere [revocare l'accesso utente in Azure ad](users-revoke-access.md)