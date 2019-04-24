---
title: Proteggere l'accesso a un insieme di credenziali delle chiavi - Azure Key Vault | Microsoft Docs
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 20c58647b8a6283de4ca2b90c830fe54db927095
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304949"
---
# <a name="secure-access-to-a-key-vault"></a>Proteggere l'accesso a un insieme di credenziali delle chiavi

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di accesso a Key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce: il **piano di gestione** e il **piano dati**. Il piano di gestione consente di gestire l'insieme di credenziali delle chiavi stesso. Le operazioni in questo piano includono la creazione e l'eliminazione di insiemi di credenziali delle chiavi, il recupero delle proprietà dell'insieme di credenziali delle chiavi e l'aggiornamento dei criteri di accesso. Il piano dati consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Per accedere a un insieme di credenziali delle chiavi in uno dei piani, tutti i chiamanti (utenti o applicazioni) devono disporre di autenticazione e autorizzazione appropriate. L'autenticazione stabilisce l'identità del chiamante. L'autorizzazione determina le operazioni che il chiamante può eseguire. 

Entrambi i piani usano Azure Active Directory (Azure AD) per l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

## <a name="active-directory-authentication"></a>Autenticazione di Active Directory

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Tutti i chiamanti in entrambi i piani devono essere registrati in questo tenant ed eseguire l'autenticazione per accedere all'insieme di credenziali delle chiavi. In entrambi i casi, le applicazioni possono accedere a un insieme di credenziali delle chiavi in due modi:

- **Accesso utente più applicazione**: l'applicazione accede all'insieme di credenziali delle chiavi per conto di un utente connesso. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. L'accesso utente viene concesso in due modi. Gli utenti possono accedere all'insieme di credenziali delle chiavi da un'applicazione oppure devono usare un'applicazione specifica (definita _identità composta_).
- **Accesso solo dell'applicazione**: l'applicazione viene eseguita come servizio daemon o processo in background. All'identità di applicazione viene concesso l'accesso all'insieme di credenziali delle chiavi.

Per entrambi i tipi di accesso, l'applicazione esegue l'autenticazione con Azure AD. L'applicazione usa qualsiasi [metodo di autenticazione supportato](../active-directory/develop/authentication-scenarios.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa del piano per la concessione dell'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Per altre informazioni, vedere l'[intero flusso di autenticazione](../active-directory/develop/v1-protocols-oauth-code.md).

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

## <a name="resource-endpoints"></a>Endpoint delle risorse

Le applicazioni accedono ai piani tramite endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per l'uso delle chiavi in un insieme di credenziali delle chiavi, concedere l'accesso al piano dati usando i criteri di accesso dell'insieme di credenziali delle chiavi. Per concedere a un utente l'accesso in lettura alle proprietà e ai tag dell'insieme di credenziali delle chiavi, ma non ai dati (chiavi, segreti o certificati), concedere l'accesso al piano di gestione con il controllo degli accessi in base al ruolo.

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo&nbsp;di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br><br> **21Vianet per Azure Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 | Creare, leggere, aggiornare ed eliminare insiemi di credenziali delle chiavi<br><br>Impostare i criteri di accesso dell'insieme di credenziali delle chiavi<br><br>Impostare tag per l'insieme di credenziali delle chiavi | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati | **Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **21Vianet per Azure Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 | Chiavi: decrittografare, crittografare,<br> annullare il wrapping, eseguire il wrapping, verificare, firmare,<br> ottenere, elencare, aggiornare, creare,<br> importare, eliminare, eseguire il backup, ripristinare<br><br> Segreti: ottenere, elencare, impostare, eliminare | Criteri di accesso dell'insieme di credenziali delle chiavi |

## <a name="management-plane-and-rbac"></a>Piano di gestione e controllo degli accessi in base al ruolo

Nel piano di gestione, è utilizzare RBAC (controllo di accesso basato su ruolo) per autorizzare le operazioni di che un chiamante può eseguire. Nel modello di controllo degli accessi in base al ruolo ogni sottoscrizione di Azure ha un'istanza di Azure AD. È possibile concedere l'accesso a utenti, gruppi e applicazioni da questa directory. Viene concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager. Per concedere l'accesso, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Creare un insieme di credenziali delle chiavi in un gruppo di risorse e gestire l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un livello di ambito specifico assegnando i ruoli Controllo degli accessi in base al ruolo appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. A un ruolo Controllo degli accessi in base al ruolo è possibile assegnare i livelli di ambiti seguenti:

- **Sottoscrizione** Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e a tutte le risorse in tale sottoscrizione.
- **Gruppo di risorse**: Un ruolo Controllo degli accessi in base al ruolo assegnato a livello di gruppo di risorse si applica a tutte le risorse di tale gruppo.
- **Risorsa specifica**: Un ruolo Controllo degli accessi in base al ruolo assegnato per una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Piano dati e criteri di accesso

L'accesso al piano dati viene concesso impostando i criteri di accesso di Key Vault per un insieme di credenziali delle chiavi. Per impostare i criteri di accesso, un utente, un gruppo o un'applicazione deve avere le autorizzazioni `Contributor` per il piano di gestione per l'insieme di credenziali delle chiavi.

È possibile concedere l'accesso a un utente, un gruppo o un'applicazione per eseguire operazioni specifiche relative a segreti o chiavi in un insieme di credenziali delle chiavi. Key Vault supporta fino a 1.024 voci di criteri di accesso per un insieme di credenziali delle chiavi. Per concedere a più utenti l'accesso al piano dati, creare un gruppo di sicurezza di Azure AD e aggiungere gli utenti a tale gruppo.

<a id="key-vault-access-policies"></a> I criteri di accesso dell'insieme di credenziali delle chiavi concedono autorizzazioni separate per chiavi, segreti e certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati vengono definite a livello di insieme di credenziali. I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Per impostare i criteri di accesso per un insieme di credenziali delle chiavi, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi si applicano a livello di insieme di credenziali. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.
>

È possibile limitare l'accesso al piano dati usando gli [endpoint di servizio della rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole della rete virtuale](key-vault-network-security.md) per un ulteriore livello di sicurezza.

## <a name="example"></a>Esempio

In questo esempio si sviluppa un'applicazione che usa un certificato per SSL, il servizio Archiviazione di Azure per archiviare i dati e una chiave RSA a 2.048 bit per le operazioni di firma. L'applicazione viene eseguita in una macchina virtuale di Azure (o un set di scalabilità di macchine virtuali). È possibile usare un insieme di credenziali delle chiavi per archiviare i segreti dell'applicazione. È possibile archiviare il certificato bootstrap usato dall'applicazione per l'autenticazione con Azure AD.

È necessario l'accesso alle chiavi e ai segreti seguenti:
- **Certificato SSL**: usato per SSL.
- **Chiave di archiviazione**: usata per accedere all'account di archiviazione.
- **Chiave RSA a 2.048 bit**: usata per le operazioni di firma.
- **Certificato bootstrap**: per eseguire l'autenticazione con Azure AD. Una volta concesso l'accesso, è possibile recuperare la chiave di archiviazione e usare la chiave RSA per la firma.

È necessario definire i ruoli seguenti per specificare chi può gestire, distribuire e controllare l'applicazione:
- **Team responsabile della sicurezza**: personale IT che lavora nell'ufficio del responsabile della sicurezza o collaboratori analoghi. Il team responsabile della sicurezza si occupa della salvaguardia di segreti. I segreti possono includere certificati SSL, chiavi RSA usate per la firma, stringhe di connessione e chiavi degli account di archiviazione.
- **Sviluppatori e operatori**: il personale che sviluppa l'applicazione e la distribuisce in Azure. I membri di questo team non fanno parte del personale responsabile della sicurezza. Non devono avere accesso ai dati sensibili, come i certificati SSL e le chiavi RSA. Solo l'applicazione da loro distribuita deve avere accesso ai dati sensibili.
- **Revisori**: questo ruolo è per i collaboratori che non sono membri del personale IT generico o di sviluppo. Queste persone verificano l'uso e la gestione di certificati, chiavi e segreti per garantire la conformità agli standard di sicurezza. 

C'è un altro ruolo che non rientra nell'ambito dell'applicazione: l'amministratore della sottoscrizione (o del gruppo di risorse). L'amministratore della sottoscrizione configura le autorizzazioni di accesso iniziali per il team responsabile della sicurezza. L'accesso al team responsabile della sicurezza viene concesso tramite un gruppo di risorse che contiene le risorse necessarie per l'applicazione.

