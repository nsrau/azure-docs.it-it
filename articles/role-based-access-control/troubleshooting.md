---
title: Risolvere i problemi relativi al controllo degli accessi in base
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo di Azure (Controllo degli accessi in base al ruolo di Azure).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 8aedc78772858815a18425fb1e6cb36a4600f647
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385096"
---
# <a name="troubleshoot-azure-rbac"></a>Risolvere i problemi relativi al controllo degli accessi in base

Questo articolo risponde ad alcune domande comuni sul controllo degli accessi in base al ruolo di Azure, in modo da sapere cosa aspettarsi quando si usano i ruoli e risolvere i problemi di accesso.

## <a name="azure-role-assignments-limit"></a>Limite assegnazioni di ruolo di AzureAzure role assignments limit

Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione. Se viene visualizzato il messaggio di errore "Non è possibile creare altre assegnazioni di ruolo (codice: RoleAssignmentLimitExceeded)" quando si tenta di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo nella sottoscrizione.

> [!NOTE]
> Il limite di assegnazioni di ruolo **2000** per sottoscrizione è fisso e non può essere aumentato.

Se ci si avvicina a questo limite, ecco alcuni modi in cui è possibile ridurre il numero di assegnazioni di ruolo:

- Aggiungere utenti ai gruppi e assegnare invece ruoli ai gruppi. 
- Combina più ruoli predefiniti con un ruolo personalizzato. 
- Effettuare assegnazioni di ruolo comuni in un ambito superiore, ad esempio sottoscrizione o gruppo di gestione.
- Se si dispone di Azure AD Premium P2, rendere idonee le assegnazioni di ruolo in [Gestione delle identità con privilegi](../active-directory/privileged-identity-management/pim-configure.md) di Azure AD anziché essere assegnate in modo permanente. 
- Aggiungere una sottoscrizione aggiuntiva. 

