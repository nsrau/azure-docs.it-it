---
title: Usare gli endpoint privatiUse private endpoints
titleSuffix: Azure Storage
description: Panoramica degli endpoint privati per l'accesso sicuro agli account di archiviazione dalle reti virtuali.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299057"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Usare gli endpoint privati per Archiviazione di AzureUse private endpoints for Azure Storage

È possibile usare endpoint privati per gli account di archiviazione di Azure per consentire [ai](../../private-link/private-endpoint-overview.md) client in una rete virtuale (VNet) di accedere in modo sicuro ai dati tramite un [collegamento privato.](../../private-link/private-link-overview.md) L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per il servizio dell'account di archiviazione. Il traffico di rete tra i client nella rete virtuale e l'account di archiviazione attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione da Internet pubblico.

L'uso di endpoint privati per l'account di archiviazione consente di:Using private endpoints for your storage account enables you to:

- Proteggere l'account di archiviazione configurando il firewall di archiviazione per bloccare tutte le connessioni nell'endpoint pubblico per il servizio di archiviazione.
- Aumentare la sicurezza per la rete virtuale (VNet), consentendo di bloccare l'esfiltrazione dei dati dalla rete virtuale.
- Connettiti in modo sicuro agli account di archiviazione da reti locali che si connettono alla rete virtuale tramite [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoutes](../../expressroute/expressroute-locations.md) con peering privato.

## <a name="conceptual-overview"></a>Informazioni generali

