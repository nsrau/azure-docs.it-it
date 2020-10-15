---
title: Trasferire una sottoscrizione di Azure a una directory Azure AD diversa
description: Informazioni su come trasferire una sottoscrizione di Azure e le risorse correlate note a una directory Azure Active Directory (Azure AD) diversa.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 10/06/2020
ms.author: rolyon
ms.openlocfilehash: 35c6d94ce69acf59ae6cd8b26b0ad75645eb526a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819712"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Trasferire una sottoscrizione di Azure a una directory Azure AD diversa

Le organizzazioni potrebbero avere più sottoscrizioni di Azure. Ogni sottoscrizione è associata a una particolare directory Azure Active Directory (Azure AD). Per semplificare la gestione, è consigliabile trasferire una sottoscrizione a un'altra directory di Azure AD. Quando si trasferisce una sottoscrizione a una directory Azure AD diversa, alcune risorse non vengono trasferite alla directory di destinazione. Ad esempio, tutte le assegnazioni di ruolo e i ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure (RBAC di Azure) vengono eliminati **definitivamente** dalla directory di origine e non vengono trasferiti alla directory di destinazione.

Questo articolo descrive i passaggi di base che è possibile seguire per trasferire una sottoscrizione a una directory Azure AD diversa e ricreare alcune risorse dopo il trasferimento.

> [!NOTE]
> Per le sottoscrizioni di Azure Cloud Solution Provider (CSP), la modifica della Azure AD directory per la sottoscrizione non è supportata.

## <a name="overview"></a>Panoramica

Il trasferimento di una sottoscrizione di Azure a una directory Azure AD diversa è un processo complesso che deve essere accuratamente pianificato ed eseguito. Molti servizi di Azure richiedono entità di sicurezza (identità) per funzionare normalmente o anche per gestire altre risorse di Azure. Questo articolo prova a coprire la maggior parte dei servizi di Azure che dipendono molto dalle entità di sicurezza, ma non è completa.

> [!IMPORTANT]
> In alcuni scenari, il trasferimento di una sottoscrizione potrebbe richiedere tempi di inattività per il completamento del processo. È necessaria un'attenta pianificazione per valutare se i tempi di inattività saranno necessari per il trasferimento.

Il diagramma seguente illustra i passaggi di base che è necessario seguire quando si trasferisce una sottoscrizione a una directory diversa.

1. Preparare il trasferimento

1. Trasferire la sottoscrizione di Azure in una directory diversa

