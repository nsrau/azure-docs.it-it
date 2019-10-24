---
title: Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure | Microsoft Docs
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo per le risorse di Azure.
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
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 158222c256e3efc7ca87d7a3781ca68e1c4307b1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750178"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure

Questo articolo contiene le risposte alle domande comuni sul controllo degli accessi in base al ruolo per le risorse di Azure, per poter sapere che cosa accade quando si usano i ruoli nel portale di Azure e risolvere i problemi di accesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemi con le assegnazioni di ruolo di Controllo degli accessi in base al ruolo

- Se non è possibile aggiungere un'assegnazione di ruolo nel **controllo di accesso (IAM)** di portale di Azure perché l'opzione **Aggiungi**  > **Aggiungi assegnazione ruolo** è disabilitata o perché si riceve l'errore di autorizzazione "il client con ID oggetto non ha autorizzazione per eseguire l'azione ", verificare di avere eseguito l'accesso con un utente a cui è stato assegnato un ruolo con l'autorizzazione `Microsoft.Authorization/roleAssignments/write`, ad esempio [proprietario](built-in-roles.md#owner) o [amministratore accesso utenti](built-in-roles.md#user-access-administrator) , nell'ambito che si sta tentando di assegnare il ruolo.
- Se viene ricevuto il messaggio di errore "non è più possibile creare assegnazioni di ruolo (codice: RoleAssignmentLimitExceeded)" quando si tenta di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo assegnando ruoli ai gruppi. Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione.

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Per istruzioni su come creare un ruolo personalizzato, vedere le esercitazioni sui ruoli personalizzati con [Azure PowerShell](tutorial-custom-role-powershell.md) o l' [interfaccia](tutorial-custom-role-cli.md)della riga di comando di Azure.
- Se non è possibile aggiornare un ruolo personalizzato esistente, verificare di avere eseguito l'accesso con un utente a cui è stato assegnato un ruolo con l'autorizzazione `Microsoft.Authorization/roleDefinition/write`, ad esempio [proprietario](built-in-roles.md#owner) o [amministratore accesso utenti](built-in-roles.md#user-access-administrator).
- Se non è possibile eliminare un ruolo personalizzato e viene ricevuto il messaggio di errore "esistono assegnazioni di ruolo che fanno riferimento a un ruolo (codice: RoleDefinitionHasAssignments)", sono ancora presenti assegnazioni di ruolo che usano il ruolo personalizzato. Rimuovere le assegnazioni di ruolo e provare di nuovo a eliminare il ruolo personalizzato.
- Se viene visualizzato il messaggio di errore "Limite di definizioni del ruolo superato. Non è possibile creare più definizioni di ruolo (codice: RoleDefinitionLimitExceeded) "quando si tenta di creare un nuovo ruolo personalizzato, eliminare tutti i ruoli personalizzati che non vengono usati. Azure supporta fino a **5000** ruoli personalizzati in un tenant. (per i cloud specializzati, ad esempio Azure per enti pubblici, Azure Germania e Azure Cina 21Vianet, il limite è di 2.000 ruoli personalizzati).
- Se viene ricevuto un errore simile a "il client dispone dell'autorizzazione per eseguire l'azione ' Microsoft. Authorization/roleDefinitions/Write ' nell'ambito '/subscriptions/{SubscriptionId}', ma la sottoscrizione collegata non è stata trovata" quando si tenta di aggiornare un ruolo personalizzato, controllare Se uno o più [ambiti assegnabili](role-definitions.md#assignablescopes) sono stati eliminati nel tenant. Se l'ambito è stato eliminato, creare un ticket di supporto perché attualmente non è disponibile alcuna soluzione self-service.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Ripristinare il Controllo degli accessi in base al ruolo quando le sottoscrizioni vengono spostate tra i tenant

- Per istruzioni su come trasferire una sottoscrizione a un tenant di Azure AD diverso, vedere trasferire la [proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md).
- Quando si trasferisce una sottoscrizione a un altro tenant di Azure Active Directory, tutte le assegnazioni di ruolo vengono eliminate definitivamente dal tenant di Azure Active Directory di origine e non ne viene eseguita la migrazione al tenant di Azure Active Directory di destinazione. È necessario creare nuovamente le assegnazioni di ruolo nel tenant di destinazione. È anche necessario ricreare manualmente le identità gestite per le risorse di Azure. Per altre informazioni, vedere [domande frequenti e problemi noti relativi alle identità gestite](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se si è Azure AD amministratore globale e non si ha accesso a una sottoscrizione dopo che è stato spostato tra i tenant, usare l'interruttore **gestione accessi per le risorse di Azure** per [elevare](elevate-access-global-admin.md) temporaneamente l'accesso per ottenere l'accesso alla sottoscrizione.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemi con gli amministratori del servizio o i coamministratori

- Se si verificano problemi con l'amministratore del servizio o con i coamministratori, vedere [aggiungere o modificare gli amministratori della sottoscrizione di Azure](../billing/billing-add-change-azure-subscription-administrator.md) e i [ruoli di amministratore della sottoscrizione classica, i ruoli RBAC di Azure e i ruoli di amministratore Azure ad](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Errori di accesso negato o autorizzazione

- Se si riceve l'errore di autorizzazione "il client con ID oggetto non dispone dell'autorizzazione per eseguire l'azione sull'ambito (codice: AuthorizationFailed)" quando si tenta di creare una risorsa, verificare di avere eseguito l'accesso con un utente a cui è assegnato un ruolo con scrittura autorizzazione per la risorsa nell'ambito selezionato. Ad esempio, per gestire le macchine virtuali in un gruppo di risorse, è necessario disporre del ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) nel gruppo di risorse (o nell'ambito padre). Per un elenco delle autorizzazioni per ogni ruolo predefinito, vedere [Ruoli predefiniti per le risorse di Azure](built-in-roles.md).
- Se viene ricevuto l'errore di autorizzazione "non si è autorizzati a creare una richiesta di supporto" quando si tenta di creare o aggiornare un ticket di supporto, verificare di avere eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione `Microsoft.Support/supportTickets/write` , ad esempio il [collaboratore alla richiesta di supporto](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Assegnazioni di ruolo con entità di sicurezza sconosciuta

Quando si elencano le assegnazioni di ruolo utilizzando Azure PowerShell, è possibile che vengano visualizzate assegnazioni con una `DisplayName` vuota e una `ObjectType` impostata su Unknown. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) , ad esempio, restituisce un'assegnazione di ruolo simile alla seguente:

```azurepowershell
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

Analogamente, quando si elencano le assegnazioni di ruolo usando l'interfaccia della riga di comando di Azure, è possibile che vengano visualizzate assegnazioni con un `principalName` vuoto Ad esempio, [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list) restituisce un'assegnazione di ruolo simile alla seguente:

```azurecli
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

Queste assegnazioni di ruolo si verificano quando si assegna un ruolo a un'entità di sicurezza (utente, gruppo, entità servizio o identità gestita) e successivamente si elimina tale entità di sicurezza. Queste assegnazioni di ruolo non vengono visualizzate nel portale di Azure e non è un problema lasciarle. Tuttavia, se si desidera, è possibile rimuovere queste assegnazioni di ruoli.

Per rimuovere queste assegnazioni di ruolo, usare i comandi [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) o [AZ Role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete) .

In PowerShell, se si tenta di rimuovere le assegnazioni di ruolo utilizzando l'ID oggetto e il nome della definizione di ruolo e più di un'assegnazione di ruolo corrisponde ai parametri, verrà visualizzato il messaggio di errore: "le informazioni fornite non sono mappate a un'assegnazione di ruolo". Di seguito viene illustrato un esempio del messaggio di errore:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se viene ricevuto questo messaggio di errore, assicurarsi di specificare anche i parametri `-Scope` o `-ResourceGroupName`.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Le modifiche del controllo degli accessi in base al ruolo non vengono rilevate

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Durante la creazione o l'eliminazione delle assegnazioni di ruolo, per l'applicazione delle modifiche possono essere necessari fino a 30 minuti. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Per le funzionalità di gestione seguenti, è necessario avere accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario. Tali funzionalità non saranno disponibili in uno scenario di sola lettura.

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

Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio, se a un utente viene assegnato il ruolo [lettore](built-in-roles.md#reader) , non sarà in grado di visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)** .

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione. Per accedere a queste funzionalità, sarà necessario il ruolo [collaboratore](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi per gli utenti Guest](role-assignments-external-users.md#troubleshoot)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md)
- [Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo alle risorse di Azure](change-history-report.md)