![Panoramica degli endpoint privati per Archiviazione di AzureOverview of private endpoints for Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella [rete virtuale](../../virtual-network/virtual-networks-overview.md) (VNet). Quando si crea un endpoint privato per l'account di archiviazione, fornisce una connettività sicura tra i client nella rete virtuale e nell'archiviazione. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della rete virtuale. La connessione tra l'endpoint privato e il servizio di archiviazione utilizza un collegamento privato sicuro.

Le applicazioni nella rete virtuale possono connettersi al servizio di archiviazione tramite l'endpoint privato senza problemi, usando le stesse stringhe di connessione e meccanismi di **autorizzazione che utilizzerebbero in caso contrario.** Gli endpoint privati possono essere usati con tutti i protocolli supportati dall'account di archiviazione, inclusi REST e SMB.

Gli endpoint privati possono essere creati in subnet che [utilizzano gli endpoint del servizio.](../../virtual-network/virtual-network-service-endpoints-overview.md) I client in una subnet possono quindi connettersi a un account di archiviazione usando un endpoint privato, usando gli endpoint di servizio per accedere ad altri utenti.

Quando si crea un endpoint privato per un servizio di archiviazione nella rete virtuale, viene inviata una richiesta di consenso che il proprietario dell'account di archiviazione dovrà approvare. Se l'utente che richiede la creazione dell'endpoint privato è anche proprietario dell'account di archiviazione, questa richiesta di consenso viene approvata automaticamente.

I proprietari degli account di archiviazione possono gestire le richieste di consenso e gli endpoint privati, tramite la scheda '*Endpoint privati*' per l'account di archiviazione nel portale di [Azure](https://portal.azure.com).

> [!TIP]
> Se si vuole limitare l'accesso all'account di archiviazione solo tramite l'endpoint privato, configurare il firewall di archiviazione per negare o controllare l'accesso tramite l'endpoint pubblico.

È possibile proteggere l'account di archiviazione per accettare solo le connessioni dalla rete virtuale, [configurando il firewall di archiviazione](storage-network-security.md#change-the-default-network-access-rule) per negare l'accesso tramite l'endpoint pubblico per impostazione predefinita. Non è necessaria una regola del firewall per consentire il traffico da una rete virtuale che dispone di un endpoint privato, poiché il firewall di archiviazione controlla solo l'accesso tramite l'endpoint pubblico. Gli endpoint privati si basano invece sul flusso di consenso per concedere alle subnet l'accesso al servizio di archiviazione.

### <a name="private-endpoints-for-azure-storage"></a>Endpoint privati per Archiviazione di AzurePrivate endpoints for Azure Storage

Quando si crea l'endpoint privato, è necessario specificare l'account di archiviazione e il servizio di archiviazione a cui si connette. È necessario un endpoint privato separato per ogni servizio di archiviazione in un account di archiviazione a cui è necessario accedere, ovvero [BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [File](../files/storage-files-introduction.md), [Code](../queues/storage-queues-introduction.md), [Tabelle](../tables/table-storage-overview.md)o Siti [Web statici](../blobs/storage-blob-static-website.md).

> [!TIP]
> Creare un endpoint privato separato per l'istanza secondaria del servizio di archiviazione per migliorare le prestazioni di lettura negli account RA-GRS.

Per l'accesso in lettura all'area secondaria con un account di archiviazione configurato per l'archiviazione con ridondanza geografica, sono necessari endpoint privati separati per le istanze primarie e secondarie del servizio. Non è necessario creare un endpoint privato per l'istanza secondaria per il **failover.** L'endpoint privato si connetterà automaticamente alla nuova istanza primaria dopo il failover. Per altre informazioni sulle opzioni di ridondanza dell'archiviazione, vedere [Ridondanza di Archiviazione di Azure.For](storage-redundancy.md)more information about storage redundancy options, see Azure Storage redundancy .

Per informazioni più dettagliate sulla creazione di un endpoint privato per l'account di archiviazione, vedere gli articoli seguenti:For more detailed information on creating a private endpoint for your storage account, refer to the following articles:

- [Connettersi privatamente a un account di archiviazione dall'esperienza dell'account di archiviazione nel portale di AzureConnect privately to a storage account from the Storage Account experience in the Azure portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato usando il Centro collegamenti privati nel portale di AzureCreate a private endpoint using the Private Link Center in the Azure portal](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato con l'interfaccia della riga di comando di Azure](../../private-link/create-private-endpoint-cli.md)
- [Creare un endpoint privato usando Azure PowerShellCreate a private endpoint using Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Connessione agli endpoint privatiConnecting to private endpoints

I client in una rete virtuale che usano l'endpoint privato devono usare la stessa stringa di connessione per l'account di archiviazione, poiché i client si connettono all'endpoint pubblico. Ci affidiamo alla risoluzione DNS per instradare automaticamente le connessioni dalla rete virtuale all'account di archiviazione tramite un collegamento privato.

> [!IMPORTANT]
> Usare la stessa stringa di connessione per connettersi all'account di archiviazione usando endpoint privati, come si userà in caso contrario. Si prega di non connettersi all'account di archiviazione utilizzando il suo URL del sottodominio '*privatelink*'.

Per impostazione predefinita, creiamo una [zona DNS privata](../../dns/private-dns-overview.md) collegata alla rete virtuale con gli aggiornamenti necessari per gli endpoint privati. Tuttavia, se si utilizza il proprio server DNS, potrebbe essere necessario apportare ulteriori modifiche alla configurazione DNS. Nella sezione sulle [modifiche DNS](#dns-changes-for-private-endpoints) riportata di seguito vengono descritti gli aggiornamenti necessari per gli endpoint privati.

## <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato, il record di risorse CNAME DNS per l'account di archiviazione viene aggiornato a un alias in un sottodominio con il prefisso '*privatelink*'. Per impostazione predefinita, creiamo anche una [zona DNS privata,](../../dns/private-dns-overview.md)corrispondente al sottodominio '*privatelink*', con i record di risorse A DNS per gli endpoint privati.

Quando si risolve l'URL dell'endpoint di archiviazione dall'esterno della rete virtuale con l'endpoint privato, questo viene risolto nell'endpoint pubblico del servizio di archiviazione. Quando viene risolto dalla rete virtuale che ospita l'endpoint privato, l'URL dell'endpoint di archiviazione viene risolto nell'indirizzo IP dell'endpoint privato.

Per l'esempio illustrato in precedenza, i record di risorse DNS per l'account di archiviazione 'StorageAccountA', se risolti dall'esterno della rete virtuale che ospita l'endpoint privato, saranno:For the illustrated example above, the DNS resource records for the storage account 'StorageAccountA', when resolved from outside the VNet hosting the private endpoint, will be:

| Nome                                                  | Type  | valore                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<endpoint pubblico del servizio di archiviazioneStorage service public endpoint\>                   |
| \<endpoint pubblico del servizio di archiviazioneStorage service public endpoint\>                   | Una      | \<indirizzo IP pubblico del servizio di archiviazione\>                 |

Come accennato in precedenza, è possibile negare o controllare l'accesso per i client esterni alla rete virtuale tramite l'endpoint pubblico utilizzando il firewall di archiviazione.

I record di risorse DNS per StorageAccountA, quando vengono risolti da un client nella rete virtuale che ospita l'endpoint privato, saranno:

| Nome                                                  | Type  | valore                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | Una      | 10.1.1.5                                              |

Questo approccio consente l'accesso all'account di archiviazione **usando la stessa stringa** di connessione per i client nella rete virtuale che ospitano gli endpoint privati, nonché i client esterni alla rete virtuale.

Se si utilizza un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo per l'endpoint dell'account di archiviazione nell'indirizzo IP dell'endpoint privato. È necessario configurare il server DNS per delegare il sottodominio del collegamento privato alla zona DNS privata per la rete virtuale oppure configurare i record A per '*StorageAccountA.privatelink.blob.core.windows.net*' con l'indirizzo IP dell'endpoint privato.

> [!TIP]
> Quando si usa un server DNS personalizzato o locale, è necessario configurare il server DNS per risolvere il nome dell'account di archiviazione nel sottodominio 'privatelink' nell'indirizzo IP dell'endpoint privato. A tale scopo, è possibile delegare il sottodominio 'privatelink' alla zona DNS privata della rete virtuale oppure configurare la zona DNS sul server DNS e aggiungere i record A DNS.

I nomi delle zone DNS consigliate per gli endpoint privati per i servizi di archiviazione sono:The recommended DNS zone names for private endpoints for storage services are:

| Servizio di archiviazione        | Nome zona                            |
| :--------------------- | :----------------------------------- |
| Servizio BLOB           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Servizio file           | `privatelink.file.core.windows.net`  |
| Servizio di coda          | `privatelink.queue.core.windows.net` |
| Servizio tabelle          | `privatelink.table.core.windows.net` |
| Siti Web statici        | `privatelink.web.core.windows.net`   |

Per ulteriori informazioni sulla configurazione di un server DNS personalizzato per supportare gli endpoint privati, vedere gli articoli seguenti:

- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per gli endpoint privati](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemi noti

Tenere presente i problemi noti seguenti sugli endpoint privati per Archiviazione di Azure.Keep in mind the following known issues about private endpoints for Azure Storage.

### <a name="copy-blob-support"></a>Copia supporto BLOB

Se l'account di archiviazione è protetto da un firewall e si accede all'account tramite endpoint privati, tale account non può essere utilizzato come origine di un'operazione [Copia BLOB.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Vincoli di accesso all'archiviazione per i client nelle reti virtuali con endpoint privatiStorage access constraints for clients in VNets with private endpoints

I client nelle reti virtuali con endpoint privati esistenti devono far fronte a vincoli quando accedono ad altri account di archiviazione con endpoint privati. Si supponga, si supponga che una rete virtuale N1 disponga di un endpoint privato per un account di archiviazione A1 per l'archiviazione BLOB. Se l'account di archiviazione A2 ha un endpoint privato in una rete virtuale N2 per l'archiviazione BLOB, i client in VNet N1 devono accedere anche all'archiviazione BLOB nell'account A2 usando un endpoint privato. Se l'account di archiviazione A2 non dispone di endpoint privati per l'archiviazione BLOB, i client in VNet N1 possono accedere all'archiviazione BLOB in tale account senza un endpoint privato.

Questo vincolo è il risultato delle modifiche DNS apportate quando l'account A2 crea un endpoint privato.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regole del gruppo di sicurezza di rete per le subnet con endpoint privati

Attualmente, non è possibile configurare le regole [del gruppo](../../virtual-network/security-overview.md) di sicurezza di rete (NSG) e le route definite dall'utente per gli endpoint privati. Le regole del gruppo di sicurezza di rete applicate alla subnet che ospita l'endpoint privato vengono applicate all'endpoint privato. Una soluzione limitata per questo problema consiste nell'implementare le regole di accesso per gli endpoint privati nelle subnet di origine, anche se questo approccio potrebbe richiedere un sovraccarico di gestione più elevato.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i firewall e le reti virtuali di Archiviazione di AzureConfigure Azure Storage firewalls and virtual networks](storage-network-security.md)
- [Consigli sulla sicurezza per l'archiviazione BLOBSecurity recommendations for Blob storage](../blobs/security-recommendations.md)
