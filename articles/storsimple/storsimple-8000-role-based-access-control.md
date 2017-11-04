---
title: Usare il controllo degli accessi in base al ruolo per StorSimple | Microsoft Docs
description: Descrive come usare il controllo degli accessi in base al ruolo di Azure nel contesto di StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: d040849360a47c611d44b3a5d7649c685dcc8068
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="role-based-access-control-for-storsimple"></a>Controllo degli accessi in base al ruolo per StorSimple

Questo articolo fornisce una breve descrizione di come è possibile usare il controllo degli accessi in base al ruolo di Azure per un dispositivo StorSimple. Il controllo degli accessi in base al ruolo consente la gestione degli accessi con granularità fine per Azure. Il controllo degli accessi in base al ruolo consente di concedere agli utenti di StorSimple il livello di accesso appropriato in base al tipo di attività che devono svolgere, anziché concedere loro un accesso illimitato. Per le nozioni di base sulla gestione degli accessi in Azure, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).

Questo articolo si applica ai dispositivi StorSimple serie 8000 con Update 3.0 o successivo in esecuzione nel portale di Azure.

## <a name="rbac-roles-for-storsimple"></a>Ruoli di controllo degli accessi in base al ruolo per StorSimple

Il controllo degli accessi in base al ruolo può essere assegnato in funzione dei ruoli, che assicurano determinati livelli di autorizzazione in base alle risorse disponibili nell'ambiente. Esistono due tipi di ruoli tra cui gli utenti di StorSimple possono scegliere: predefiniti o personalizzati.

* **Ruoli predefiniti**: possono essere i ruoli di proprietario, collaboratore, lettore o amministratore Accesso utenti. Per altre informazioni, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md#built-in-roles).

* **Ruoli personalizzati**: se i ruoli predefiniti non soddisfano le esigenze correnti, è possibile creare ruoli di controllo degli accessi in base al ruolo personalizzati per StorSimple. Per creare un ruolo di controllo degli accessi in base al ruolo personalizzato, usare un ruolo predefinito, modificarlo e quindi importarlo di nuovo nell'ambiente. Il download e l'upload del ruolo vengono gestiti tramite Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-custom-roles.md).

Per visualizzare i diversi ruoli disponibili per un utente di un dispositivo StorSimple nel portale di Azure, passare al servizio Gestione dispositivi StorSimple e quindi selezionare **Controllo di accesso (IAM) > Ruoli**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Creare un ruolo personalizzato per Amministratore dell'infrastruttura StorSimple

Nell'esempio seguente si inizia con il ruolo predefinito **Lettore**, che consente agli utenti di visualizzare tutti gli ambiti di risorsa, ma non di modificarli o di crearne nuovi. Si estenderà quindi il ruolo per creare il nuovo ruolo personalizzato Amministratore dell'infrastruttura StorSimple. Questo ruolo viene assegnato agli utenti autorizzati a gestire l'infrastruttura per i dispositivi StorSimple.

1. Eseguire Windows PowerShell come amministratore.

2. Accedere ad Azure.

    `Login-AzureRMAccount`

3. Esportare il ruolo Lettore nel computer come modello JSON.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Aprire il file JSON in Visual Studio. In genere, un ruolo di controllo degli accessi in base al ruolo è composto da tre sezioni principali: **Actions**, **NotActions** e **AssignableScopes**.

    Nella sezione **Action** vengono elencate tutte le operazioni consentite per questo ruolo. Ogni azione viene assegnata da un provider di risorse. Per il ruolo Amministratore dell'infrastruttura StorSimple, usare il provider di risorse `Microsoft.StorSimple`.

    Per visualizzare tutti i provider di risorse disponibili e registrati nella sottoscrizione, è possibile usare PowerShell.

    `Get-AzureRMResourceProvider`

    È possibile anche cercare tutti i cmdlet di PowerShell disponibili per gestire i provider di risorse.

    Nelle sezioni **NotActions** vengono elencate tutte le azioni vietate a un determinato ruolo di controllo degli accessi in base al ruolo. In questo esempio non è vietata alcuna azione.
    
    In **AssignableScopes** vengono elencati gli ID di sottoscrizione. Accertarsi che il ruolo di controllo degli accessi in base al ruolo contenga gli ID di sottoscrizione espliciti in cui viene usato. Se non viene specificato l'ID di sottoscrizione corretto, non è possibile importare il ruolo nella sottoscrizione.

    Modificare il file tenendo presenti le considerazioni precedenti.

    ```
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Reimportare nell'ambiente il ruolo di controllo degli accessi in base al ruolo personalizzato.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Il ruolo dovrebbe essere ora visualizzato nell'elenco dei ruoli nel pannello **Controllo di accesso**.

![Visualizzare i ruoli di controllo degli accessi in base al ruolo](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Per altre informazioni, vedere [Creare un ruolo personalizzato di controllo degli accessi in base al ruolo usando PowerShell](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md#create-a-custom-rbac-role-to-open-support-requests-using-powershell).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Esempio di output per la creazione di un ruolo personalizzato mediante PowerShell

```
PS C:\WINDOWS\system32> Login-AzureRMAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Aggiungere utenti al ruolo personalizzato

Si concede l'accesso dalla risorsa, dal gruppo di risorse o dalla sottoscrizione che costituisce l'ambito dell'assegnazione di ruolo. Quando si concede l'accesso, tenere presente che l'accesso concesso al nodo padre viene ereditato dal figlio. Per altre informazioni, visitare [Gerarchia delle risorse ed ereditarietà dell'accesso](../active-directory/role-based-access-control-what-is.md#resource-hierarchy-and-access-inheritance).

1. Selezionare **Controllo di accesso (IAM)**. Fare clic su **+Aggiungi** nel pannello Controllo di accesso.

    ![Aggiungere l'accesso al ruolo di controllo degli accessi in base al ruolo](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selezionare il ruolo che si vuole assegnare, in questo caso **Amministratore dell'infrastruttura StorSimple**.

3. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.

4. Scegliere **Salva** per creare l'assegnazione.

    ![Aggiungere autorizzazioni al ruolo di controllo degli accessi in base al ruolo](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

La notifica **Aggiunta dell'utente in corso** tiene traccia dello stato di avanzamento del processo di aggiunta. Al termine del processo, l'elenco degli utenti nel pannello Controllo degli accessi viene aggiornato.

## <a name="view-permissions-for-the-custom-role"></a>Visualizzare le autorizzazioni per il ruolo personalizzato

Dopo aver creato il ruolo, è possibile visualizzare le autorizzazioni associate al ruolo nel portale di Azure.

1. Per visualizzare le autorizzazioni associate al ruolo, passare a **Controllo di accesso (IAM) > Ruoli > Amministratore dell'infrastruttura StorSimple**. Viene visualizzato l'elenco degli utenti in questo ruolo.

2. Selezionare un utente Amministratore dell'infrastruttura StorSimple e fare clic su **Autorizzazioni**.

    ![Visualizzare le autorizzazioni per il ruolo Amministratore dell'infrastruttura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Vengono visualizzate le autorizzazioni associate al ruolo.

    ![Visualizzare gli utenti nel ruolo Amministratore dell'infrastruttura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [Assegnare ruoli personalizzati per utenti interni ed esterni](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md).