Per ottenere il numero di assegnazioni di ruolo, è possibile visualizzare il grafico nella pagina Controllo di accesso (IAM) nel portale di Azure.To get the number of role assignments, you can view the [chart on the Access control (IAM) page](role-assignments-list-portal.md#list-number-of-role-assignments) in the Azure portal. È anche possibile usare i comandi di Azure PowerShell seguenti:You can also use the following Azure PowerShell commands:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemi con le assegnazioni di ruolo di AzureProblems with Azure role assignments

- Se non è possibile aggiungere un'assegnazione di ruolo nel portale **Add** > di Azure nel controllo di accesso **(IAM)** perché l'opzione**Aggiungi assegnazione ruolo** è disabilitata o perché viene visualizzato l'errore `Microsoft.Authorization/roleAssignments/write` delle autorizzazioni "Il client con ID oggetto non dispone dell'autorizzazione per eseguire l'azione", verificare di aver attualmente eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell'autorizzazione, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore accesso utente](built-in-roles.md#user-access-administrator) nell'ambito che si sta tentando di assegnare il ruolo.

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Se sono necessari passaggi per creare un ruolo personalizzato, vedere esercitazioni sui ruoli personalizzati usando il portale di Azure (attualmente in anteprima), Azure PowerShell o [l'interfaccia della riga di comando](tutorial-custom-role-cli.md)di Azure.If you need steps for how to create a custom role, see the custom role tutorials using the Azure [portal](custom-roles-portal.md) (currently in preview), [Azure PowerShell,](tutorial-custom-role-powershell.md)or Azure CLI.
- Se non è possibile aggiornare un ruolo personalizzato esistente, verificare di aver eseguito `Microsoft.Authorization/roleDefinition/write` l'accesso con un utente a cui è assegnato un ruolo che dispone dell'autorizzazione, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore accesso utenti.](built-in-roles.md#user-access-administrator)
- Se non è possibile eliminare un ruolo personalizzato e viene visualizzato il messaggio di errore "Esistono assegnazioni di ruolo esistenti che fanno riferimento al ruolo (codice: RoleDefinitionHasAssignments)", sono presenti assegnazioni di ruolo che utilizzano ancora il ruolo personalizzato. Rimuovere le assegnazioni di ruolo e provare di nuovo a eliminare il ruolo personalizzato.
- Se viene visualizzato il messaggio di errore "Limite di definizioni del ruolo superato. Non è possibile creare altre definizioni di ruolo (codice: RoleDefinitionLimitExceeded)" quando si tenta di creare un nuovo ruolo personalizzato, eliminare tutti i ruoli personalizzati che non vengono utilizzati. Azure supporta fino a **5000** ruoli personalizzati in una directory. Per Azure Germania e Azure China 21Vianet, il limite è 2000 ruoli personalizzati.
- Se viene visualizzato un errore simile a "Il client dispone dell'autorizzazione per eseguire l'azione 'Microsoft.Authorization/roleDefinitions/write' nell'ambito '/subscriptions/', tuttavia la sottoscrizione collegata non è stata trovata" quando si tenta di aggiornare un ruolo personalizzato, verificare se uno o più [ambiti assegnabili](role-definitions.md#assignablescopes) sono stati eliminati nella directory. Se l'ambito è stato eliminato, creare un ticket di supporto perché attualmente non è disponibile alcuna soluzione self-service.

## <a name="custom-roles-and-management-groups"></a>Ruoli personalizzati e gruppi di gestione

- È possibile definire un `AssignableScopes` solo gruppo di gestione in un ruolo personalizzato. L'aggiunta di `AssignableScopes` un gruppo di gestione è attualmente in anteprima.
- I ruoli `DataActions` personalizzati con non possono essere assegnati nell'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione del ruolo.
- Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere Organizzare le risorse con i gruppi di gestione di Azure.For more information about custom roles and management groups, see [Organize your resources with Azure management groups.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Trasferimento di una sottoscrizione in una directory diversa

- Per informazioni su come trasferire una sottoscrizione in una directory di Azure AD diversa, vedere Trasferire la [proprietà di una sottoscrizione di Azure a un altro account.](../cost-management-billing/manage/billing-subscription-transfer.md)
- Se si trasferisce una sottoscrizione in una directory di Azure AD diversa, tutte le assegnazioni di ruolo vengono eliminate **definitivamente** dalla directory di Azure AD di origine e non vengono migrate nella directory di Azure AD di destinazione. È necessario ricreare le assegnazioni di ruolo nella directory di destinazione. È inoltre necessario ricreare manualmente le identità gestite per le risorse di Azure.You also have to manually recreate managed identities for Azure resources. Per ulteriori informazioni, vedere [Domande frequenti e problemi noti relativi alle identità gestite](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se si è un amministratore globale di Azure AD e non si ha accesso a una sottoscrizione dopo che è stata trasferita da una directory all'altro, usare l'opzione Di **attivazione/disattivazione** Gestione accessi per le risorse di Azure per [elevare](elevate-access-global-admin.md) temporaneamente l'accesso per ottenere l'accesso alla sottoscrizione.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemi con gli amministratori del servizio o i coamministratori

- In caso di problemi con l'amministratore del servizio o i coamministratori, vedere [Aggiungere o modificare gli amministratori](../cost-management-billing/manage/add-change-subscription-administrator.md) della sottoscrizione di Azure e Ruoli di amministratore della sottoscrizione classica, ruoli di Azure e ruoli di amministratore di Azure [AD.](rbac-and-directory-admin-roles.md)

## <a name="access-denied-or-permission-errors"></a>Accesso negato o errori di autorizzazione

- Se viene visualizzato l'errore di autorizzazioni "il client con ID oggetto non dispone dell'autorizzazione per eseguire l'azione sull'ambito (codice: autorizzazioneautorizzazione)" quando si tenta di creare una risorsa, verificare che sia stato attualmente eseguito l'accesso con un utente a cui è assegnato un ruolo con scrittura l'autorizzazione per la risorsa nell'ambito selezionato. Ad esempio, per gestire le macchine virtuali in un gruppo di risorse, è necessario disporre del ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) nel gruppo di risorse (o nell'ambito padre). Per un elenco delle autorizzazioni per ogni ruolo predefinito, vedere [Ruoli predefiniti per le risorse di Azure](built-in-roles.md).
- Se viene visualizzato l'errore di autorizzazioni "Non si dispone dell'autorizzazione per creare una richiesta di supporto" quando si tenta di creare `Microsoft.Support/supportTickets/write` o aggiornare un ticket di supporto, verificare di aver eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell'autorizzazione, ad esempio [Collaboratore richiesta](built-in-roles.md#support-request-contributor)di supporto .

## <a name="role-assignments-with-unknown-security-principal"></a>Assegnazioni di ruolo con entità di sicurezza sconosciutaRole assignments with Unknown security principal

Se si assegna un ruolo a un'entità di sicurezza (utente, gruppo, entità servizio o identità gestita) e successivamente si elimina tale entità di sicurezza senza rimuovere l'assegnazione di ruolo, il tipo di entità di sicurezza per l'assegnazione di ruolo verrà elencato come **Sconosciuto**. Lo screenshot seguente illustra un esempio nel portale di Azure. Il nome dell'entità di sicurezza è elencato come **Identità eliminata** e **Identità non esiste più.** 

![Gruppo di risorse per app Web](./media/troubleshooting/unknown-security-principal.png)

Se si elenca questa assegnazione di ruolo tramite Azure PowerShell, verrà visualizzata una vuota e un valore impostato su Sconosciuto.If you list this role assignment using Azure PowerShell, you will see an empty `DisplayName` and an `ObjectType` set to Unknown. Ad esempio, Get-AzRoleAssignment restituisce un'assegnazione di ruolo simile alla seguente:For example, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returns a role assignment that is similar to the following:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Analogamente, se si elenca questa assegnazione di `principalName`ruolo usando l'interfaccia della riga di comando di Azure, verrà visualizzato un file vuoto. Ad esempio, [az elenco di assegnazione di ruolo](/cli/azure/role/assignment#az-role-assignment-list) restituisce un'assegnazione di ruolo che è simile al seguente:For example, az role assignment list returns a role assignment that is similar to the following:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Non è un problema lasciare queste assegnazioni di ruolo, ma è possibile rimuoverle utilizzando passaggi simili ad altre assegnazioni di ruolo. Per informazioni su come rimuovere le assegnazioni di ruolo, vedere Portale di [Azure,](role-assignments-portal.md#remove-a-role-assignment)Azure PowerShell o interfaccia della riga di comando di [AzureFor](role-assignments-cli.md#remove-a-role-assignment) information about how to remove role assignments, see Azure portal , [Azure PowerShell,](role-assignments-powershell.md#remove-a-role-assignment)or Azure CLI

In PowerShell, se si tenta di rimuovere le assegnazioni di ruolo utilizzando l'ID oggetto e il nome della definizione di ruolo e più di un'assegnazione di ruolo corrisponde ai parametri, verrà visualizzato il messaggio di errore: "Le informazioni fornite non vengono mappate a un'assegnazione di ruolo". Di seguito è riportato un esempio del messaggio di errore:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se viene visualizzato questo messaggio di errore, assicurarsi di specificare anche i `-Scope` parametri o `-ResourceGroupName` .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Le modifiche all'assegnazione di ruolo non vengono rilevate

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Quando si aggiungono o rimuovono assegnazioni di ruolo, possono essere votì fino a 30 minuti per rendere effettive le modifiche. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

Se si aggiunge o si rimuove un'assegnazione `DataActions`di ruolo nell'ambito del gruppo di gestione e il ruolo ha , l'accesso sul piano dati potrebbe non essere aggiornato per diverse ore. Questo vale solo per l'ambito del gruppo di gestione e il piano dati.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Le funzionalità di gestione seguenti richiedono l'accesso in **scrittura** a un'app Web (Collaboratore o Proprietario) e non sono disponibili in uno scenario di sola lettura.

* Comandi (quali avvio, interruzione e così via)
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log di diagnostica
* Console (prompt dei comandi)
* Distribuzioni attive e recenti, per la distribuzione continua del Git locale
* Spesa prevista
* Test Web
* Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per l'app Web.

## <a name="web-app-resources-that-require-write-access"></a>Risorse dell'app Web che richiedono l'accesso in scrittura

La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/troubleshooting/website-resource-model.png)

Quindi, se si concede l'accesso solo all'app Web, la maggior parte delle funzionalità del pannello del sito Web nel portale di Azure viene disabilitata.

Questi elementi richiedono accesso **in scrittura** al **piano di servizio App** che corrisponde al sito Web:  

* Visualizzazione del piano tariffario dell'app Web, che può essere Gratuito o Standard  
* Configurazione di scalabilità, ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica  
* Quote, ad esempio archiviazione, larghezza di banda e CPU  

Gli elementi seguenti richiedono accesso **in scrittura** all'intero **gruppo di risorse** che contiene il sito Web:  

* Certificati e associazioni SSL. I certificati SSL possono essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica.  
* Regole di avviso  
* Impostazioni di scalabilità automatica  
* Componenti di Application Insights  
* Test Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funzionalità delle macchine virtuali che richiedono l'accesso in scrittura

Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le macchine virtuali sono correlate a nomi di dominio, reti virtuali, account di archiviazione e regole di avviso.

Gli elementi seguenti richiedono accesso **in scrittura** alla **macchina virtuale**:

* Endpoint  
* Indirizzi IP  
* Dischi  
* Estensioni  

Gli elementi seguenti richiedono accesso **in scrittura** sia alla **macchina virtuale** che al **gruppo di risorse**, così come al nome di dominio a cui appartiene:  

* Set di disponibilità  
* Set con carico bilanciato  
* Regole di avviso  

Se non è possibile accedere a nessuno di questi riquadri, richiedere all'amministratore l'accesso come Collaboratore al gruppo di risorse.

## <a name="azure-functions-and-write-access"></a>Funzioni di Azure e accesso in scrittura

Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio, se a un utente viene assegnato il ruolo [Lettore,](built-in-roles.md#reader) non sarà possibile visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)**.

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione. Per accedere a queste funzionalità, è necessario il ruolo [Collaboratore.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi per gli utenti guestTroubleshoot for guest users](role-assignments-external-users.md#troubleshoot)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md)
- [Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo alle risorse di Azure](change-history-report.md)