È necessario autorizzare le operazioni seguenti per i ruoli:

**Team responsabile della sicurezza**
- Creare insiemi di credenziali delle chiavi.
- Attivare la registrazione per gli insiemi di credenziali delle chiavi.
- Aggiungere chiavi e segreti.
- Creare i backup delle chiavi per il ripristino di emergenza.
- Impostare i criteri di accesso dell'insieme di credenziali delle chiavi per concedere a utenti e applicazioni le autorizzazioni per eseguire operazioni specifiche.
- Aggiornare periodicamente chiavi e segreti.

**Sviluppatori e operatori**
- Ottenere riferimenti per i certificati bootstrap e SSL (identificazioni personali), la chiave di archiviazione (URI del segreto) e la chiave RSA (URI della chiave) per la firma dal team responsabile della sicurezza.
- Sviluppare e distribuire l'applicazione per l'accesso a chiavi e segreti a livello di codice.

**Revisori**
- Esaminare i log di Key Vault per verificare che chiavi e segreti siano usati in modo appropriato e conformemente agli standard di sicurezza dei dati.

Nella tabella seguente sono riepilogate le autorizzazioni di accesso per i ruoli e l'applicazione. 

| Ruolo | Autorizzazioni del piano di gestione | Autorizzazioni del piano dati |
| --- | --- | --- |
| Team responsabile della sicurezza | Collaboratore di Key Vault | Chiavi: backup, create, delete, get, import, list, restore<br>Segreti: tutte le operazioni |
| Sviluppatori e&nbsp;operatori | Autorizzazione di distribuzione dell'insieme di credenziali delle chiavi<br><br> **Nota**: Questa autorizzazione consente alle macchine virtuali distribuite di recuperare i segreti da un insieme di credenziali delle chiavi. | Nessuna |
| Revisori | Nessuna | Chiavi: list<br>Segreti: list<br><br> **Nota**: Questa autorizzazione consente ai revisori di esaminare gli attributi (tag e date di attivazione e scadenza) per le chiavi e i segreti che non vengono riportati nei log. |
| Applicazione | Nessuna | Chiavi: sign<br>Segreti: get |

Oltre alle autorizzazioni per l'insieme di credenziali delle chiavi, i tre i ruoli dei team devono poter accedere ad altre risorse. Per distribuire le macchine virtuali (o la funzionalità App Web di Servizio app di Azure), sviluppatori e operatori necessitano dell'accesso `Contributor` a tali tipi di risorse. I revisori necessitano dell'accesso in lettura all'account di archiviazione in cui vengono archiviati i log dell'insieme di credenziali delle chiavi.

