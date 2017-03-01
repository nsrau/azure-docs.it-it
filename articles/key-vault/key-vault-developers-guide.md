---
title: Guida per gli sviluppatori dell&quot;insieme di credenziali delle chiavi | Documentazione Microsoft
description: 'Gli sviluppatori possono utilizzare l&quot;insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all&quot;interno dell&quot;ambiente Microsoft Azure. '
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: b2b1bd28-e149-4d69-b08b-97f6c50ebe30
ms.service: key-vault
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/17/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 74de2165ea3a66cd0babc9f6d6d451522988bbe6
ms.openlocfilehash: b3fc845812e8a4a2729f026baa82b58319d5c5f3


---
# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure
Con l'insieme di credenziali delle chiavi è possibile accedere in modo sicuro ai dati sensibili all'interno delle applicazioni in modo che:

* Le chiavi e i segreti vengano protetti senza dover scrivere manualmente il codice e possano essere usati dalle applicazioni.
* I clienti possano avere e gestire autonomamente le rispettive chiavi in modo che lo sviluppatore possa concentrarsi su altre attività, ad esempio fornire le principali funzionalità del software. In questo modo le applicazioni non saranno responsabili dei segreti e delle chiavi tenant dei clienti.
* L'applicazione possa usare le chiavi per la firma e la crittografia, ma tenga la gestione della chiave esterna all'applicazione in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita.
* Con la versione di settembre 2016 dell'insieme di credenziali delle chiavi, le applicazioni ora possono usare i certificati dell'applicazione. Per altre informazioni, vedere l'articolo **About keys, secrets, and certificates** (Informazioni su chiavi, segreti e certificati) nelle [informazioni di riferimento su REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)

## <a name="videos"></a>Video
Questo video mostra come creare il proprio insieme di credenziali delle chiavi e come usarlo dall'applicazione di esempio 'Hello Key Vault'.


>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player]


Collegamenti alle risorse citate nel video:

* [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
* [Codice di esempio dell'insieme di credenziali delle chiavi di Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Per altre informazioni, è possibile seguire il [blog relativo all'insieme di credenziali delle chiavi](http://aka.ms/kvblog) e partecipare al [forum relativo all'insieme di credenziali delle chiavi](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione di insiemi di credenziali delle chiavi
Prima di usare l'insieme di credenziali delle chiavi di Azure nel codice, è possibile creare e gestire insiemi di credenziali tramite REST, modelli di Resource Manager, PowerShell o l'interfaccia della riga di comando, come descritto negli articoli seguenti:

* [Creare e gestire insiemi di credenziali chiave con REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
* [Creare e gestire insiemi di credenziali chiave con PowerShell](key-vault-get-started.md)
* [Creare e gestire insiemi di credenziali chiave con l’interfaccia della riga di comando](key-vault-manage-with-cli.md)
* [Creare un insieme di credenziali delle chiavi e aggiungere un segreto tramite un modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Le operazioni sugli insiemi di credenziali delle chiavi vengono autenticate tramite AAD e autorizzate tramite i criteri di accesso dell'insieme di credenziali delle chiavi, definiti per ogni insieme di credenziali.
>
>

## <a name="coding-with-key-vault"></a>Codifica con l'insieme di credenziali delle chiavi
Il sistema di gestione dell'insieme di credenziali delle chiavi per i programmatori è costituito da diverse interfacce che hanno come base REST; vedere [Riferimento all'API REST dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Gli utenti per i quali l'autorizzazione ha esito positivo possono eseguire le operazioni seguenti:

* Gestire le chiavi di crittografia usando [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) e altre operazioni
* Gestire i segreti con [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) e altre operazioni
* Usare le chiavi di crittografia con le operazioni [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx)[WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Per l'uso con l'insieme di credenziali delle chiavi sono disponibili gli SDK seguenti:

| [![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx) | [![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
|:---:|:---:|
| [Documentazione su .NET SDK](https://msdn.microsoft.com/library/mt765854.aspx) |[Documentazione su Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
| [Pacchetto .NET SDK in NuGet](http://www.nuget.org/packages/Microsoft.Azure.KeyVault) |[Pacchetto Node.js SDK](https://www.npmjs.com/package/azure-keyvault) |

Per altre informazioni sulla versione 2.x di .NET SDK, vedere le [note sulla versione](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Codice di esempio
Per esempi completi che usano l'insieme di credenziali delle chiavi con le applicazioni, vedere:

* L'applicazione .NET di esempio *HelloKeyVault* e un esempio di servizio Web di Azure. [Esempi di codice di insieme di credenziali chiave di Azure](http://www.microsoft.com/download/details.aspx?id=45343)
* L'esercitazione relativa all'uso dell'insieme di credenziali delle chiavi di Azure da un'applicazione Web in Azure. [Usare l'insieme di credenziali chiave di Azure da un'applicazione Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procedure
Gli articoli e gli scenari seguenti offrono indicazioni specifiche su come usare l'insieme di credenziali delle chiavi di Azure:

* [Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione](key-vault-subscription-move-fix.md): quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità (utenti e applicazioni) nel tenant B. Per risolvere questo problema, seguire questa guida.
* [Accesso a un insieme di credenziali delle chiavi protetto da firewall](key-vault-access-behind-firewall.md): per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.
* [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md) : questo articolo consente di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure.
* [Passare valori protetti come password durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md) : se è necessario passare come parametro durante la distribuzione un valore protetto, ad esempio una password, è possibile archiviare tale valore come chiave privata in un insieme di credenziali delle chiavi di Azure e fare riferimento al valore in altri modelli di Resource Manager.
* [Extensible Key Management tramite l'insieme di credenziali delle chiavi di Azure (SQL Server)](https://msdn.microsoft.com/library/dn198405.aspx): SQL Server Connector per l'insieme di credenziali delle chiavi di Azure consente a SQL Server e a SQL in una VM di sfruttare il servizio di insieme di credenziali delle chiavi di Azure come provider EKM (Extensible Key Management) per proteggere le chiavi di crittografia per le applicazioni. L'articolo contiene esempi di Transparent Data Encryption, backup della crittografia e crittografia a livello di colonna.
* [Come distribuire certificati a macchine virtuali dall'insieme di credenziali delle chiavi](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : un'applicazione cloud in esecuzione su una VM in Azure necessita di un certificato. Come ottenere oggi il certificato per questa VM?
* [Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end](key-vault-key-rotation-log-monitoring.md): illustra come configurare la rotazione e il controllo delle chiavi con l'insieme di credenziali delle chiavi di Azure.
* [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Distribuzione del certificato dell'app Web di Azure tramite l'insieme di credenziali delle chiavi) offre istruzioni dettagliate per la distribuzione dei certificati archiviati nell'insieme di credenziali delle chiavi come parte dell'offerta del [certificato del servizio app](https://azure.microsoft.com/en-us/blog/internals-of-app-service-certificate/).
* [Concedere a molte applicazioni l'autorizzazione per accedere a Key Vault](key-vault-group-permissions-for-apps.md): i criteri di controllo di accesso dell'insieme di credenziali delle chiavi supportano solo 16 voci. È tuttavia possibile creare un gruppo di sicurezza di Azure Active Directory. Aggiungere tutte le entità servizio associate a questo gruppo di sicurezza e quindi concedere a tale gruppo di accedere a Key Vault.

Per indicazioni specifiche sulle attività relative all'integrazione e all'uso dell'insieme di credenziali delle chiavi con Azure, vedere gli [esempi di modelli di Azure Resource Manager di Ryan Jones per l'insieme di credenziali delle chiavi](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integrazione con l'insieme di credenziali delle chiavi
Questi articoli illustrano altri scenari e servizi che usano o integrano l'insieme di credenziali delle chiavi.

* [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) sfrutta la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo. La soluzione è integrata con l'insieme di credenziali delle chiavi di Azure per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.
* [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) offre la possibilità di crittografare i dati archiviati nell'account. In merito alla gestione delle chiavi, Data Lake Store offre due modalità per la gestione delle chiavi di crittografia master (MEK) necessarie per decrittografare i dati archiviati in Data Lake Store. È possibile affidare la gestione delle chiavi MEK a Data Lake Store oppure mantenere la proprietà delle chiavi MEK usando il proprio account dell'insieme di credenziali delle chiavi di Azure. La modalità di gestione delle chiavi viene specificata durante la creazione dell'account di Data Lake Store. 
* [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) consente di gestire la propria chiave tenant. Ad esempio, anziché affidare a Microsoft la gestione della chiave tenant (impostazione predefinita), l'utente può gestire la propria chiave tenant per garantire la conformità alle normative specifiche che si applicano all'organizzazione. La gestione della propria chiave tenant viene definita Bring Your Own Key o BYOK.


## <a name="supporting-libraries"></a>Supporto di librerie
* [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) include le interfacce `IKey` e `IKeyResolver` per individuare le chiavi dagli identificatori ed eseguire operazioni con le chiavi.
* [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornisce funzionalità estese per Insieme di credenziali delle chiavi di Azure.

## <a name="other-key-vault-resources"></a>Altre risorse per l'insieme di credenziali delle chiavi
* [Blog sull'insieme di credenziali delle chiavi](http://aka.ms/kvblog)
* [Forum sull'insieme di credenziali delle chiavi](http://aka.ms/kvforum)



<!--HONumber=Jan17_HO3-->


