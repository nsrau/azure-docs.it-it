---
ms.assetid: ''
title: Endpoint del servizio di rete virtuale per Azure Key Vault | Microsoft Docs
description: Panoramica degli endpoint del servizio di rete virtuale per Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 6e0029e051e418bc54471284547329a0b0a2e9cd
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50246655"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Endpoint del servizio di rete virtuale per Azure Key Vault

Gli endpoint del servizio di rete virtuale per Key Vault consentono di limitare l'accesso alla rete virtuale specificata e/o a un elenco di intervalli di indirizzi IPv4 (protocollo IP versione 4). A qualsiasi chiamante che si connetta all'insieme di credenziali delle chiavi dall'esterno di tali origini verrà negato l'accesso. Se il cliente ha scelto di consentire i "Servizi Microsoft attendibili", ad esempio Office 365 Exchange Online, Office 365 SharePoint Online, calcolo di Azure, Azure Resource Manager, Backup di Azure, ecc., le connessioni da tali servizi potranno attraversare il firewall. Naturalmente, questi chiamanti devono comunque presentare un token AAD valido e devono avere le autorizzazioni, configurate come criteri di accesso, per eseguire l'operazione richiesta. Altre informazioni tecniche sugli [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenari di utilizzo

È possibile configurare [reti virtuali e firewall di Key Vault](key-vault-network-security.md) per negare l'accesso al traffico da tutte le reti, incluso il traffico Internet, per impostazione predefinita. È possibile concedere l'accesso al traffico proveniente da reti virtuali specifiche di Azure e/o intervalli di indirizzi IP Internet pubblici, creando un limite di rete protetta per le applicazioni.

> [!NOTE]
> I firewall di Key Vault e le regole di rete virtuale si applicano SOLO al [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) dell'insieme di credenziali delle chiavi. Le operazioni del piano di controllo Key Vault, ad esempio le operazioni di creazione, eliminazione e modifica dell'insieme di credenziali delle chiavi, l'impostazione di criteri di accesso, la configurazione del firewall e delle regole di rete virtuale, non sono interessate dai firewall e dalle regole di rete virtuale.

Ad esempio,
* Se si usa Key Vault per archiviare chiavi di crittografia, segreti dell'applicazione, certificati e si vuole bloccare l'accesso all'insieme di credenziali delle chiavi dalla rete Internet pubblica.
* Si vuole bloccare l'accesso all'insieme di credenziali delle chiavi in modo che solo l'applicazione o un breve elenco di host designati possano connettesi all'insieme di credenziali delle chiavi
* Si ha un'applicazione in esecuzione nella rete virtuale di Azure e la rete virtuale è bloccata per tutto il traffico in ingresso e in uscita. L'applicazione deve comunque connettersi all'insieme di credenziali delle chiavi per recuperare i segreti o i certificati o usare le chiavi di crittografia.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurare firewall e reti virtuali Key Vault

Ecco i passaggi necessari per configurare i firewall e le reti virtuali. Questi passaggi sono gli stessi indipendentemente dall'interfaccia, ovvero PowerShell, interfaccia della riga di comando o portale di Azure, che si usa per configurare il firewall e le regole di rete virtuale.
1. Facoltativo ma consigliato: abilitare la [registrazione dell'insieme di credenziali chiave](key-vault-logging.md) per vedere log di accesso dettagliati. Ciò è utile nella diagnostica quando i firewall e le regole di rete virtuale impediscono l'accesso a un insieme di credenziali delle chiavi.
2. Abilitare "gli endpoint del servizio per l'insieme di credenziali delle chiavi" per le reti virtuali di destinazione e le subnet
3. Configurare i firewall e le regole di rete virtuale per un insieme di credenziali delle chiavi per limitare l'accesso a tale insieme di credenziali delle chiavi da specifiche reti virtuali, subnet e intervalli di indirizzi IPv4.
4. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi Microsoft attendibili, è necessario abilitare l'opzione per consentire ai "servizi di Azure attendibili" di connettersi all'insieme di credenziali delle chiavi.

Per istruzioni passo per passo dettagliate, vedere [Configurare reti virtuali e firewall di Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Quando le regole del firewall sono operative, tutte le operazioni del [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault possono essere eseguite SOLO se le richieste del chiamante hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. (Per altre informazioni sul confronto tra piano di gestione e piano dati, leggere [qui](../key-vault/key-vault-secure-your-key-vault.md#management-plane-access-control). Questo vale anche per l'accesso all'insieme di credenziali delle chiavi dal portale di Azure. Anche se un utente può accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbe non riuscire a elencare le chiavi/segreti/certificati se il computer client non è presente nell'elenco dei computer consentiti. Ciò influisce inoltre sul "selettore dell'insieme di credenziali delle chiavi" di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero riuscire a visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non riuscire a elencare le chiavi.


> [!NOTE]
> * È consentito un massimo di 127 regole di rete virtuale e 127 regole di IPv4. 
> * Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati. Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.
> * Le regole di rete IP sono consentite solo per gli indirizzi IP pubblici. Gli intervalli di indirizzi IP riservati per le reti private (come da definizione in RFC 1918) non sono consentiti nelle regole IP. Le reti private includono gli indirizzi che iniziano con *10.*\*, *172.16.*\* e *192.168.*\*. 
> * Attualmente sono supportati solo gli indirizzi IPv4.

## <a name="trusted-services"></a>Servizi attendibili
Ecco un elenco di servizi attendibili che sono autorizzati ad accedere a un insieme di credenziali delle chiavi se è abilitata l'opzione "Allow trusted services" (Consenti servizi attendibili).

|Servizio attendibile|Scenari di utilizzo|
| --- | --- |
|Servizio di distribuzione di Macchine virtuali di Azure|[Distribuire i certificati alle macchine virtuali dal Key Vault gestito dal cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Servizio di distribuzione dei modelli di Azure Resource Manager|[Passare valori protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Servizio di crittografia dei volumi di Crittografia dischi di Azure|Consentire l'accesso a BitLocker Key (VM Windows) o DM Passphrase (VM Linux) e la chiave di crittografia della chiave durante la distribuzione della macchina virtuale per abilitare la [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md)|
|Backup di Azure|Consentire il backup e ripristino di segreti e chiavi pertinenti durante il backup della macchina virtuale di Azure usando [Backup di Azure](../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online e SharePoint Online|Consentire l'accesso alla chiave cliente per la crittografia del servizio con [Chiave cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Consentire l'accesso alla chiave del tenant per [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection).|
|Servizi app|[Distribuzione di un certificato dell'app Web di Azure tramite Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|SQL di Azure|[Transparent Data Encryption con supporto BYOK (Bring Your Own Key) per il database SQL di Azure e Azure SQL Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Archiviazione di Azure|[Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Archivio Azure Data Lake|[Crittografia dei dati in Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) con la chiave gestita dal cliente|



> [!NOTE]
> I criteri di accesso pertinenti dell'insieme di credenziali delle chiavi devono essere impostati in modo da consentire ai servizi corrispondenti di ottenere l'accesso all'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md)
* [Configurare reti virtuali e firewall di Azure Key Vault](key-vault-network-security.md)