Per altre informazioni su come distribuire certificati, chiavi di accesso e segreti a livello di codice, vedere queste risorse:
- Vedere il post di blog su come [distribuire i certificati nelle macchine virtuali da un insieme di credenziali delle chiavi gestito dal cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).
- Scaricare gli [esempi client di Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Questo contenuto illustra come usare un certificato bootstrap per eseguire l'autenticazione con Azure AD per l'accesso a un insieme di credenziali delle chiavi.

È possibile concedere la maggior parte delle autorizzazioni di accesso tramite il portale di Azure. Per concedere autorizzazioni granulari, è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

I frammenti di codice di PowerShell in questa sezione sono creati basandosi sui presupposti seguenti:
- L'amministratore di Azure AD ha creato i gruppi di sicurezza per rappresentare i tre ruoli: Contoso Security Team, Contoso App DevOps e Contoso App Auditors. L'amministratore ha aggiunto gli utenti ai rispettivi gruppi.
- Tutte le risorse si trovano nel gruppo di risorse **ContosoAppRG**.
- I log dell'insieme di credenziali delle chiavi sono archiviati nell'account di archiviazione **contosologstorage**. 
- L'insieme di credenziali delle chiavi **ContosoKeyVault** e l'account di archiviazione **contosologstorage** si trovano nello stesso percorso di Azure.

L'amministratore della sottoscrizione assegna i ruoli `key vault Contributor` e `User Access Administrator` al team responsabile della sicurezza. Questi ruoli permettono al team responsabile della sicurezza di gestire l'accesso ad altre risorse e insiemi di credenziali delle chiavi, entrambi nel gruppo di risorse **ContosoAppRG**.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Il team responsabile della sicurezza crea un insieme di credenziali delle chiavi e configura la registrazione e le autorizzazioni di accesso. Per informazioni dettagliate sulle autorizzazioni dei criteri di accesso dell'insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati di Azure Key Vault](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

I ruoli personalizzati definiti possono essere assegnati solo alla sottoscrizione in cui viene creato il gruppo di risorse **ContosoAppRG**. Per usare un ruolo personalizzato per altri progetti in altre sottoscrizioni, aggiungere altre sottoscrizioni all'ambito per il ruolo.

Per il personale DevOps, l'assegnazione del ruolo personalizzato per l'autorizzazione `deploy/action` per l'insieme di credenziali delle chiavi ha come ambito il gruppo di risorse. Solo le macchine virtuali create nel gruppo di risorse **ContosoAppRG** possono accedere ai segreti (certificati SSL e bootstrap). Le macchine virtuali create in altri gruppi di risorse da un membro del team DevOps non possono accedere a questi segreti, anche se dispongono dei relativi URI.

Questo esempio illustra uno scenario semplice. Gli scenari reali possono essere più complessi. È possibile modificare le autorizzazioni per l'insieme di credenziali delle chiavi in base alle esigenze. In questo esempio si presuppone che il team responsabile della sicurezza fornisca i riferimenti a chiavi e segreti (URI e identificazioni personali) usati dal personale DevOps nelle applicazioni. Sviluppatori e operatori non necessitano di alcun accesso al piano dati. In questo articolo è stato analizzato in particolare come proteggere l'insieme di credenziali delle chiavi. Tenere presenti considerazioni simili per la protezione di [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/security/), [account di archiviazione](../storage/common/storage-security-guide.md) e altre risorse di Azure.

> [!NOTE]
> Questo esempio mostra come l'accesso all'insieme di credenziali delle chiavi viene bloccato nell'ambiente di produzione. Gli sviluppatori devono avere una sottoscrizione o un gruppo di risorse personale su cui dispongono di autorizzazioni complete per gestire gli insiemi di credenziali delle chiavi, le macchine virtuali e l'account di archiviazione in cui sviluppano l'applicazione.

È consigliabile proteggere ulteriormente l'accesso all'insieme di credenziali delle chiavi mediante la [configurazione di firewall e reti virtuali di Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Risorse

* [Controllo degli accessi in base al ruolo di Azure AD](../role-based-access-control/role-assignments-portal.md)

* [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md)

* [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC for Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707) (Controllo degli accessi in base al ruolo per Microsoft Azure)

    Questo video tratto dalla conferenza Microsoft Ignite 2015 illustra le funzionalità di Azure per la gestione dell'accesso e la creazione di report. Descrive anche le procedure consigliate per la protezione dell'accesso alle sottoscrizioni di Azure tramite Azure Active Directory.

* [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../active-directory/develop/v1-protocols-oauth-code.md)

* [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Informazioni di riferimento sulle API REST per gestire l'insieme di credenziali delle chiavi a livello di codice, inclusa l'impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi.

* [API REST di Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Key access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl) (Controllo di accesso per le chiavi)
  
* [Secret access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl) (Controllo di accesso per i segreti)
  
* [Impostare](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [rimuovere](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) i criteri di accesso dell'insieme di credenziali delle chiavi tramite PowerShell.
  
## <a name="next-steps"></a>Passaggi successivi

Configurare [firewall e reti virtuali di Key Vault](key-vault-network-security.md).

Per un'esercitazione introduttiva per gli amministratori, vedere [Cos'è Azure Key Vault?](key-vault-overview.md).

Per altre informazioni sulla registrazione dell'utilizzo per l'insieme di credenziali delle chiavi, vedere [Registrazione dell'insieme di credenziali delle chiavi di Azure](key-vault-logging.md).

Per altre informazioni sull'uso di chiavi e segreti con Azure Key Vault, vedere [Informazioni su chiavi e segreti](https://msdn.microsoft.com/library/azure/dn903623.aspx).

In caso di domande su Key Vault, visitare i [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
