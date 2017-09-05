---
title: Proteggere l'insieme di credenziali delle chiavi | Documentazione Microsoft
description: Gestire le autorizzazioni di accesso per un insieme di credenziali delle chiavi per controllare insiemi di credenziali, chiavi e segreti. Modello di autenticazione e autorizzazione per l'insieme di credenziali delle chiavi e istruzioni per proteggere l'insieme di credenziali delle chiavi
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b81791f0bce7e6f57782dfe7bc5fb5fc21369e7d
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="secure-your-key-vault"></a>Proteggere l'insieme di credenziali delle chiavi
Insieme di credenziali delle chiavi di Azure è un servizio cloud che consente di proteggere le chiavi di crittografia e i segreti (ad esempio, certificati, stringhe di connessione e password) per le applicazioni cloud. Poiché questi dati sono riservati e importanti per l'azienda, è opportuno limitare l'accesso agli insiemi di credenziali delle chiavi alle applicazioni e agli utenti autorizzati. Questo articolo offre una panoramica del modello di accesso all'insieme di credenziali delle chiavi, illustra i criteri di autenticazione e autorizzazione e spiega come proteggere l'accesso all'insieme di credenziali delle chiavi per le applicazioni cloud presentando un esempio.

## <a name="overview"></a>Panoramica
L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce separate: piano di gestione e piano dati. Per entrambi i piani, sono richieste opportune procedure di autenticazione e autorizzazione prima che un chiamante (un utente o un'applicazione) possa ottenere l'accesso all'insieme di credenziali delle chiavi. L'autenticazione stabilisce l'identità del chiamante, mentre l'autorizzazione determina le operazioni che il chiamante è autorizzato a eseguire.

Per l'autenticazione, sia il piano di gestione sia il piano dati usano Azure Active Directory. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo, mentre il piano dati usa i criteri di accesso dell'insieme di credenziali delle chiavi.

Ecco una breve panoramica degli argomenti trattati:

[Autenticazione tramite Azure Active Directory](#authentication-using-azure-active-directory): questa sezione spiega come un chiamante esegue l'autenticazione con Azure Active Directory per accedere a un insieme di credenziali delle chiavi tramite il piano di gestione e il piano dati. 

[Piano di gestione e piano dati](#management-plane-and-data-plane): questi sono i due piani usati per accedere all'insieme di credenziali delle chiavi. Ogni piano di accesso supporta operazioni specifiche. Questa sezione descrive gli endpoint di accesso, le operazioni supportate e il metodo di controllo di accesso usato da ogni piano. 

[Controllo di accesso al piano di gestione](#management-plane-access-control): questa sezione illustra come consentire l'accesso alle operazioni del piano di gestione tramite il controllo degli accessi in base al ruolo.

[Controllo di accesso al piano dati](#data-plane-access-control): questa sezione descrive come usare i criteri di accesso dell'insieme di credenziali delle chiavi per controllare l'accesso al piano dati.

[Esempio](#example): questo esempio spiega come configurare il controllo di accesso per l'insieme di credenziali delle chiavi in modo da consentire a tre diversi team (team responsabile della sicurezza, sviluppatori/operatori e revisori) di eseguire attività specifiche per sviluppare, gestire e monitorare un'applicazione in Azure.

## <a name="authentication-using-azure-active-directory"></a>Autenticazione tramite Azure Active Directory
Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure Active Directory relativo alla sottoscrizione. Per accedere all'insieme di credenziali delle chiavi, tutti i chiamanti (utenti e applicazioni) devono essere registrati in questo tenant e inoltre un'applicazione o un utente deve autenticarsi con Azure Active Directory. Queste considerazioni sono valide sia per il piano di gestione sia per il piano dati. In entrambi i casi, un'applicazione può accedere a un insieme di credenziali delle chiavi in due modi:

* **accesso utente+app**: in genere questo meccanismo viene usato per le applicazioni che accedono all'insieme di credenziali delle chiavi per conto di un utente connesso. Azure PowerShell e il portale di Azure offrono un esempio di questo tipo di accesso. Esistono due modi per concedere l'accesso agli utenti: gli utenti possono accedere all'insieme di credenziali delle chiavi da qualsiasi applicazione oppure solo da un'applicazione specifica, definita identità composta. 
* **accesso solo app**: questo meccanismo viene usato per le applicazioni che eseguono servizi daemon, processi in background e così via. L'accesso all'insieme di credenziali delle chiavi viene concesso all'identità dell'applicazione.

In entrambi i casi, l'applicazione esegue l'autenticazione con Azure Active Directory usando uno dei [metodi di autenticazione supportati](../active-directory/active-directory-authentication-scenarios.md) e acquisisce un token. Il metodo di autenticazione usato dipende dal tipo di applicazione. L'applicazione usa quindi questo token e invia una richiesta API REST all'insieme di credenziali delle chiavi. In caso di accesso al piano di gestione, le richieste vengono instradate attraverso l'endpoint di Azure Resource Manager. Quando invece si accede al piano dati, le applicazioni interagiscono direttamente con un endpoint dell'insieme di credenziali delle chiavi. Sono disponibili informazioni più dettagliate sull'[intero flusso di autenticazione](../active-directory/active-directory-protocols-oauth-code.md). 

Il nome della risorsa per cui l'applicazione richiede un token è diverso a seconda che l'applicazione acceda al piano di gestione o al piano dati. Di conseguenza, il nome della risorsa corrisponde all'endpoint del piano di gestione o a quello del piano dati descritto nella tabella di una sezione successiva, a seconda dell'ambiente di Azure.

L'uso di un solo meccanismo per l'autenticazione a entrambi i piani presenta specifici vantaggi:

* Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
* Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
* Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure Active Directory, ad esempio abilitando l'autenticazione a più fattori per maggiore sicurezza.

## <a name="management-plane-and-data-plane"></a>Piano di gestione e piano dati
Insieme di credenziali delle chiavi è un servizio di Azure disponibile tramite il modello di distribuzione Azure Resource Manager. Quando si crea un insieme di credenziali delle chiavi, si ottiene un contenitore virtuale nel quale è possibile creare altri oggetti come chiavi, segreti e certificati. Si accede quindi all'insieme di credenziali delle chiavi usando il piano di gestione e il piano dati per eseguire operazioni specifiche. L'interfaccia del piano di gestione viene usata per gestire l'insieme di credenziali delle chiavi, ad esempio per creare, eliminare e aggiornare gli attributi dell'insieme e impostare criteri di accesso per il piano dati. L'interfaccia del piano dati viene usata per aggiungere, eliminare, modificare e usare le chiavi, i segreti e i certificati archiviati nell'insieme di credenziali delle chiavi.

Le due interfacce sono accessibili tramite endpoint diversi (vedere la tabella). La seconda colonna della tabella indica i nomi DNS per questi endpoint in diversi ambienti di Azure. La terza colonna descrive le operazioni che è possibile eseguire da ogni piano di accesso. Ognuno dei piani ha anche uno specifico meccanismo di controllo di accesso: per il piano di gestione viene usato il controllo degli accessi in base al ruolo di Azure Resource Manager, mentre per il piano dati vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi.

| Piano di accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo di accesso |
| --- | --- | --- | --- |
| Piano di gestione |**Globale:**<br> management.azure.com:443<br><br> **Azure per la Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 |Creare/Leggere/Aggiornare/Eliminare l'insieme di credenziali delle chiavi <br> Impostare criteri di accesso per l'insieme di credenziali delle chiavi<br>Impostare tag per l'insieme di credenziali delle chiavi |Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati |**Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **Azure per la Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 |Per le chiavi: Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Per i segreti: Get, List, Set, Delete |Criteri di accesso dell'insieme di credenziali delle chiavi |

I controlli di accesso al piano di gestione e al piano dati funzionano in maniera indipendente. Se ad esempio si vuole concedere l'accesso a un'applicazione per l'uso di chiavi in un insieme di credenziali delle chiavi, è necessario concedere solo autorizzazioni di accesso al piano dati tramite criteri di accesso dell'insieme di credenziali delle chiavi. Non sono necessarie autorizzazioni di accesso al piano di gestione per l'applicazione. Al contrario, se si vuole che un utente sia in grado di leggere i tag e le proprietà dell'insieme di credenziali, senza avere accesso a chiavi, segreti o certificati, è possibile concedere all'utente l'accesso "read" usando il controllo degli accessi in base al ruolo e non è necessario l'accesso al piano dati.

## <a name="management-plane-access-control"></a>Controllo di accesso al piano di gestione
Il piano di gestione è costituito da operazioni che interessano l'insieme di credenziali delle chiavi. È ad esempio possibile creare o eliminare un insieme di credenziali, ottenere l'elenco degli insiemi di credenziali di una sottoscrizione, recuperare le proprietà dell'insieme (come SKU e tag), nonché impostare i criteri di accesso che controllano le applicazioni e gli utenti autorizzati ad accedere alle chiavi e ai segreti nell'insieme di credenziali delle chiavi. Il controllo di accesso al piano di gestione è basato sul controllo degli accessi in base al ruolo. Per l'elenco completo delle operazioni relative all'insieme di credenziali delle chiavi che possono essere eseguite tramite il piano di gestione, vedere la tabella nella sezione precedente. 

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. A utenti, gruppi e applicazioni di questa directory può essere concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager. Questo approccio è definito controllo degli accessi in base al ruolo. Per gestire l'accesso è possibile usare il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia della riga di comando di Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Con il modello di Azure Resource Manager si crea l'insieme di credenziali delle chiavi in un gruppo di risorse e si controlla l'accesso al piano di gestione dell'insieme di credenziali delle chiavi usando Azure Active Directory. È ad esempio possibile consentire agli utenti o a un gruppo di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse specifico.

È possibile concedere l'accesso a utenti, gruppi e applicazioni in un ambito specifico assegnando i ruoli Controllo degli accessi in base al ruolo appropriati. Ad esempio, per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, si assegna all'utente un ruolo predefinito "key vault Contributor" in un ambito specifico. In questo caso, l'ambito può essere una sottoscrizione, un gruppo di risorse o semplicemente un insieme di credenziali delle chiavi. Un ruolo assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e a tutte le risorse nell'ambito della sottoscrizione. Un ruolo assegnato a livello di gruppo di risorse si applica a tutte le risorse di tale gruppo. Un ruolo assegnato per una risorsa specifica si applica solo a tale risorsa. Esistono diversi ruoli predefiniti (vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md)) e, se questi non soddisfano specifiche esigenze, è possibile definire anche ruoli personalizzati.

> [!IMPORTANT]
> Si noti che, se un utente dispone di autorizzazioni di collaboratore (controllo degli accessi in base al ruolo) per un piano di gestione dell'insieme di credenziali delle chiavi, può concedere a se stesso l'accesso al piano dati impostando i criteri di accesso dell'insieme di credenziali delle chiavi, che controllano l'accesso al piano dati. È pertanto consigliabile controllare rigorosamente gli utenti che dispongono dei diritti di accesso di collaboratore all'insieme di credenziali delle chiavi per avere la sicurezza che solo le persone autorizzate possano accedere e gestire insiemi di credenziali delle chiavi, chiavi, segreti e certificati.
> 
> 

## <a name="data-plane-access-control"></a>Controllo di accesso al piano dati
Il piano dati dell'insieme di credenziali delle chiavi è costituito da operazioni che influiscono sugli oggetti in un insieme di credenziali delle chiavi, ad esempio chiavi, segreti e certificati.  Sono incluse le operazioni relative alle chiavi, come la creazione, l'importazione, l'aggiornamento, la generazione di un elenco, il backup e il ripristino, le operazioni di crittografia, come la firma, la verifica, la crittografia, la decrittografia, l'esecuzione e la rimozione del wrapping, nonché l'impostazione di tag e altri attributi per le chiavi. Analogamente, per i segreti sono incluse le operazioni per ottenere, impostare, elencare ed eliminare i segreti.

L'accesso al piano dati viene concesso impostando criteri di accesso per un insieme di credenziali delle chiavi. Per essere in grado di impostare i criteri di accesso per un insieme di credenziali delle chiavi, un utente, un gruppo o un'applicazione deve disporre delle autorizzazioni di collaboratore (controllo degli accessi in base al ruolo) per il piano di gestione relativo a tale insieme. A un utente, un gruppo o un'applicazione può essere concesso l'accesso per eseguire operazioni specifiche relative a segreti o chiavi in un insieme di credenziali delle chiavi. Per un insieme di credenziali delle chiavi sono supportate fino a 16 voci di criteri di accesso. Per concedere a più utenti l'accesso del piano dati a un insieme di credenziali delle chiavi, creare un gruppo di sicurezza di Azure Active Directory e aggiungere utenti a tale gruppo.

### <a name="key-vault-access-policies"></a>Criteri di accesso di Key Vault
I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti e certificati. È ad esempio possibile concedere a un utente l'accesso alle chiavi, ma non le autorizzazioni per i segreti. Tuttavia, le autorizzazioni per accedere a chiavi, segreti o certificati vengono definite a livello di insieme di credenziali. In altre parole, i criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni a livello di oggetto. Per impostare i criteri di accesso per un insieme di credenziali delle chiavi è possibile usare il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia dalla riga di comando di Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di gestione dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Si noti che i criteri di accesso dell'insieme di credenziali delle chiavi si applicano a livello di insieme di credenziali. Ad esempio, quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, potrà eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.
> 
> 

## <a name="example"></a>Esempio
Si supponga di voler sviluppare un'applicazione che usa un certificato per SSL, il servizio Archiviazione di Azure per archiviare i dati e una chiave RSA a 2048 bit per le operazioni di firma. Si supponga inoltre che tale applicazione venga eseguita in una macchina virtuale (o in un set di scalabilità di macchine virtuali). È possibile usare un insieme di credenziali delle chiavi per archiviare tutti i segreti dell'applicazione e anche per archiviare il certificato bootstrap usato dall'applicazione per l'autenticazione con Azure Active Directory.

Ecco un riepilogo delle chiavi e dei segreti da archiviare in un insieme di credenziali delle chiavi.

* **Certificato SSL**: usato per SSL.
* **Chiave di archiviazione**: usata per ottenere l'accesso all'account di archiviazione.
* **Chiave RSA a 2048 bit**: usata per le operazioni di firma.
* **Certificato bootstrap**: usato per eseguire l'autenticazione ad Azure Active Directory, per ottenere l'accesso all'insieme di credenziali delle chiavi in modo da recuperare la chiave di archiviazione e usare la chiave RSA per la firma.

Si considerino ora le persone coinvolte nella gestione, nella distribuzione e nel controllo dell'applicazione. In questo esempio verranno usati tre ruoli.

* **Team responsabile della sicurezza**: si tratta in genere di personale IT con ruoli di responsabilità che si occupa della salvaguardia di segreti, ad esempio i certificati SSL, le chiavi RSA usate per la firma, le stringhe di connessione per i database e le chiavi dell'account di archiviazione.
* **Sviluppatori/operatori**: si tratta di persone che sviluppano l'applicazione e la distribuiscono in Azure. In genere, non fanno parte del team responsabile della sicurezza e pertanto non dovrebbero disporre dei diritti di accesso ai dati sensibili, come i certificati SSL e le chiavi RSA, a cui però dovrebbe accedere l'applicazione che distribuiscono.
* **Revisori**: si tratta in genere di un insieme diverso di persone, distinte dagli sviluppatori e dal personale IT generale, che hanno il compito di verificare la correttezza dell'uso e della gestione di certificati, chiavi e così via e garantire la conformità agli standard di sicurezza dei dati. 

Esiste anche un altro ruolo che non rientra nell'ambito di questa applicazione, ma che è importante menzionare, ossia l'amministratore della sottoscrizione (o del gruppo di risorse). L'amministratore della sottoscrizione imposta le autorizzazioni di accesso iniziali per il team responsabile della sicurezza. In questo esempio si presuppone che l'amministratore della sottoscrizione abbia concesso al team l'accesso a un gruppo di risorse in cui sono incluse tutte le risorse necessarie per l'applicazione.

Ora verranno esaminate le azioni eseguite da ogni ruolo nel contesto di questa applicazione.

* **Team responsabile della sicurezza**
  * Creare insiemi di credenziali delle chiavi
  * Attivare la registrazione degli insiemi di credenziali delle chiavi
  * Aggiungere chiavi o segreti
  * Creare una copia di backup delle chiavi per il ripristino di emergenza
  * Impostare i criteri di accesso dell'insieme di credenziali delle chiavi per concedere a utenti e applicazioni le autorizzazioni di eseguire operazioni specifiche
  * Distribuire periodicamente chiavi o segreti
* **Sviluppatori/operatori**
  * Ottenere riferimenti a certificati bootstrap e SSL (identificazioni personali), alla chiave di archiviazione (URI del segreto) e alla chiave di firma (URI della chiave) dal team responsabile della sicurezza
  * Sviluppare e distribuire l'applicazione che accede a chiavi e segreti a livello di codice
* **Revisori**
  * Esaminare i log di utilizzo per verificare che la chiave o il segreto sia usato correttamente e sia conforme agli standard di sicurezza dei dati

Ora verranno esaminate le autorizzazioni di accesso all'insieme di credenziali delle chiavi richieste da ogni ruolo (e dall'applicazione) per eseguire le attività assegnate. 

| Ruolo utente | Autorizzazioni del piano di gestione | Autorizzazioni del piano dati |
| --- | --- | --- |
| Team responsabile della sicurezza |key vault Contributor |Chiavi: backup, create, delete, get, import, list, restore <br> Segreti: all |
| Sviluppatori/operatori |Autorizzazione di distribuzione dell'insieme di credenziali delle chiavi per consentire alle macchine virtuali distribuite di recuperare i segreti da tale insieme |Nessuna |
| Revisori |Nessuna |Chiavi: list<br>Segreti: list |
| Applicazione |Nessuna |Chiavi: sign<br>Segreti: get |

> [!NOTE]
> I revisori devono disporre dell'autorizzazione list per le chiavi e i segreti in modo da poter esaminare gli attributi di chiavi e segreti che non vengono riportati nei log, come i tag e le date di attivazione e scadenza.
> 
> 

Oltre alle autorizzazioni per l'insieme di credenziali delle chiavi, tutti e tre i ruoli devono poter accedere anche ad altre risorse, ad esempio essere in grado di distribuire macchine virtuali (o app Web e così via). Anche gli sviluppatori/operatori devono disporre dei diritti di accesso di collaboratore a questi tipi di risorse. Per i revisori è necessario l'accesso in lettura all'account di archiviazione in cui vengono archiviati i log dell'insieme di credenziali delle chiavi.

Poiché l'obiettivo principale di questo articolo è quello di illustrare la protezione dell'accesso all'insieme di credenziali delle chiavi, vengono presentati solo i concetti pertinenti a questo argomento, ignorando i dettagli relativi alla distribuzione di certificati, all'accesso a chiavi e segreti a livello di codice e così via, che sono trattati in altri documenti. La distribuzione dei certificati archiviati nell'insieme di credenziali delle chiavi alle macchine virtuali viene descritta in un [post di blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), mentre è disponibile [codice di esempio](https://www.microsoft.com/download/details.aspx?id=45343) che illustra come usare un certificato bootstrap per autenticarsi ad Azure Active Directory e ottenere l'accesso all'insieme di credenziali delle chiavi.

La maggior parte delle autorizzazioni di accesso può essere concessa tramite il portale di Azure, ma per concedere autorizzazioni granulari può essere necessario usare Azure PowerShell (o l'interfaccia della riga di comando di Azure). 

I frammenti di codice di PowerShell seguenti presuppongono quanto segue:

* L'amministratore di Azure Active Directory ha creato gruppi di sicurezza che rappresentano i tre ruoli, ovvero Contoso Security Team, Contoso App Devops e Contoso App Auditors. L'amministratore ha inoltre aggiunto utenti ai gruppi di appartenenza.
* **ContosoAppRG** è il gruppo in cui si trovano tutte le risorse. **contosologstorage** è l'area in cui vengono archiviati i log. 
* L'insieme di credenziali delle chiavi **ContosoKeyVault** e l'account di archiviazione usato per i log dell'insieme di credenziali delle chiavi **contosologstorage** devono trovarsi nello stesso percorso di Azure.

Innanzitutto l'amministratore della sottoscrizione assegna i ruoli "key vault Contributor" e "User Access Administrator" al team responsabile della sicurezza. In questo modo il team può gestire l'accesso ad altre risorse e gli insiemi di credenziali delle chiavi nel gruppo di risorse ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Lo script seguente illustra come il team responsabile della sicurezza può creare un insieme di credenziali delle chiavi, configurare la registrazione e impostare le autorizzazioni di accesso per altri ruoli e per l'applicazione. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

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

Il ruolo personalizzato definito può essere assegnato solo alla sottoscrizione in cui viene creato il gruppo di risorse ContosoAppRG. Se gli stessi ruoli personalizzati verranno usati per altri progetti in altre sottoscrizioni, è possibile che all'ambito siano associate più sottoscrizioni.

L'assegnazione di ruoli personalizzati per l'autorizzazione "deploy/action" di sviluppatori/operatori rientra nell'ambito del gruppo di risorse. In questo modo, solo le macchine virtuali create nel gruppo di risorse "ContosoAppRG" otterranno i segreti (certificati SSL e bootstrap). Le macchine virtuali create dal team di sviluppatori/operatori in un altro gruppo di risorse non saranno in grado di ottenere questi segreti, anche se conoscono gli URI dei segreti.

Questo esempio illustra uno scenario semplice. Gli scenari reali possono essere più complessi e in alcuni casi può essere necessario modificare le autorizzazioni per l'insieme di credenziali delle chiavi in base alle esigenze. Ad esempio, in questo caso si presuppone che il team responsabile della sicurezza fornisca i riferimenti a chiavi e segreti (URI e identificazioni personali) richiesti dal team di sviluppatori/operatori per inserire riferimenti nelle proprie applicazioni. Di conseguenza, non è necessario concedere l'accesso al piano dati al team di sviluppatori/operatori. Si noti inoltre che questo esempio riguarda principalmente la sicurezza dell'insieme di credenziali delle chiavi. Considerazioni simili sono necessarie anche per proteggere [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/security/), [ account di archiviazione](../storage/common/storage-security-guide.md) e altre risorse di Azure.

> [!NOTE]
> Nota: questo esempio mostra come l'accesso all'insieme di credenziali delle chiavi verrà bloccato nell'ambiente di produzione. Gli sviluppatori devono quindi avere una sottoscrizione o un gruppo di risorse personale su cui dispongono di autorizzazioni complete per gestire gli insiemi di credenziali delle chiavi, le macchine virtuali e l'account di archiviazione in cui sviluppano l'applicazione.
> 
> 

## <a name="resources"></a>Risorse
* [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md)
  
  Questo articolo descrive il controllo degli accessi in base al ruolo di Azure Active Directory e il relativo funzionamento.
* [Controllo degli accessi in base al ruolo: Ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md)
  
  Questo articolo illustra tutti i ruoli predefiniti disponibili nel controllo degli accessi in base al ruolo.
* [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Questo articolo illustra i modelli di distribuzione classica e di Resource Manager e descrive i vantaggi dell'uso di Resource Manager e dei gruppi di risorse.
* [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  
  Questo articolo illustra come gestire il controllo degli accessi in base al ruolo con Azure PowerShell.
* [Gestione del controllo degli accessi in base al ruolo con l'API REST](../active-directory/role-based-access-control-manage-access-rest.md)
  
  Questo articolo illustra come usare l'API REST per gestire il controllo degli accessi in base al ruolo.
* [Controllo degli accessi in base al ruolo per Microsoft Azure da Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Questo è un collegamento a un video su Channel 9 della conferenza Microsoft Ignite 2015. In questa sessione vengono illustrate le funzionalità di creazione report e gestione degli accessi di Azure, oltre alle procedure consigliate per la protezione dell'accesso alle sottoscrizioni di Azure con Azure Active Directory.
* [Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)
  
  Questo articolo descrive l'intero flusso di OAuth 2.0 per l'autenticazione con Azure Active Directory.
* [key vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx) (API REST di gestione dell'insieme di credenziali delle chiavi)
  
  Questo documento include informazioni di riferimento sulle API REST per gestire l'insieme di credenziali delle chiavi a livello di codice, inclusa l'impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi.
* [key vault REST APIs](https://msdn.microsoft.com/library/azure/dn903609.aspx) (API REST dell'insieme di credenziali delle chiavi)
  
  Questo documento include informazioni di riferimento sulle API REST dell'insieme di credenziali delle chiavi.
* [Key access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl) (Controllo di accesso per le chiavi)
  
  Collegamento alla documentazione di riferimento sul controllo di accesso per i segreti.
* [Secret access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl) (Controllo di accesso per i segreti)
  
  Questa sezione include informazioni di riferimento sul controllo di accesso per i segreti.
* [Impostare](https://msdn.microsoft.com/library/mt603625.aspx) e [rimuovere](https://msdn.microsoft.com/library/mt619427.aspx) i criteri di accesso dell'insieme di credenziali delle chiavi usando PowerShell
  
  Questi documenti includono informazioni di riferimento per consentire ai cmdlet di PowerShell di gestire i criteri di accesso dell'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione introduttiva per gli amministratori, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).

Per altre informazioni sulla registrazione dell'utilizzo per l'insieme di credenziali delle chiavi, vedere [Registrazione dell'insieme di credenziali delle chiavi di Azure](key-vault-logging.md).

Per altre informazioni sull'uso di chiavi e segreti con l'insieme di credenziali delle chiavi di Azure, vedere [About Keys and Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx) (Informazioni su chiavi e segreti).

In caso di domande sull'insieme di credenziali delle chiavi, visitare i [forum di Insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).


