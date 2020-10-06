---
title: Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure
description: Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 662c23a29e383800a4591c900e02133c16fa2090
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743319"
---
# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure

Key Vault consente di accedere in modo sicuro ai dati sensibili dall'interno delle applicazioni:

- Le chiavi, i segreti e i certificati vengono protetti senza dover scrivere manualmente il codice ed è possibile usarli facilmente dalle applicazioni.
- È possibile fare in modo che i clienti possiedano e gestiscano chiavi, segreti e certificati, per consentirti di concentrarti sulla fornitura delle funzionalità software di base. In questo modo, le applicazioni non sono proprietarie della responsabilità o della responsabilità potenziale per le chiavi del tenant, i segreti e i certificati dei clienti.
- L'applicazione può usare chiavi per la firma e la crittografia, ma mantiene la gestione delle chiavi esterna dall'applicazione. Per ulteriori informazioni sulle chiavi, vedere [informazioni sulle chiavi](../keys/about-keys.md) .
- È possibile gestire le credenziali, ad esempio le password, le chiavi di accesso, i token di firma di accesso condiviso archiviati in Key Vault come segreti, vedere [informazioni sui segreti](../secrets/about-secrets.md)
- Gestire i certificati. Per ulteriori informazioni, vedere [informazioni sui certificati](../certificates/about-certificates.md) .

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo [Cos'è l'insieme di credenziali chiave di Azure?](overview.md)

## <a name="public-previews"></a>Anteprime pubbliche

Periodicamente, viene rilasciata un'anteprima pubblica di una nuova funzionalità di Key Vault. Prova le funzionalità di anteprima pubblica e inviaci le tue azurekeyvault@microsoft.com opinioni, l'indirizzo di posta elettronica di feedback.

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione di insiemi di credenziali delle chiavi

Key Vault gestione, analogamente ad altri servizi di Azure, viene eseguita tramite Azure Resource Manager servizio. Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione che consente di creare, aggiornate ed eliminare risorse nell'account Azure. Per ulteriori informazioni, vedere [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

L'accesso al livello di gestione è controllato dal [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview). In Key Vault, il livello di gestione, noto anche come piano di gestione o di controllo, consente di creare e gestire gli insiemi di credenziali delle chiavi e i relativi attributi, inclusi i criteri di accesso, ma non le chiavi, i segreti e i certificati, gestiti sul piano dati. È possibile usare il ruolo predefinito `Key Vault Contributor` per concedere l'accesso di gestione ai Key Vault.     

**API e SDK per la gestione di Key Vault:**

| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault)<br>[Guida introduttiva](quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault)<br>[Guida introduttiva](quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/)|[Riferimento](/azure/templates/microsoft.keyvault/vaults)|[Riferimento](/dotnet/api/microsoft.azure.management.keyvault)|[Riferimento](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Riferimento](/java/api/com.microsoft.azure.management.keyvault)|[Riferimento](/javascript/api/@azure/arm-keyvault)|

Vedere [librerie client](client-libraries.md) per pacchetti di installazione e codice sorgente.

Per ulteriori informazioni sul piano di gestione Key Vault, vedere [Key Vault piano di gestione](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Eseguire l'autenticazione a Key Vault nel codice

Key Vault usa l'autenticazione Azure AD che richiede Azure AD entità di sicurezza per concedere l'accesso. Un Azure AD entità di sicurezza può essere un utente, un'entità servizio dell'applicazione, un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)o un gruppo di qualsiasi tipo di entità di sicurezza.

### <a name="authentication-best-practices"></a>Procedure consigliate per l'autenticazione
Si consiglia di usare l'identità gestita per le applicazioni distribuite in Azure. Se si usano i servizi di Azure, che non supportano l'identità gestita o se le applicazioni vengono distribuite in locale, l' [entità servizio con un certificato](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) è un'alternativa possibile. In questo scenario, il certificato deve essere archiviato in Key Vault e ruotato spesso. L'entità servizio con segreto può essere usata per ambienti di sviluppo e test e in locale o in Cloud Shell usando l'entità utente è consigliata.

Gli scenari di autenticazione precedenti sono supportati dalla libreria client di identità di Azure e sono integrati con Key Vault SDK. La libreria di identità di Azure può essere usata in diversi ambienti e piattaforme senza modificare il codice. Anche l'identità di Azure recupera automaticamente il token di autenticazione da connesso all'utente di Azure con l'interfaccia della riga di comando di Azure, Visual Studio, Visual Studio Code e altri. 

Per altre informazioni, vedere: 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>Gestire chiavi, certificati e segreti

L'accesso a chiavi, segreti e certificati è controllato dal piano dati. Il controllo di accesso al piano dati può essere eseguito usando i criteri di accesso dell'insieme di credenziali locale o RBAC (anteprima).