1. Ricreare le risorse nella directory di destinazione, ad esempio le assegnazioni di ruolo, i ruoli personalizzati e le identità gestite

    ![Diagramma Transfer Subscription](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Decidere se trasferire una sottoscrizione a una directory diversa

Di seguito sono riportati alcuni dei motivi per cui è consigliabile trasferire una sottoscrizione:

- A causa di una fusione aziendale o di un'acquisizione, è necessario gestire una sottoscrizione acquisita nella directory Azure AD primaria.
- Un utente dell'organizzazione ha creato una sottoscrizione e si desidera consolidare la gestione in una particolare directory Azure AD.
- Sono presenti applicazioni che dipendono da un ID sottoscrizione o un URL specifico e non è facile modificare la configurazione o il codice dell'applicazione.
- Una parte del business è stata suddivisa in una società separata ed è necessario spostare alcune risorse in un'altra directory Azure AD.
- Si desidera gestire alcune risorse in una directory Azure AD diversa a scopo di isolamento della sicurezza.

Il trasferimento di una sottoscrizione richiede tempi di inattività per il completamento del processo. A seconda dello scenario, potrebbe essere preferibile ricreare solo le risorse e copiare i dati nella directory e nella sottoscrizione di destinazione.

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Comprendere l'effetto del trasferimento di una sottoscrizione

Diverse risorse di Azure hanno una dipendenza da una sottoscrizione o una directory. A seconda della situazione, nella tabella seguente sono elencati gli effetti noti del trasferimento di una sottoscrizione. Eseguendo la procedura descritta in questo articolo, è possibile ricreare alcune delle risorse esistenti prima del trasferimento della sottoscrizione.

> [!IMPORTANT]
> Questa sezione elenca i servizi o le risorse di Azure noti che dipendono dalla sottoscrizione. Poiché i tipi di risorse in Azure sono in continua evoluzione, potrebbero essere presenti dipendenze aggiuntive non elencate in questa sezione che possono causare una modifica sostanziale dell'ambiente. 

| Servizio o risorsa | Influenzato | Recuperabile | Si è interessati? | Operazioni possibili |
| --------- | --------- | --------- | --------- | --------- |
| Assegnazioni di ruoli | Sì | Sì | [Elencare le assegnazioni di ruolo](#save-all-role-assignments) | Tutte le assegnazioni di ruolo vengono eliminate definitivamente. È necessario eseguire il mapping di utenti, gruppi ed entità servizio a oggetti corrispondenti nella directory di destinazione. È necessario ricreare le assegnazioni di ruolo. |
| Ruoli personalizzati | Sì | Sì | [Elencare ruoli personalizzati](#save-custom-roles) | Tutti i ruoli personalizzati vengono eliminati definitivamente. È necessario ricreare i ruoli personalizzati e le assegnazioni di ruolo. |
| Identità gestite assegnate dal sistema | Sì | Sì | [Elencare le identità gestite](#list-role-assignments-for-managed-identities) | È necessario disabilitare e riabilitare le identità gestite. È necessario ricreare le assegnazioni di ruolo. |
| Identità gestite assegnate dall'utente | Sì | Sì | [Elencare le identità gestite](#list-role-assignments-for-managed-identities) | È necessario eliminare, ricreare e collegare le identità gestite alla risorsa appropriata. È necessario ricreare le assegnazioni di ruolo. |
| Insieme di credenziali chiave di Azure | Sì | Sì | [Elencare i criteri di accesso Key Vault](#list-key-vaults) | È necessario aggiornare l'ID tenant associato agli insiemi di credenziali delle chiavi. È necessario rimuovere e aggiungere nuovi criteri di accesso. |
| Database SQL di Azure con Azure AD Integration Authentication abilitato | Sì | No | [Controllare i database SQL di Azure con l'autenticazione Azure AD](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Archiviazione di Azure e Azure Data Lake Storage Gen2 | Sì | Sì |  | È necessario ricreare gli ACL. |
| Azure Data Lake Storage Gen1 | Sì | Sì |  | È necessario ricreare gli ACL. |
| File di Azure | Sì | Sì |  | È necessario ricreare gli ACL. |
| Sincronizzazione file di Azure | Sì | Sì |  |  |
| Azure Managed Disks | Sì | Sì |  |  Se si usano i set di crittografia del disco per crittografare Managed Disks con chiavi gestite dal cliente, è necessario disabilitare e riabilitare le identità assegnate dal sistema associate ai set di crittografia del disco. È necessario ricreare le assegnazioni di ruolo, ovvero concedere di nuovo le autorizzazioni necessarie per i set di crittografia del disco negli insiemi di credenziali delle chiavi. |
| Servizi contenitore di Azure per Kubernetes | Sì | Sì |  |  |
| Servizi di dominio Azure Active Directory | Sì | No |  |  |
| Registrazioni per l'app | Sì | Sì |  |  |

> [!WARNING]
> Se si usa la crittografia dei dati inattivi per una risorsa, ad esempio un account di archiviazione o un database SQL, che ha una dipendenza da un insieme di credenziali delle chiavi che **non** si trova nella stessa sottoscrizione da trasferire, può causare uno scenario irreversibile. In tal caso, è necessario eseguire i passaggi per usare un insieme di credenziali delle chiavi diverso o disabilitare temporaneamente le chiavi gestite dal cliente per evitare questo scenario irreversibile.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi, sarà necessario:

- [Bash in Azure cloud Shell o nell'interfaccia della riga di comando di](/azure/cloud-shell/overview) [Azure](/cli/azure)
- Amministratore account della sottoscrizione che si desidera trasferire nella directory di origine
- Ruolo [proprietario](built-in-roles.md#owner) nella directory di destinazione

## <a name="step-1-prepare-for-the-transfer"></a>Passaggio 1: preparare il trasferimento

### <a name="sign-in-to-source-directory"></a>Accedi alla directory di origine

1. Accedere ad Azure come amministratore.

1. Ottenere un elenco delle sottoscrizioni con il comando [AZ account list](/cli/azure/account#az_account_list) .

    ```azurecli
    az account list --output table
    ```

1. Usare [AZ account set](/cli/azure/account#az_account_set) per impostare la sottoscrizione attiva che si desidera trasferire.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>Installare l'estensione Graph di risorse

 L'estensione Graph di risorse consente di usare il comando [AZ Graph](/cli/azure/ext/resource-graph/graph) per eseguire query sulle risorse gestite da Azure Resource Manager. Questo comando verrà usato nei passaggi successivi.

1. Usare [AZ Extension List](/cli/azure/extension#az_extension_list) per verificare se l'estensione del *grafico risorse* è installata.

    ```azurecli
    az extension list
    ```

1. In caso contrario, installare l'estensione *Graph di risorse* .

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Salva tutte le assegnazioni di ruolo

1. Usare [AZ Role Assignment list](/cli/azure/role/assignment#az_role_assignment_list) per elencare tutte le assegnazioni di ruolo, incluse le assegnazioni di ruolo ereditate.

    Per semplificare la revisione dell'elenco, è possibile esportare l'output come JSON, TSV o una tabella. Per altre informazioni, vedere [elencare le assegnazioni di ruolo usando RBAC di Azure e l'interfaccia](role-assignments-list-cli.md)della riga di comando

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Salvare l'elenco di assegnazioni di ruolo.

    Quando si trasferisce una sottoscrizione, tutte le assegnazioni di ruolo vengono eliminate **definitivamente** , quindi è importante salvare una copia.

1. Esaminare l'elenco di assegnazioni di ruolo. Potrebbero essere presenti assegnazioni di ruolo che non saranno necessarie nella directory di destinazione.

### <a name="save-custom-roles"></a>Salva ruoli personalizzati

1. Usare l' [elenco AZ Role Definition](/cli/azure/role/definition#az_role_definition_list) per elencare i ruoli personalizzati. Per altre informazioni, vedere [creare o aggiornare i ruoli personalizzati di Azure con l'interfaccia](custom-roles-cli.md)della riga di comando di Azure.

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Salvare ogni ruolo personalizzato che sarà necessario nella directory di destinazione come file JSON separato.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Creare copie dei file del ruolo personalizzato.

1. Modificare ogni copia per utilizzare il formato seguente.

    Questi file verranno usati in un secondo momento per ricreare i ruoli personalizzati nella directory di destinazione.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Determinare i mapping di utenti, gruppi ed entità servizio

1. In base all'elenco di assegnazioni di ruolo, determinare gli utenti, i gruppi e le entità servizio a cui si eseguirà il mapping nella directory di destinazione.

    È possibile identificare il tipo di entità esaminando la `principalType` Proprietà in ogni assegnazione di ruolo.

1. Se necessario, nella directory di destinazione creare gli utenti, i gruppi o le entità servizio necessari.

### <a name="list-role-assignments-for-managed-identities"></a>Elencare le assegnazioni di ruolo per le identità gestite

Le identità gestite non vengono aggiornate quando si trasferisce una sottoscrizione a un'altra directory. Di conseguenza, tutte le identità gestite assegnate dall'utente o dal sistema esistenti verranno interrotte. Dopo il trasferimento, è possibile riabilitare le identità gestite assegnate dal sistema. Per le identità gestite assegnate dall'utente, sarà necessario ricrearle e collegarle nella directory di destinazione.

1. Esaminare l' [elenco dei servizi di Azure che supportano le identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) per tenere presente dove si potrebbero usare identità gestite.

1. Usare [AZ ad SP list](/cli/azure/ad/sp#az_ad_sp_list) per elencare le identità gestite assegnate dall'utente e assegnate dal sistema.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Nell'elenco delle identità gestite determinare quali sono assegnati dal sistema e quali sono assegnati dall'utente. È possibile utilizzare i criteri seguenti per determinare il tipo.

    | Criteri | Tipo di identità gestita |
    | --- | --- |
    | `alternativeNames` inclusioni proprietà `isExplicit=False` | Assegnato dal sistema |
    | `alternativeNames` la proprietà non include `isExplicit` | Assegnato dal sistema |
    | `alternativeNames` inclusioni proprietà `isExplicit=True` | Assegnati dall'utente |

    È anche possibile usare [AZ Identity list](/cli/azure/identity#az_identity_list) per elencare solo le identità gestite assegnate dall'utente. Per altre informazioni, vedere [creare, elencare o eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)riga di comando di Azure.

    ```azurecli
    az identity list
    ```

1. Ottenere un elenco dei `objectId` valori per le identità gestite.

1. Cercare nell'elenco le assegnazioni di ruolo per verificare se sono presenti assegnazioni di ruolo per le identità gestite.

### <a name="list-key-vaults"></a>List key vaults

Quando si crea un insieme di credenziali delle chiavi, questo viene automaticamente associato all'ID tenant predefinito Azure Active Directory per la sottoscrizione in cui viene creato. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Per ulteriori informazioni, vedere [passaggio di un Azure Key Vault a un'altra sottoscrizione](../key-vault/general/move-subscription.md).

> [!WARNING]
> Se si usa la crittografia dei dati inattivi per una risorsa, ad esempio un account di archiviazione o un database SQL, che ha una dipendenza da un insieme di credenziali delle chiavi che **non** si trova nella stessa sottoscrizione da trasferire, può causare uno scenario irreversibile. In tal caso, è necessario eseguire i passaggi per usare un insieme di credenziali delle chiavi diverso o disabilitare temporaneamente le chiavi gestite dal cliente per evitare questo scenario irreversibile.

- Se si dispone di un insieme di credenziali delle chiavi, usare [AZ Key Vault Show](/cli/azure/keyvault#az_keyvault_show) per elencare i criteri di accesso. Per altre informazioni, vedere [assegnare un criterio di accesso key Vault](../key-vault/general/assign-access-policy-cli.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Elencare i database SQL di Azure con autenticazione Azure AD

- Usare [AZ SQL Server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) e [AZ Graph](/cli/azure/ext/resource-graph/graph) Extension per verificare se si usano database sql di Azure con Azure ad l'integrazione dell'autenticazione abilitata. Per altre informazioni, vedere [configurare e gestire Azure Active Directory autenticazione con SQL](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Elenca elenchi ACL

1. Se si usa Azure Data Lake Storage Gen1, elencare gli ACL applicati a qualsiasi file usando il portale di Azure o PowerShell.

1. Se si usa Azure Data Lake Storage Gen2, elencare gli ACL applicati a qualsiasi file usando il portale di Azure o PowerShell.

1. Se si utilizza File di Azure, elencare gli ACL applicati a qualsiasi file.

### <a name="list-other-known-resources"></a>Elencare altre risorse note

1. Usare [AZ account Show](/cli/azure/account#az_account_show) per ottenere l'ID sottoscrizione.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Usare [AZ Graph](/cli/azure/ext/resource-graph/graph) Extension per elencare altre risorse di Azure con le dipendenze della directory Azure ad note.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Passaggio 2: trasferire la sottoscrizione

In questo passaggio la sottoscrizione viene trasferita dalla directory di origine alla directory di destinazione. La procedura varia a seconda che si desideri trasferire anche la proprietà della fatturazione.

> [!WARNING]
> Quando si trasferisce la sottoscrizione, tutte le assegnazioni di ruolo nella directory di origine vengono eliminate **definitivamente** e non possono essere ripristinate. Non è possibile tornare indietro dopo il trasferimento della sottoscrizione. Assicurarsi di completare i passaggi precedenti prima di eseguire questo passaggio.

1. Determinare se si desidera trasferire anche la proprietà di fatturazione a un altro account.

1. Trasferire la sottoscrizione a una directory diversa.

    - Se si vuole gestire la proprietà di fatturazione corrente, seguire la procedura descritta in [associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    - Se si vuole anche trasferire la proprietà di fatturazione, seguire la procedura descritta in [trasferire la proprietà della fatturazione di una sottoscrizione di Azure a un altro account](../cost-management-billing/manage/billing-subscription-transfer.md). Per trasferire la sottoscrizione a una directory diversa, è necessario selezionare la casella di controllo **sottoscrizione Azure ad tenant** .

1. Al termine del trasferimento della sottoscrizione, tornare a questo articolo per ricreare le risorse nella directory di destinazione.

## <a name="step-3-re-create-resources"></a>Passaggio 3: ricreare le risorse

### <a name="sign-in-to-target-directory"></a>Accedi alla directory di destinazione

1. Nella directory di destinazione accedere come utente che ha accettato la richiesta di trasferimento.

    Solo l'utente nel nuovo account che ha accettato la richiesta di trasferimento avrà accesso per gestire le risorse.

1. Ottenere un elenco delle sottoscrizioni con il comando [AZ account list](/cli/azure/account#az_account_list) .

    ```azurecli
    az account list --output table
    ```

1. Usare [AZ account set](/cli/azure/account#az_account_set) per impostare la sottoscrizione attiva che si vuole usare.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Creare ruoli personalizzati
        
- Usare [AZ Role Definition create](/cli/azure/role/definition#az_role_definition_create) per creare ogni ruolo personalizzato dai file creati in precedenza. Per altre informazioni, vedere [creare o aggiornare i ruoli personalizzati di Azure con l'interfaccia](custom-roles-cli.md)della riga di comando di Azure.

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>Creare assegnazioni di ruolo

- Usare [AZ Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create) per creare le assegnazioni di ruolo per utenti, gruppi ed entità servizio. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e l'interfaccia](role-assignments-cli.md)della riga di comando di Azure.

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Aggiornare le identità gestite assegnate dal sistema

1. Disabilitare e riabilitare le identità gestite assegnate dal sistema.

    | Servizio di Azure | Ulteriori informazioni | 
    | --- | --- |
    | Macchine virtuali | [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | set di scalabilità di macchine virtuali | [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Altri servizi | [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Usare [AZ Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create) per creare le assegnazioni di ruolo per le identità gestite assegnate dal sistema. Per altre informazioni, vedere [assegnare un accesso a identità gestite a una risorsa tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Aggiornare le identità gestite assegnate dall'utente

1. Elimina, ricrea e Connetti identità gestite assegnate dall'utente.

    | Servizio di Azure | Ulteriori informazioni | 
    | --- | --- |
    | Macchine virtuali | [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | set di scalabilità di macchine virtuali | [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Altri servizi | [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Usare [AZ Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create) per creare le assegnazioni di ruolo per le identità gestite assegnate dall'utente. Per altre informazioni, vedere [assegnare un accesso a identità gestite a una risorsa tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Aggiornare gli insiemi di credenziali delle chiavi

Questa sezione descrive i passaggi di base per aggiornare gli insiemi di credenziali delle chiavi. Per ulteriori informazioni, vedere [passaggio di un Azure Key Vault a un'altra sottoscrizione](../key-vault/general/move-subscription.md).

1. Aggiornare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione alla directory di destinazione.

1. Rimuovere tutte le voci dei criteri di accesso esistenti.

1. Aggiungere nuove voci di criteri di accesso associate alla directory di destinazione.

### <a name="update-acls"></a>Aggiornamenti ACL

1. Se si utilizza Azure Data Lake Storage Gen1, assegnare gli ACL appropriati. Per altre informazioni, vedere [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Se si utilizza Azure Data Lake Storage Gen2, assegnare gli ACL appropriati. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).

1. Se si utilizza File di Azure, assegnare gli ACL appropriati.

### <a name="review-other-security-methods"></a>Esaminare altri metodi di sicurezza

Anche se le assegnazioni di ruolo vengono rimosse durante il trasferimento, gli utenti nell'account proprietario originale potrebbero continuare ad accedere alla sottoscrizione tramite altri metodi di sicurezza, tra cui:

- Chiavi di accesso per servizi quali Archiviazione.
- [Certificati di gestione](../cloud-services/cloud-services-certs-create.md) che concedono all'amministratore utente l'accesso alle risorse della sottoscrizione.
- Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se si intende rimuovere l'accesso dagli utenti nella directory di origine in modo che non dispongano dell'accesso nella directory di destinazione, è necessario prendere in considerazione la rotazione delle credenziali. Fino a quando le credenziali non vengono aggiornate, gli utenti continueranno ad accedere dopo il trasferimento.

1. Ruotare le chiavi di accesso dell'account di archiviazione. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md).

1. Se si usano chiavi di accesso per altri servizi, ad esempio il database SQL di Azure o la messaggistica del bus di servizio di Azure, ruotare le chiavi di accesso.

1. Per le risorse che usano i segreti, aprire le impostazioni per la risorsa e aggiornare il segreto.

1. Per le risorse che usano certificati, aggiornare il certificato.

## <a name="next-steps"></a>Passaggi successivi

- [Trasferire la proprietà della fatturazione di una sottoscrizione di Azure in un altro account](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
