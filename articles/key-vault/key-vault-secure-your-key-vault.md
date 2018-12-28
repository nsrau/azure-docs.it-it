---
title: Proteggere Azure Key Vault | Microsoft Docs
description: Gestire le autorizzazioni di accesso per Azure Key Vault, chiavi e segreti. L'articolo riguarda il modello di autenticazione e autorizzazione per Key Vault e offre informazioni su come proteggere l'insieme di credenziali delle chiavi.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 67f24bbccdd2dcf5cca09e09557d7ebebd0a5c2d
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891079"
---
# <a name="secure-your-key-vault"></a>Proteggere l'insieme di credenziali delle chiavi
Azure Key Vault è un servizio cloud che consente di proteggere le chiavi di crittografia e i segreti (ad esempio certificati, stringhe di connessione e password). Poiché questi dati sono riservati e importanti per l'azienda, è opportuno proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di accesso a Key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso.

## <a name="overview"></a>Panoramica
L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce separate: piano di gestione e piano dati. 
**Piano di gestione**: riguarda la gestione dell'insieme di credenziali, ad esempio la sua creazione, l'aggiornamento e l'eliminazione. 
**Piano dati**: riguarda i segreti all'interno di un insieme di credenziali, ossia la creazione, l'aggiornamento, l'eliminazione e la lettura di un segreto contenuto nell'insieme di credenziali. Per entrambi i piani, sono richieste opportune procedure di autenticazione e autorizzazione prima che un chiamante (un utente o un'applicazione) possa ottenere l'accesso all'insieme di credenziali delle chiavi. L'autenticazione stabilisce l'identità del chiamante, mentre l'autorizzazione determina le operazioni che il chiamante è autorizzato a eseguire.

Per l'autenticazione, sia il piano di gestione sia il piano dati usano Azure Active Directory. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

Ecco una breve panoramica degli argomenti trattati:

[Autenticazione tramite Azure Active Directory](#authentication-using-azure-active-directory): questa sezione spiega come un chiamante esegue l'autenticazione con Azure Active Directory per accedere a un insieme di credenziali delle chiavi tramite il piano di gestione e il piano dati. 

Per l'autenticazione, entrambi i piani utilizzano Azure Active Directory (Azure AD). Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

## <a name="authenticate-by-using-azure-active-directory"></a>Autenticazione tramite Azure Active Directory
Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant della sottoscrizione di Azure Active Directory. Per accedere all'insieme di credenziali delle chiavi, tutti i chiamanti devono essere registrati in questo tenant ed eseguire l'autenticazione. Questo requisito è valido sia per il piano di gestione che per il piano dati. In entrambi i casi, un'applicazione può accedere a un insieme di credenziali delle chiavi in due modi:

* **accesso utente + app**: questa modalità viene usata per le applicazioni che accedono all'insieme di credenziali delle chiavi per conto di un utente connesso. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. È possibile concedere l'accesso agli utenti in due modi: 
  - Accesso all'insieme di credenziali delle chiavi da qualsiasi applicazione.
  - Accesso all'insieme di credenziali delle chiavi solo in caso di uso di un'applicazione specifica (definita identità composta).

* **accesso solo app**: questa modalità viene usata per le applicazioni eseguite come servizi daemon o processi in background. L'accesso all'insieme di credenziali delle chiavi viene concesso all'identità dell'applicazione.

In entrambi i casi, l'applicazione esegue l'autenticazione con Azure AD usando uno dei [metodi di autenticazione supportati](../active-directory/develop/authentication-scenarios.md) e acquisendo un token. Il metodo di autenticazione usato dipende dal tipo di applicazione. L'applicazione usa quindi questo token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Le richieste relative al piano di gestione vengono instradate attraverso un endpoint di Azure Resource Manager. Quando invece si accede al piano dati, l'applicazione interagisce direttamente con un endpoint dell'insieme di credenziali delle chiavi. Per altre informazioni, vedere il [flusso di autenticazione completo](../active-directory/develop/v1-protocols-oauth-code.md). 

Il nome della risorsa per cui l'applicazione richiede un token dipende dal piano a cui l'applicazione sta accedendo. Il nome della risorsa è un endpoint del piano di gestione o un endpoint del piano dati, a seconda dell'ambiente Azure. Per maggiori dettagli, vedere la tabella proposta in una sezione successiva di questo articolo.

Disporre di un unico meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

* Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
* Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
* Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

## <a name="the-management-plane-and-the-data-plane"></a>Piano di gestione e piano dati
Il piano di gestione consente di gestire l'insieme di credenziali delle chiavi. Sono incluse operazioni come la gestione degli attributi e l'impostazione dei criteri di accesso al piano dati. Il piano dati consente di aggiungere, eliminare, modificare e usare le chiavi, i segreti e i certificati archiviati nell'insieme di credenziali delle chiavi.

Le interfacce del piano di gestione e del piano dati sono accessibili tramite endpoint diversi, elencati nella tabella di seguito. La seconda colonna della tabella descrive i nomi DNS per questi endpoint in diversi ambienti di Azure. La terza colonna descrive le operazioni che è possibile eseguire da ogni piano di accesso. Ogni piano di accesso dispone anche di un proprio meccanismo di controllo dell'accesso. Il controllo di accesso per il piano di gestione è impostato tramite il controllo degli accessi in base al ruolo di Azure Resource Manager. Il controllo dell'accesso per il piano dati è impostato tramite i criteri di accesso dell'insieme di credenziali delle chiavi.

| Piano di accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo di accesso |
| --- | --- | --- | --- |
| Piano di gestione |**Globale:**<br> management.azure.com:443<br><br> **21Vianet per Azure Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 |Creare/leggere/aggiornare/eliminare l'insieme di credenziali delle chiavi <br> Impostare criteri di accesso per l'insieme di credenziali delle chiavi<br>Impostare tag per l'insieme di credenziali delle chiavi |Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati |**Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **21Vianet per Azure Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 |Per le chiavi: Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Per i segreti: Get, List, Set, Delete |Criteri di accesso dell'insieme di credenziali delle chiavi |

I controlli dell'accesso per il piano di gestione e il piano dati funzionano in maniera indipendente. Ad esempio, per concedere a un'applicazione l'accesso all'uso delle chiavi in un insieme di credenziali delle chiavi, è sufficiente concedere l'accesso al piano dati. L'accesso viene concesso tramite i criteri di accesso dell'insieme di credenziali delle chiavi. Al contrario, per un utente che deve leggere le proprietà e i tag dell'insieme di credenziali delle chiavi ma non accedere ai dati (chiavi, segreti o certificati), è sufficiente l'accesso al piano di gestione. L'accesso viene concesso assegnando l'accesso in lettura all'utente tramite il controllo degli accessi in base al ruolo.

## <a name="management-plane-access-control"></a>Controllo di accesso al piano di gestione
Il piano di gestione è costituito da operazioni che interessano l'insieme di credenziali delle chiavi, come:

- Creazione o eliminazione di un insieme di credenziali delle chiavi.
- Recupero di un elenco degli insiemi di credenziali di una sottoscrizione.
- Recupero delle proprietà degli insiemi di credenziali delle chiavi (ad esempio SKU e tag).
- Impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi, che controllano l'accesso di utenti e applicazioni a chiavi e segreti.

Il controllo di accesso al piano di gestione è basato sul controllo degli accessi in base al ruolo.  

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. A utenti, gruppi e applicazioni di questa directory può essere concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager. Questo approccio è definito controllo degli accessi in base al ruolo. Per gestire l'accesso è possibile usare il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Creare un insieme di credenziali delle chiavi in un gruppo di risorse e controllare l'accesso al piano di gestione tramite Azure Active Directory. È ad esempio possibile consentire agli utenti o a un gruppo di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse.

È possibile concedere l'accesso a utenti, gruppi e applicazioni in un ambito specifico assegnando i ruoli Controllo degli accessi in base al ruolo appropriati. Ad esempio, per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, si assegna all'utente un ruolo predefinito di "Collaboratore di Key Vault" in un ambito specifico. In questo caso, l'ambito può essere una sottoscrizione, un gruppo di risorse o un insieme di credenziali delle chiavi. Un ruolo assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e a tutte le risorse nell'ambito della sottoscrizione. Un ruolo assegnato a livello di gruppo di risorse si applica a tutte le risorse di tale gruppo. Un ruolo assegnato per una risorsa specifica si applica a tale risorsa. Esistono diversi ruoli predefiniti (vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md)). Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato.

> [!IMPORTANT]
> Se un utente dispone di autorizzazioni di collaboratore per un piano di gestione dell'insieme di credenziali delle chiavi, può accedere al piano dati impostando i criteri di accesso per l'insieme di credenziali delle chiavi. Pertanto, è consigliabile controllare attentamente chi ha accesso come collaboratore agli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.
> 
> 

## <a name="data-plane-access-control"></a>Controllo di accesso al piano dati
Le operazioni del piano dati dell'insieme di credenziali delle chiavi si applicano agli oggetti archiviati come chiavi, segreti e certificati. Le operazioni relative alle chiavi includono la creazione, l'importazione, l'aggiornamento, l'elencazione, il backup e il ripristino delle chiavi. Le operazioni di crittografia includono la firma, la verifica, la crittografia, la decrittografia, il wrapping, l'annullamento del wrapping, l'impostazione di tag e di altri attributi per le chiavi. Analogamente, le operazioni sui segreti includono il recupero, l'impostazione, l'elencazione e l'eliminazione.

L'accesso al piano dati viene concesso impostando criteri di accesso per un insieme di credenziali delle chiavi. Per essere in grado di impostare i criteri di accesso per un insieme di credenziali delle chiavi, un utente, un gruppo o un'applicazione deve disporre delle autorizzazioni di collaboratore per il piano di gestione relativo a tale insieme. È possibile concedere l'accesso a un utente, un gruppo o un'applicazione per eseguire operazioni specifiche relative a segreti o chiavi in un insieme di credenziali delle chiavi. Key Vault supporta fino a 1024 criteri di accesso per un insieme di credenziali delle chiavi. Per concedere a più utenti l'accesso al piano dati, creare un gruppo di sicurezza di Azure Active Directory e aggiungere utenti a tale gruppo.

### <a name="key-vault-access-policies"></a>Criteri di accesso di Key Vault
I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti e certificati. È ad esempio possibile concedere a un utente l'accesso alle chiavi, ma non le autorizzazioni per i segreti. Le autorizzazioni per accedere a chiavi, segreti o certificati vengono definite a livello di insieme di credenziali. I criteri di accesso di Key Vault non supportano autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifici. Per impostare i criteri di accesso per un insieme di credenziali delle chiavi è possibile usare il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi si applicano a livello di insieme di credenziali. Ad esempio, quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, potrà eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

Oltre a usare i criteri di accesso, è inoltre possibile limitare l'accesso al piano dati usando gli [endpoint di servizio della rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Configurare i [firewall e le regole della rete virtuale](key-vault-network-security.md) per un ulteriore livello di sicurezza.

## <a name="example"></a>Esempio
Si supponga di voler sviluppare un'applicazione che usa un certificato per SSL, il servizio Archiviazione di Azure per archiviare i dati e una chiave RSA a 2048 bit per le operazioni di firma. Immaginiamo che l'applicazione sia in esecuzione in una macchina virtuale di Azure (o un set di scalabilità di macchine virtuali). È possibile usare un insieme di credenziali delle chiavi per archiviare tutti i segreti dell'applicazione e il certificato bootstrap usato dall'applicazione per l'autenticazione con Azure Active Directory.

Ecco un riepilogo dei tipi di chiavi e segreti archiviati:

* **Certificato SSL**: usato per SSL.
* **Chiave di archiviazione**: per ottenere l'accesso all'account di archiviazione.
* **Chiave RSA a 2048 bit**: usata per le operazioni di firma.
* **Certificato bootstrap**: per eseguire l'autenticazione con Azure AD. Una volta concesso l'accesso, è possibile recuperare la chiave di archiviazione e usare la chiave RSA per la firma.

Si considerino ora le persone coinvolte nella gestione, nella distribuzione e nel controllo dell'applicazione. In questo esempio verranno usati tre ruoli.

* **Team responsabile della sicurezza**: si tratta in genere del personale IT con ruoli di responsabilità che si occupa della salvaguardia di segreti, ad esempio i certificati SSL, le chiavi RSA usate per la firma, le stringhe di connessione per i database e le chiavi dell'account di archiviazione.
* **Sviluppatori/operatori**: le persone che sviluppano l'applicazione e la distribuiscono in Azure. In genere, non fanno parte del team responsabile della sicurezza e di conseguenza non devono avere accesso ai dati sensibili, come i certificati SSL e le chiavi RSA. Solo l'applicazione che distribuiscono deve avere accesso a tali oggetti.
* **Revisori**: si tratta in genere di un insieme diverso di persone, distinte dagli sviluppatori e dal personale IT generale, che hanno il compito di verificare l'uso e la gestione di certificati, chiavi e segreti per garantire la conformità agli standard di sicurezza. 

Esiste anche un altro ruolo che non rientra nell'ambito di questa applicazione, ma che è importante menzionare: l'amministratore della sottoscrizione (o del gruppo di risorse). L'amministratore della sottoscrizione imposta le autorizzazioni di accesso iniziali per il team responsabile della sicurezza. L'amministratore della sottoscrizione concede l'accesso al team responsabile della sicurezza tramite un gruppo di risorse che contiene quelle necessarie per l'applicazione.

Ora verranno esaminate le azioni eseguite da ogni ruolo nel contesto di questa applicazione.

* **Team responsabile della sicurezza**
  * Crea insiemi di credenziali delle chiavi.
  * Attiva la registrazione degli insiemi di credenziali delle chiavi.
  * Aggiunge chiavi o segreti.
  * Crea una copia di backup delle chiavi per il ripristino di emergenza.
  * Imposta i criteri di accesso dell'insieme di credenziali delle chiavi per concedere a utenti e applicazioni l'autorizzazione per eseguire operazioni specifiche.
  * Distribuisce periodicamente chiavi o segreti.
* **Sviluppatori/operatori**
  * Ottengono riferimenti a certificati bootstrap e SSL (identificazioni personali), alla chiave di archiviazione (URI del segreto) e alla chiave di firma (URI della chiave) dal team responsabile della sicurezza.
  * Sviluppano e distribuiscono l'applicazione che accede a chiavi e segreti a livello di codice.
* **Revisori**
  * Esaminano i log di utilizzo per verificare che la chiave o il segreto sia usato correttamente e sia conforme agli standard di sicurezza dei dati.

Ora verranno esaminate le autorizzazioni di accesso richieste da ogni ruolo e dall'applicazione per eseguire le attività assegnate. 

| Ruolo utente | Autorizzazioni del piano di gestione | Autorizzazioni del piano dati |
| --- | --- | --- |
| Team responsabile della sicurezza |Collaboratore di Key Vault |Chiavi: backup, create, delete, get, import, list, restore <br> Segreti: all |
| Sviluppatori/operatori |Autorizzazione alla distribuzione dell'insieme di credenziali delle chiavi per consentire alle macchine virtuali distribuite di recuperare i segreti da tale insieme. |Nessuna |
| Revisori |Nessuna |Chiavi: list<br>Segreti: list |
| Applicazione |Nessuna |Chiavi: sign<br>Segreti: get |

> [!NOTE]
> I revisori devono disporre dell'autorizzazione list per le chiavi e i segreti in modo da poter esaminare gli attributi di chiavi e segreti che non vengono riportati nei log. Questi attributi includono tag, attivazione e date di scadenza.
> 
> 

Oltre alle autorizzazioni per l'insieme di credenziali delle chiavi, tutti e tre i ruoli devono poter accedere anche ad altre risorse, per essere ad esempio in grado di distribuire le macchine virtuali (o la funzionalità App Web del Servizio App di Azure). Per questi tipi di risorse, sviluppatori e operatori necessitano inoltre dell'accesso come collaboratore. Per i revisori è necessario l'accesso in lettura all'account di archiviazione in cui vengono archiviati i log dell'insieme di credenziali delle chiavi.

Poiché l'obiettivo di questo articolo è la protezione dell'accesso all'insieme di credenziali delle chiavi, verranno illustrati solo i concetti relativi a tale argomento. I dettagli relativi alla distribuzione dei certificati e all'accesso a chiavi e segreti a livello di codice sono trattati in altri documenti. Ad esempio:

- La distribuzione nelle macchine virtuali dei certificati archiviati in Key Vault è descritta in [Distribuire i certificati nelle macchine virtuali da un insieme di credenziali delle chiavi gestito dal cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (post di blog).
- Il [download degli esempi del client per Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) illustra come usare un certificato bootstrap per eseguire l'autenticazione con Azure AD per l'accesso a un insieme di credenziali delle chiavi.

È possibile concedere la maggior parte delle autorizzazioni di accesso tramite il portale di Azure. Per concedere autorizzazioni granulari, può essere necessario usare Azure PowerShell o l'interfaccia della riga di comando per ottenere il risultato desiderato. 

I frammenti di codice di PowerShell seguenti presuppongono quanto segue:

* L'amministratore di Azure Active Directory ha creato gruppi di sicurezza che rappresentano i tre ruoli: team responsabile della sicurezza di Contoso, DevOps dell'app di Contoso e revisori dell'app di Contoso. L'amministratore ha inoltre aggiunto utenti ai gruppi di appartenenza.
* **ContosoAppRG** è il gruppo in cui si trovano tutte le risorse. **contosologstorage** è l'area in cui vengono archiviati i log. 
* L'insieme di credenziali delle chiavi **ContosoKeyVault** e l'account di archiviazione usato per i log dell'insieme di credenziali delle chiavi **contosologstorage** devono trovarsi nello stesso percorso di Azure.

Innanzitutto l'amministratore della sottoscrizione assegna i ruoli `key vault Contributor` e `User Access Administrator` al team responsabile della sicurezza. In questo modo il team può gestire l'accesso ad altre risorse e gli insiemi di credenziali delle chiavi nel gruppo di risorse ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Lo script seguente mostra in che modo il team responsabile della sicurezza può creare un insieme di credenziali delle chiavi e impostare la registrazione e le autorizzazioni di accesso. Per informazioni dettagliate sulle autorizzazioni dei criteri di accesso dell'insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati di Azure Key Vault](about-keys-secrets-and-certificates.md).

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Il ruolo personalizzato definito può essere assegnato solo alla sottoscrizione in cui viene creato il gruppo di risorse `ContosoAppRG`. Se gli stessi ruoli personalizzati verranno usati per altri progetti in altre sottoscrizioni, è possibile che all'ambito siano associate più sottoscrizioni.

L'assegnazione di ruoli personalizzati per l'autorizzazione "deploy/action" per sviluppatori e operatori rientra nell'ambito del gruppo di risorse. In questo modo, solo le macchine virtuali create nel gruppo di risorse `ContosoAppRG` avranno accesso ai segreti (certificati SSL e bootstrap). Le macchine virtuali create in un altro gruppo di risorse da un membro del team di sviluppatori e operatori non avranno accesso a questi segreti, anche se dispongono dei relativi URI.

Questo esempio illustra uno scenario semplice. Gli scenari reali possono essere più complessi e in alcuni casi può essere necessario modificare le autorizzazioni per l'insieme di credenziali delle chiavi in base alle esigenze. In questo esempio si presuppone che il team responsabile della sicurezza fornisca i riferimenti a chiavi e segreti (URI e identificazioni personali) richiesti da sviluppatori e operatori per inserire riferimenti nelle proprie applicazioni. Sviluppatori e operatori non necessitano di alcun accesso al piano dati. Questo esempio riguarda principalmente la sicurezza dell'insieme di credenziali delle chiavi. Considerazioni simili sono necessarie per proteggere [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/security/), [account di archiviazione](../storage/common/storage-security-guide.md) e altre risorse di Azure.

> [!NOTE]
> Questo esempio mostra come l'accesso all'insieme di credenziali delle chiavi verrà bloccato nell'ambiente di produzione. Gli sviluppatori devono quindi avere una sottoscrizione o un gruppo di risorse personali su cui dispongono di autorizzazioni complete per gestire gli insiemi di credenziali delle chiavi, le macchine virtuali e l'account di archiviazione in cui sviluppano l'applicazione.

È altamente consigliabile proteggere ulteriormente l'accesso all'insieme di credenziali delle chiavi mediante la [configurazione di firewall e reti virtuali di Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Risorse
* [Controllo degli accessi in base al ruolo di Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
* [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md)
  
* [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
* [Gestione del controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
  
* [Controllo degli accessi in base al ruolo per Microsoft Azure da Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Questo video tratto dalla conferenza Microsoft Ignite 2015 illustra le funzionalità di Azure per la gestione dell'accesso e la creazione di report. Descrive anche le procedure consigliate per la protezione dell'accesso alle sottoscrizioni di Azure tramite Azure Active Directory.
* [Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [API REST di gestione di Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Questo documento include informazioni di riferimento sulle API REST per gestire l'insieme di credenziali delle chiavi a livello di codice, inclusa l'impostazione dei criteri di accesso di Key Vault.
* [API REST di Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Key access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl) (Controllo di accesso per le chiavi)
  
* [Secret access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl) (Controllo di accesso per i segreti)
  
* [Impostare](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) e [rimuovere](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) i criteri di accesso dell'insieme di credenziali delle chiavi usando PowerShell
  
## <a name="next-steps"></a>Passaggi successivi
[Configurare reti virtuali e firewall di Key Vault](key-vault-network-security.md)

Per un'esercitazione introduttiva per gli amministratori, vedere [Introduzione all'insieme di credenziali chiave di Azure](key-vault-get-started.md).

Per altre informazioni sulla registrazione dell'utilizzo per l'insieme di credenziali delle chiavi, vedere [Registrazione dell'insieme di credenziali delle chiavi di Azure](key-vault-logging.md).

Per altre informazioni sull'uso di chiavi e segreti con Azure Key Vault, vedere [Informazioni su chiavi e segreti](https://msdn.microsoft.com/library/azure/dn903623.aspx).

In caso di domande su Key Vault, visitare i [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