**API e SDK per chiavi**


| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/key)<br>[Guida introduttiva](../keys/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault/)<br>[Guida introduttiva](../keys/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#key-operations)|N/D|[Riferimento](/dotnet/api/azure.security.keyvault.keys)|[Riferimento](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Guida introduttiva](../keys/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[Riferimento](/javascript/api/@azure/keyvault-keys/)|

**API e SDK per i certificati**


| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/certificate)<br>[Guida introduttiva](../certificates/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault)<br>[Guida introduttiva](../certificates/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#certificate-operations)|N/D|[Riferimento](/dotnet/api/azure.security.keyvault.certificates)|[Riferimento](/python/api/overview/azure/keyvault-certificates-readme)<br>[Guida introduttiva](../certificates/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[Riferimento](/javascript/api/@azure/keyvault-certificates/)|

**API e SDK per i segreti**


| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/secret)<br>[Guida introduttiva](../secrets/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault/)<br>[Guida introduttiva](../secrets/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#secret-operations)|[Riferimento](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Guida introduttiva](../secrets/quick-create-template.md)|[Riferimento](/dotnet/api/azure.security.keyvault.secrets)<br>[Guida introduttiva](../secrets/quick-create-net.md)|[Riferimento](/python/api/overview/azure/keyvault-secrets-readme)<br>[Guida introduttiva](../secrets/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Guida introduttiva](../secrets/quick-create-java.md)|[Riferimento](/javascript/api/@azure/keyvault-secrets/)<br>[Guida introduttiva](../secrets/quick-create-node.md)|

Vedere [librerie client](client-libraries.md) per pacchetti di installazione e codice sorgente.

Per altre informazioni sulla sicurezza del piano dati Key Vault, vedere [criteri di accesso e piano dati Key Vault](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) e Key Vault piano dati e controllo degli accessi in base al ruolo [(anteprima)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Esempi di codice

Per esempi completi che usano l'insieme di credenziali delle chiavi con le applicazioni, vedere:

- [Esempi di codice di Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault): esempi di codice per Azure Key Vault. 

## <a name="how-tos"></a>Procedure

Gli articoli e gli scenari seguenti offrono indicazioni specifiche su come usare l'insieme di credenziali delle chiavi di Azure:

- [Accesso a un insieme di credenziali delle chiavi protetto da firewall](access-behind-firewall.md): per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.
- Come distribuire certificati alle macchine virtuali da Key Vault [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) : un'applicazione cloud in esecuzione in una macchina virtuale in Azure necessita di un certificato. Come ottenere oggi il certificato per questa VM?
- [Distribuzione di un certificato dell'app Web di Azure tramite Key Vault](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- Assegnare un criterio di accesso[CLI](assign-access-policy-cli.md)(  |  [portale PowerShell](assign-access-policy-powershell.md)dell'interfaccia della riga di comando  |  [Portal](assign-access-policy-portal.md)). 
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](soft-delete-cli.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.
- [Passare valori protetti come password durante la distribuzione](../../azure-resource-manager/templates/key-vault-parameter.md) : se è necessario passare come parametro durante la distribuzione un valore protetto, ad esempio una password, è possibile archiviare tale valore come chiave privata in un insieme di credenziali delle chiavi di Azure e fare riferimento al valore in altri modelli di Resource Manager.

## <a name="integrated-with-key-vault"></a>Integrazione con l'insieme di credenziali delle chiavi

Questi articoli illustrano altri scenari e servizi che usano o si integrano con Key Vault.

- [Crittografia dei inattivi con Key Vault](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) consente di gestire la propria chiave tenant. Ad esempio, anziché affidare a Microsoft la gestione della chiave tenant (impostazione predefinita), l'utente può gestire la propria chiave tenant per garantire la conformità alle normative specifiche che si applicano all'organizzazione. La gestione della propria chiave tenant viene definita Bring Your Own Key o BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Panoramiche e concetti su Key Vault

- [Comportamento di eliminazione temporanea di Key Vault](soft-delete-overview.md) descrive una funzionalità che consente il recupero di oggetti eliminati, sia che l'eliminazione sia stata accidentale sia che sia stata intenzionale.
- [Limitazione del client di Key Vault](overview-throttling.md) descrive i concetti di base della limitazione e offre un approccio per l'app.
- [Scenari di sicurezza di Key Vault](overview-security-worlds.md) descrive le relazioni tra le aree e le zone di sicurezza.

## <a name="social"></a>Social network

- [Blog sull'insieme di credenziali delle chiavi](https://aka.ms/kvblog)
- [Forum sull'insieme di credenziali delle chiavi](https://aka.ms/kvforum)
