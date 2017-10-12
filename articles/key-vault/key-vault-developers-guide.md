---
title: Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure
description: Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure.
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.openlocfilehash: fec4769c0bd571edea84dd2f766bb907d8819be5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure

Key Vault consente di accedere in modo sicuro ai dati sensibili dall'interno delle applicazioni:

- Le chiavi e i segreti vengano protetti senza dover scrivere manualmente il codice e possano essere usati dalle applicazioni.
- I clienti possano avere e gestire autonomamente le rispettive chiavi in modo che lo sviluppatore possa concentrarsi su altre attività, ad esempio fornire le principali funzionalità del software. In questo modo le applicazioni non saranno responsabili dei segreti e delle chiavi tenant dei clienti.
- L'applicazione possa usare le chiavi per la firma e la crittografia, ma tenga la gestione della chiave esterna all'applicazione in modo che la soluzione sia adatta a essere un'app geograficamente distribuita.
- A partire dalla versione di settembre 2016 di Key Vault, le applicazioni possono ora usare i [certificati](https://docs.microsoft.com/rest/api/keyvault/certificate-operations) di Key Vault. Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)

## <a name="public-previews"></a>Anteprime pubbliche

Periodicamente, viene rilasciata un'anteprima pubblica di una nuova funzionalità di Key Vault. Provarle e comunicare il feedback all'azienda all'indirizzo e-mail azurekeyvault@microsoft.com, dedicato ai commenti e ai suggerimenti.

### <a name="storage-account-keys---july-10-2017"></a>Chiavi degli account di archiviazione - 10 luglio 2017

>[!NOTE]
>Per questo aggiornamento di Azure Key Vault è disponibile in anteprima solo la funzionalità **Chiavi degli account di archiviazione**.

Questa versione di anteprima include la nuova funzionalità di chiavi degli account di archiviazione, disponibile tramite queste interfacce: [.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) e [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Per ulteriori informazioni sulla nuova funzionalità chiavi degli account di archiviazione, vedere [Panoramica delle chiavi degli account di Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Video

Questo video mostra come creare il proprio insieme di credenziali delle chiavi e come usarlo dall'applicazione di esempio 'Hello Key Vault'.

- [Guida introduttiva per gli sviluppatori di Key Vault](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Risorse citate nel video precedente:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Codice di esempio dell'insieme di credenziali delle chiavi di Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione di insiemi di credenziali delle chiavi

Prima di usare l'insieme di credenziali delle chiavi di Azure nel codice, è possibile creare e gestire insiemi di credenziali tramite REST, modelli di Resource Manager, PowerShell o l'interfaccia della riga di comando, come descritto negli articoli seguenti:

- [Creare e gestire insiemi di credenziali chiave con REST](https://docs.microsoft.com/rest/api/keyvault/)
- [Creare e gestire insiemi di credenziali chiave con PowerShell](key-vault-get-started.md)
- [Creare e gestire insiemi di credenziali chiave con l’interfaccia della riga di comando](key-vault-manage-with-cli2.md)
- [Creare un insieme di credenziali delle chiavi e aggiungere un segreto tramite un modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Le operazioni sugli insiemi di credenziali delle chiavi vengono autenticate tramite AAD e autorizzate tramite i criteri di accesso dell'insieme di credenziali delle chiavi, definiti per ogni insieme di credenziali.

## <a name="coding-with-key-vault"></a>Codifica con l'insieme di credenziali delle chiavi

Il sistema di gestione di Key Vault per i programmatori è costituito da diverse interfacce, con REST come base. Tramite l'interfaccia REST è possibile accedere a tutte le risorse degli insiemi di credenziali delle chiavi: chiavi, segreti e certificati. [Informazioni di riferimento sull'API REST di Key Vault](https://docs.microsoft.com/rest/api/keyvault/). 

### <a name="supported-programming-languages"></a>Linguaggi di programmazione supportati

#### <a name="net"></a>.NET

- [Informazioni di riferimento sulle API .NET per Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Per altre informazioni sulla versione 2.x di .NET SDK, vedere le [note sulla versione](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

- [Java SDK per Key Vault](https://docs.microsoft.com/java/api/com.microsoft.azure.keyvault)

#### <a name="nodejs"></a>Node.js

In Node.js l'API di gestione dell'insieme di credenziali e l'API dell'oggetto dell'insieme di credenziali sono separati. Gestione dell'insieme di credenziali delle chiavi consente la creazione e l'aggiornamento dell'insieme di credenziali delle chiavi. L'API delle operazioni di Key Vault funziona con oggetti dell'insieme di credenziali come ad esempio chiavi, segreti e certificati. 

- [Informazioni di riferimento sulle API Node.js per Gestione di Key Vault](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)
- [Informazioni di riferimento sulle API Node.js per le operazioni di Key Vault](http://azure.github.io/azure-sdk-for-node/azure-keyvault/latest/) 

### <a name="quick-start"></a>Avvio rapido

- [Creare un insieme di credenziali delle chiavi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introduzione a Key Vault in Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Esempi di codice

Per esempi completi che usano l'insieme di credenziali delle chiavi con le applicazioni, vedere:

- [Esempi di codice di Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343): applicazione .NET di esempio *HelloKeyVault* ed esempio di servizio Web di Azure. 
- [Usare Azure Key Vault da un'applicazione Web](key-vault-use-from-web-application.md): esercitazione sull'uso di Azure Key Vault da un'applicazione Web in Azure. 

## <a name="how-tos"></a>Procedure

Gli articoli e gli scenari seguenti offrono indicazioni specifiche su come usare l'insieme di credenziali delle chiavi di Azure:

- [Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione](key-vault-subscription-move-fix.md): quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità (utenti e applicazioni) nel tenant B. Per risolvere questo problema, seguire questa guida.
- [Accesso a un insieme di credenziali delle chiavi protetto da firewall](key-vault-access-behind-firewall.md): per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.
- [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md) : questo articolo consente di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure.
- [Passare valori protetti come password durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md) : se è necessario passare come parametro durante la distribuzione un valore protetto, ad esempio una password, è possibile archiviare tale valore come chiave privata in un insieme di credenziali delle chiavi di Azure e fare riferimento al valore in altri modelli di Resource Manager.
- [Extensible Key Management tramite l'insieme di credenziali delle chiavi di Azure (SQL Server)](https://msdn.microsoft.com/library/dn198405.aspx): SQL Server Connector per l'insieme di credenziali delle chiavi di Azure consente a SQL Server e a SQL in una VM di sfruttare il servizio di insieme di credenziali delle chiavi di Azure come provider EKM (Extensible Key Management) per proteggere le chiavi di crittografia per le applicazioni. L'articolo contiene esempi di Transparent Data Encryption, backup della crittografia e crittografia a livello di colonna.
- [Come distribuire certificati a macchine virtuali dall'insieme di credenziali delle chiavi](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : un'applicazione cloud in esecuzione su una VM in Azure necessita di un certificato. Come ottenere oggi il certificato per questa VM?
- [Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end](key-vault-key-rotation-log-monitoring.md): illustra come configurare la rotazione e il controllo delle chiavi con l'insieme di credenziali delle chiavi di Azure.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Distribuzione del certificato dell'app Web di Azure tramite l'insieme di credenziali delle chiavi) offre istruzioni dettagliate per la distribuzione dei certificati archiviati nell'insieme di credenziali delle chiavi come parte dell'offerta del [certificato del servizio app](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Concedere a molte applicazioni l'autorizzazione per accedere a Key Vault](key-vault-group-permissions-for-apps.md): i criteri di controllo di accesso dell'insieme di credenziali delle chiavi supportano solo 16 voci. È tuttavia possibile creare un gruppo di sicurezza di Azure Active Directory. Aggiungere tutte le entità servizio associate a questo gruppo di sicurezza e quindi concedere a tale gruppo di accedere a Key Vault.
- Per indicazioni specifiche sulle attività relative all'integrazione e all'uso dell'insieme di credenziali delle chiavi con Azure, vedere gli [esempi di modelli di Azure Resource Manager di Ryan Jones per l'insieme di credenziali delle chiavi](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](key-vault-soft-delete-cli.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.
- [Come usare l'eliminazione temporanea di Key Vault con PowerShell](key-vault-soft-delete-powershell.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.

## <a name="integrated-with-key-vault"></a>Integrazione con l'insieme di credenziali delle chiavi

Questi articoli illustrano altri scenari e servizi che usano o si integrano con Key Vault.

- [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) sfrutta la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo. La soluzione è integrata con l'insieme di credenziali delle chiavi di Azure per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) offre la possibilità di crittografare i dati archiviati nell'account. In merito alla gestione delle chiavi, Data Lake Store offre due modalità per la gestione delle chiavi di crittografia master (MEK) necessarie per decrittografare i dati archiviati in Data Lake Store. È possibile affidare la gestione delle chiavi MEK a Data Lake Store oppure mantenere la proprietà delle chiavi MEK usando il proprio account dell'insieme di credenziali delle chiavi di Azure. La modalità di gestione delle chiavi viene specificata durante la creazione dell'account di Data Lake Store. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) consente di gestire la propria chiave tenant. Ad esempio, anziché affidare a Microsoft la gestione della chiave tenant (impostazione predefinita), l'utente può gestire la propria chiave tenant per garantire la conformità alle normative specifiche che si applicano all'organizzazione. La gestione della propria chiave tenant viene definita Bring Your Own Key o BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Panoramiche e concetti su Key Vault

- [Comportamento di eliminazione temporanea di Key Vault](key-vault-ovw-soft-delete.md) descrive una funzionalità che consente il recupero di oggetti eliminati, sia che l'eliminazione sia stata accidentale sia che sia stata intenzionale.
- [Limitazione del client di Key Vault](key-vault-ovw-throttling.md) descrive i concetti di base della limitazione e offre un approccio per l'app.
- [Panoramica delle chiavi dell'account di archiviazione di Key Vault](key-vault-ovw-storage-keys.md) descrive le chiavi dell'account di archiviazione di Azure dell'integrazione di Key Vault.
- [Scenari di sicurezza di Key Vault](key-vault-ovw-security-worlds.md) descrive le relazioni tra le aree e le zone di sicurezza.

## <a name="social"></a>Social media

- [Blog sull'insieme di credenziali delle chiavi](http://aka.ms/kvblog)
- [Forum sull'insieme di credenziali delle chiavi](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Supporto di librerie

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) include le interfacce **IKey** e **IKeyResolver** per individuare le chiavi dagli identificatori ed eseguire operazioni con le chiavi.
- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornisce funzionalità estese per Insieme di credenziali delle chiavi di Azure.


