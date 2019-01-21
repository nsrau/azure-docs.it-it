---
ms.assetid: ''
title: Endpoint del servizio di rete virtuale per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Panoramica degli endpoint del servizio di rete virtuale per Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 2c035b7c6bbd687ca2922d5f52e2ddaa0c96e50c
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351425"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Endpoint del servizio di rete virtuale per Azure Key Vault

Gli endpoint del servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.

Esiste un'importante eccezione a questa limitazione. Se un utente ha acconsentito esplicitamente a usare servizi Microsoft attendibili, le connessioni da tali servizi sono consentire attraverso il firewall. Ad esempio, questi servizi includono Office 365 Exchange Online, Office 365 SharePoint Online, Calcolo di Azure, Azure Resource Manager e Backup di Azure. Questi utenti devono comunque presentare un token di Azure Active Directory valido e devono avere le autorizzazioni, configurate come criteri di accesso, per eseguire l'operazione richiesta. Per altre informazioni, vedere [Endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenari di utilizzo

È possibile configurare [reti virtuali e firewall di Key Vault](key-vault-network-security.md) per negare l'accesso al traffico da tutte le reti, incluso il traffico Internet, per impostazione predefinita. È possibile concedere l'accesso al traffico proveniente da reti virtuali specifiche di Azure e intervalli di indirizzi IP Internet pubblici, creando un limite di rete protetta per le applicazioni.

> [!NOTE]
> I firewall di Key Vault e le regole di rete virtuale si applicano solo al [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) di Key Vault. Le operazioni del piano di controllo Key Vault, ad esempio le operazioni di creazione, eliminazione e modifica, l'impostazione di criteri di accesso, la configurazione del firewall e delle regole di rete virtuale, non sono interessate dai firewall e dalle regole di rete virtuale.

Di seguito sono riportati alcuni esempi di uso degli endpoint del servizio:

* Si usa Key Vault per archiviare chiavi di crittografia, segreti dell'applicazione, certificati e si vuole bloccare l'accesso all'insieme di credenziali delle chiavi dalla rete Internet pubblica.
* Si vuole bloccare l'accesso all'insieme di credenziali delle chiavi in modo che solo l'applicazione o un breve elenco di host designati possano connettesi all'insieme di credenziali delle chiavi.
* Si ha un'applicazione in esecuzione nella rete virtuale di Azure e la rete virtuale è bloccata per tutto il traffico in ingresso e in uscita. L'applicazione deve comunque connettersi a Key Vault per recuperare segreti o certificati o usare le chiavi di crittografia.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurare firewall e reti virtuali Key Vault

Ecco i passaggi necessari per configurare i firewall e le reti virtuali. Questi passaggi sono applicabili se si usa PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure.

1. Abilitare la [registrazione in Key Vault](key-vault-logging.md) per visualizzare i log di accesso dettagliati. Ciò è utile nella diagnostica quando i firewall e le regole di rete virtuale impediscono l'accesso a un insieme di credenziali delle chiavi. Questo passaggio è facoltativo ma consigliato.
2. Abilitare gli **endpoint del servizio per l'insieme di credenziali delle chiavi** per le reti virtuali di destinazione e le subnet.
3. Configurare i firewall e le regole di rete virtuale per un insieme di credenziali delle chiavi per limitare l'accesso a tale insieme da reti virtuali, subnet e intervalli di indirizzi IPv4 specifici.
4. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi Microsoft attendibili, abilitare l'opzione per consentire ai **servizi di Azure attendibili** di connettersi a Key Vault.

Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Quando le regole del firewall sono operative, gli utenti possono eseguire le operazioni del [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault solo se le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.


> [!NOTE]
> Tenere presente le seguenti limitazioni di configurazione:
> * Sono consentite al massimo 127 regole della rete virtuale e 127 regole IPv4. 
> * Gli intervalli di indirizzi di piccole dimensioni che usano dimensioni di prefisso "/31" o "/32" non sono supportati. Configurare questi intervalli usando le regole dei singoli indirizzi IP.
> * Le regole di rete IP sono consentite solo per gli indirizzi IP pubblici. Gli intervalli di indirizzi IP riservati per le reti private (come da definizione in RFC 1918) non sono consentiti nelle regole IP. Le reti private includono gli indirizzi che iniziano con **10.**, **172.16.** e **192.168.**. 
> * Attualmente sono supportati solo gli indirizzi IPv4.

## <a name="trusted-services"></a>Servizi attendibili

Di seguito è riportato un elenco di servizi attendibili che sono autorizzati ad accedere a un insieme di credenziali delle chiavi se è abilitata l'opzione **Allow trusted services** (Consenti servizi attendibili).

|Servizio attendibile|Scenari di utilizzo|
| --- | --- |
|Servizio di distribuzione di Macchine virtuali di Azure|[Distribuire i certificati alle macchine virtuali da Key Vault gestito dal cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Servizio di distribuzione dei modelli di Azure Resource Manager|[Passare valori protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Servizio di crittografia dei volumi di Crittografia dischi di Azure|Consentire l'accesso a BitLocker Key (VM Windows) o DM Passphrase (VM Linux) e la chiave di crittografia della chiave durante la distribuzione della macchina virtuale. In questo modo si abilita [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md).|
|Backup di Azure|Consentire il backup e ripristino di segreti e chiavi pertinenti durante il backup delle macchine virtuali di Azure usando [Backup di Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online e SharePoint Online|Consentire l'accesso alla chiave cliente per la crittografia del servizio di archiviazione di Azure con [Chiave cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Consentire l'accesso alla chiave del tenant per [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection).|
|Servizio app di Azure|[Distribuire un certificato dell'app Web di Azure con Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Database SQL di Azure|[Transparent Data Encryption con supporto BYOK (Bring Your Own Key) per il database SQL di Azure e Azure SQL Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Archiviazione di Azure|[Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Archivio Azure Data Lake|[Crittografia dei dati in Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) con una chiave gestita dal cliente.|
|Azure Databricks|[Servizio di analisi veloce, facile e collaborativo basato su Apache Spark](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> L'utente deve impostare i criteri di accesso pertinenti per Key Vault in modo da consentire ai servizi corrispondenti di ottenere l'accesso a Key Vault.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md)
* [Configurare reti virtuali e firewall di Azure Key Vault](key-vault-network-security.md)
