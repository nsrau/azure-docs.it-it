---
title: Risolvere i problemi relativi a RBAC di Azure
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 069c290de0278202b2e20d67f0ce792a0a79c345
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368231"
---
# <a name="troubleshoot-azure-rbac"></a>Risolvere i problemi relativi a RBAC di Azure

Questo articolo risponde ad alcune domande comuni sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure), in modo da sapere cosa accade quando si usano i ruoli e può risolvere i problemi di accesso.

## <a name="azure-role-assignments-limit"></a>Limite assegnazioni di ruolo di Azure

Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione. Questo limite include le assegnazioni di ruolo negli ambiti di sottoscrizione, gruppo di risorse e risorsa. Se viene ricevuto il messaggio di errore "non è più possibile creare assegnazioni di ruolo (codice: RoleAssignmentLimitExceeded)" quando si tenta di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo nella sottoscrizione.

> [!NOTE]
> Il limite di assegnazioni di ruolo **2000** per ogni sottoscrizione è fisso e non può essere aumentato.

Se si avvicina questo limite, di seguito sono riportati alcuni modi in cui è possibile ridurre il numero di assegnazioni di ruolo:

- Aggiungere utenti ai gruppi e assegnare ruoli ai gruppi. 
- Combinare più ruoli predefiniti con un ruolo personalizzato. 
- Eseguire assegnazioni di ruolo comuni a un ambito superiore, ad esempio una sottoscrizione o un gruppo di gestione.
- Se si dispone di Azure AD Premium P2, rendere le assegnazioni di ruolo idonee per [Azure ad Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) invece che assegnate in modo permanente. 
- Aggiungere una sottoscrizione aggiuntiva. 

Per ottenere il numero di assegnazioni di ruolo, è possibile visualizzare il [grafico nella pagina controllo di accesso (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) del portale di Azure. È anche possibile usare i comandi di Azure PowerShell seguenti:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemi con le assegnazioni di ruolo di Azure

- Se non è possibile aggiungere un'assegnazione di ruolo nel **controllo di accesso (IAM)** di portale di Azure perché l'opzione **Aggiungi**  >  **assegnazione ruolo** è disabilitata o perché si riceve l'errore di autorizzazione "il client con ID oggetto non dispone dell'autorizzazione per eseguire l'azione", verificare di aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l' `Microsoft.Authorization/roleAssignments/write` autorizzazione, ad esempio [proprietario](built-in-roles.md#owner) o [amministratore accesso utenti](built-in-roles.md#user-access-administrator) , nell'ambito che si sta provando ad assegnare il ruolo.
- Se si utilizza un'entità servizio per assegnare i ruoli, è possibile che venga ricevuto l'errore "privilegi insufficienti per completare l'operazione". Si immagini, ad esempio, di disporre di un'entità servizio a cui è stato assegnato il ruolo di proprietario e si tenta di creare l'assegnazione di ruolo seguente come entità servizio usando l'interfaccia della riga di comando di Azure:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Se si riceve l'errore "privilegi insufficienti per completare l'operazione", è probabile che l'interfaccia della riga di comando di Azure tenti di cercare l'identità assegnata in Azure AD e che l'entità servizio non sia in grado di leggere Azure AD per impostazione predefinita.

    Esistono due modi per risolvere l'errore. Il primo consiste nell'assegnare il ruolo [Readers di directory](../active-directory/roles/permissions-reference.md#directory-readers) all'entità servizio in modo che possa leggere i dati nella directory.

    Il secondo modo per risolvere l'errore consiste nel creare l'assegnazione di ruolo utilizzando il `--assignee-object-id` parametro anziché `--assignee` . Con, l'interfaccia della riga di comando di Azure ignorerà `--assignee-object-id` la ricerca Azure ad. Sarà necessario ottenere l'ID oggetto dell'utente, del gruppo o dell'applicazione a cui si desidera assegnare il ruolo. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia](role-assignments-cli.md#add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope)della riga di comando di Azure.

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Per istruzioni su come creare un ruolo personalizzato, vedere le esercitazioni sui ruoli personalizzati usando il [portale di Azure](custom-roles-portal.md) (attualmente in anteprima), [Azure PowerShell](tutorial-custom-role-powershell.md)o l'interfaccia della riga di comando di [Azure](tutorial-custom-role-cli.md).
- Se non è possibile aggiornare un ruolo personalizzato esistente, verificare di avere eseguito l'accesso con un utente a cui è stato assegnato un ruolo con l' `Microsoft.Authorization/roleDefinition/write` autorizzazione, ad esempio [proprietario](built-in-roles.md#owner) o [amministratore accesso utenti](built-in-roles.md#user-access-administrator).
- Se non è possibile eliminare un ruolo personalizzato e ricevere il messaggio di errore "Sono presenti assegnazioni di ruolo esistenti che fanno riferimento a ruolo (codice: RoleDefinitionHasAssignments)", esistono assegnazioni di ruolo che usano ancora il ruolo personalizzato. Rimuovere le assegnazioni di ruolo e provare di nuovo a eliminare il ruolo personalizzato.
- Se viene visualizzato il messaggio di errore "Limite di definizioni del ruolo superato. Non è possibile creare più definizioni di ruolo (codice: RoleDefinitionLimitExceeded) "quando si tenta di creare un nuovo ruolo personalizzato, eliminare tutti i ruoli personalizzati che non vengono usati. Azure supporta fino a **5000** ruoli personalizzati in una directory. (Per Azure Germania e Azure Cina 21Vianet, il limite è 2000 ruoli personalizzati).
- Se viene rilevato un errore simile a "il client dispone dell'autorizzazione per eseguire l'azione ' Microsoft. Authorization/roleDefinitions/Write ' nell'ambito '/subscriptions/{SubscriptionId}', ma la sottoscrizione collegata non è stata trovata" quando si tenta di aggiornare un ruolo personalizzato, controllare se uno o più [ambiti assegnabili](role-definitions.md#assignablescopes) sono stati eliminati nella directory. Se l'ambito è stato eliminato, creare un ticket di supporto perché attualmente non è disponibile alcuna soluzione self-service.

## <a name="custom-roles-and-management-groups"></a>Ruoli personalizzati e gruppi di gestione

- È possibile definire un solo gruppo di gestione in `AssignableScopes` di un ruolo personalizzato. L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima.
- I ruoli personalizzati con `DataActions` non possono essere assegnati all'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione di ruolo.
- Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere [organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Trasferimento di una sottoscrizione a una directory diversa

- Per istruzioni su come trasferire una sottoscrizione a una directory Azure AD diversa, vedere [trasferire una sottoscrizione di Azure a una directory di Azure ad diversa](transfer-subscription.md).
- Se si trasferisce una sottoscrizione a una directory Azure AD diversa, tutte le assegnazioni di ruolo vengono eliminate **definitivamente** dalla directory di Azure ad di origine e non vengono migrate alla directory Azure ad di destinazione. È necessario ricreare le assegnazioni di ruolo nella directory di destinazione. È anche necessario ricreare manualmente le identità gestite per le risorse di Azure. Per altre informazioni, vedere [domande frequenti e problemi noti relativi alle identità gestite](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se si è un Azure AD amministratore globale e non si ha accesso a una sottoscrizione dopo che è stato trasferito tra le directory, usare l'interruttore **gestione accessi per le risorse di Azure** per [elevare](elevate-access-global-admin.md) temporaneamente l'accesso per ottenere l'accesso alla sottoscrizione.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemi con gli amministratori del servizio o i coamministratori

- Se si verificano problemi con l'amministratore del servizio o con i coamministratori, vedere [aggiungere o modificare gli amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e i [ruoli di amministratore della sottoscrizione classica, i ruoli di Azure e i ruoli Azure ad](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Errori di accesso negato o autorizzazione

- Se si riceve l'errore relativo alle autorizzazioni "Il client con ID oggetto non è autorizzato a eseguire l'azione sull'ambito (codice: AuthorizationFailed)" quando si cerca di creare una risorsa, verificare di aver effettuato l'accesso con un utente a cui è assegnato un ruolo con autorizzazione di scrittura alla risorsa nell'ambito selezionato. Ad esempio, per gestire le macchine virtuali in un gruppo di risorse, è necessario disporre del ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) nel gruppo di risorse (o nell'ambito padre). Per un elenco delle autorizzazioni per ogni ruolo predefinito, vedere [ruoli predefiniti di Azure](built-in-roles.md).
- Se si riceve l'errore di autorizzazione "non si è autorizzati a creare una richiesta di supporto" quando si tenta di creare o aggiornare un ticket di supporto, verificare di avere eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell' `Microsoft.Support/supportTickets/write` autorizzazione, ad esempio [supporto richieste di supporto](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Spostare le risorse con assegnazioni di ruolo

Se si sposta una risorsa che dispone di un ruolo di Azure assegnato direttamente alla risorsa (o a una risorsa figlio), l'assegnazione di ruolo non viene spostata e diventa orfana. Dopo lo spostamento, è necessario ricreare l'assegnazione di ruolo. Infine, l'assegnazione di ruolo orfana verrà rimossa automaticamente, ma è consigliabile rimuovere l'assegnazione di ruolo prima di spostare la risorsa.

Per informazioni su come spostare le risorse, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Assegnazioni di ruolo con identità non trovate

Nell'elenco delle assegnazioni di ruolo per la portale di Azure è possibile notare che l'entità di sicurezza (utente, gruppo, entità servizio o identità gestita) è elencata come **identità non trovata** con un tipo **sconosciuto** .

![Identità non trovata nell'elenco assegnazioni di ruolo di Azure](./media/troubleshooting/unknown-security-principal.png)

È possibile che l'identità non sia stata trovata per due motivi:

- È stato invitato di recente un utente durante la creazione di un'assegnazione di ruolo
- È stata eliminata un'entità di sicurezza con un'assegnazione di ruolo

Se di recente è stato invitato un utente durante la creazione di un'assegnazione di ruolo, questa entità di sicurezza potrebbe essere ancora nel processo di replica tra le aree. In tal caso, attendere alcuni istanti e aggiornare l'elenco assegnazioni di ruolo.

Tuttavia, se questa entità di sicurezza non è un utente invitato recentemente, potrebbe trattarsi di un'entità di sicurezza eliminata. Se si assegna un ruolo a un'entità di sicurezza e successivamente si elimina tale entità di sicurezza senza prima rimuovere l'assegnazione di ruolo, l'entità di sicurezza sarà elencata come **identità non trovata** e un tipo **sconosciuto** .

Se l'assegnazione di ruolo viene elencata utilizzando Azure PowerShell, è possibile che venga visualizzato un oggetto vuoto `DisplayName` e un `ObjectType` valore impostato su **Unknown**. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) , ad esempio, restituisce un'assegnazione di ruolo simile all'output seguente:

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

Analogamente, se si elenca questa assegnazione di ruolo usando l'interfaccia della riga di comando di Azure, potrebbe essere presente un oggetto vuoto `principalName` . Ad esempio, [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list) restituisce un'assegnazione di ruolo simile all'output seguente:

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

Non è un problema lasciare queste assegnazioni di ruolo in cui l'entità di sicurezza è stata eliminata. Se lo si desidera, è possibile rimuovere queste assegnazioni di ruolo utilizzando passaggi simili ad altre assegnazioni di ruolo. Per informazioni su come rimuovere le assegnazioni di ruolo, vedere [portale di Azure](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)o l'interfaccia della riga di comando di [Azure](role-assignments-cli.md#remove-role-assignment)

In PowerShell, se si tenta di rimuovere le assegnazioni di ruolo utilizzando l'ID oggetto e il nome della definizione di ruolo e più di un'assegnazione di ruolo corrisponde ai parametri, verrà visualizzato il messaggio di errore: "le informazioni fornite non sono mappate a un'assegnazione di ruolo". L'output seguente mostra un esempio del messaggio di errore:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se viene ricevuto questo messaggio di errore, assicurarsi di specificare anche i `-Scope` `-ResourceGroupName` parametri o.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Non sono state rilevate modifiche alle assegnazioni di ruolo

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Quando si aggiungono o rimuovono le assegnazioni di ruolo, possono essere necessari fino a 30 minuti per rendere effettive le modifiche. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

Se si aggiunge o rimuove un'assegnazione di ruolo nell'ambito del gruppo di gestione e il ruolo ha `DataActions` , l'accesso al piano dati potrebbe non essere aggiornato per diverse ore. Si applica solo all'ambito del gruppo di gestione e al piano dati.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Le funzionalità di gestione seguenti richiedono l'accesso in **scrittura** a un'app Web (collaboratore o proprietario) e non sono disponibili in uno scenario di sola lettura.

* Comandi (quali avvio, interruzione e così via)
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log delle risorse
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

* Certificati e associazioni TLS/SSL (i certificati TLS/SSL possono essere condivisi tra siti nello stesso gruppo di risorse e nella stessa posizione geografica)  
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

Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio, se a un utente viene assegnato il ruolo [lettore](built-in-roles.md#reader) , non sarà in grado di visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)**.

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione. Per accedere a queste funzionalità, sarà necessario il ruolo [collaboratore](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi per gli utenti Guest](role-assignments-external-users.md#troubleshoot)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
- [Visualizzare i log attività per le modifiche RBAC di Azure](change-history-report.md)
