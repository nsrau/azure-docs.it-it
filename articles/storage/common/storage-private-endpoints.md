---
title: Uso di endpoint privati con archiviazione di Azure | Microsoft Docs
description: Panoramica degli endpoint privati per l'accesso sicuro agli account di archiviazione dalle reti virtuali.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: e9781d9c277d19257d9b00bea3106adb3b04ffd6
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672511"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Uso di endpoint privati per archiviazione di Azure (anteprima)

È possibile usare [endpoint privati](../../private-link/private-endpoint-overview.md) per gli account di archiviazione di Azure per consentire ai client in una rete virtuale (VNet) di accedere in modo sicuro ai dati tramite un [collegamento privato](../../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello spazio di indirizzi VNet per il servizio dell'account di archiviazione. Il traffico di rete tra i client nella VNet e l'account di archiviazione attraversa il VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di endpoint privati per l'account di archiviazione consente di:
- Proteggere l'account di archiviazione configurando il firewall di archiviazione per bloccare tutte le connessioni nell'endpoint pubblico per il servizio di archiviazione.
- Aumentare la sicurezza per la rete virtuale (VNet), consentendo di bloccare exfiltration di dati da VNet.
- Connettersi in modo sicuro agli account di archiviazione da reti locali che si connettono alla VNet tramite [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../../expressroute/expressroute-locations.md) con peering privato.

## <a name="conceptual-overview"></a>Panoramica concettuale
![Panoramica degli endpoint privati per archiviazione di Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella [rete virtuale](../../virtual-network/virtual-networks-overview.md) (VNet). Quando si crea un endpoint privato per l'account di archiviazione, fornisce connettività sicura tra i client nella VNet e l'archiviazione. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet. La connessione tra l'endpoint privato e il servizio di archiviazione usa un collegamento privato protetto.

Le applicazioni in VNet possono connettersi al servizio di archiviazione tramite l'endpoint privato senza interruzioni, **usando le stesse stringhe di connessione e i meccanismi di autorizzazione che verrebbero usati in caso contrario**. Gli endpoint privati possono essere usati con tutti i protocolli supportati dall'account di archiviazione, inclusi REST e SMB.

Gli endpoint privati possono essere creati in subnet che usano gli [endpoint del servizio](/azure/virtual-network/virtual-network-service-endpoints-overview.md). I client in una subnet possono quindi connettersi a un account di archiviazione usando un endpoint privato, usando gli endpoint di servizio per accedere ad altri utenti.

Quando si crea un endpoint privato per un servizio di archiviazione nella rete virtuale, viene inviata una richiesta di consenso che il proprietario dell'account di archiviazione dovrà approvare. Se l'utente che richiede la creazione dell'endpoint privato è anche un proprietario dell'account di archiviazione, questa richiesta di consenso viene approvata automaticamente.

I proprietari dell'account di archiviazione possono gestire le richieste di consenso e gli endpoint privati, tramite la scheda "*endpoint privati*" per l'account di archiviazione nell' [portale di Azure](https://portal.azure.com).

> [!TIP]
> Se si vuole limitare l'accesso all'account di archiviazione solo tramite l'endpoint privato, configurare il firewall di archiviazione per negare o controllare l'accesso tramite l'endpoint pubblico.

È possibile proteggere l'account di archiviazione in modo che accetti solo le connessioni da VNet, [configurando il firewall di archiviazione](storage-network-security.md#change-the-default-network-access-rule) per negare l'accesso tramite il relativo endpoint pubblico per impostazione predefinita. Non è necessaria una regola del firewall per consentire il traffico da un VNet con un endpoint privato, perché il firewall di archiviazione controlla solo l'accesso tramite l'endpoint pubblico. Gli endpoint privati si basano invece sul flusso di autorizzazioni per concedere alle subnet l'accesso al servizio di archiviazione.

### <a name="private-endpoints-for-storage-service"></a>Endpoint privati per il servizio di archiviazione

Quando si crea l'endpoint privato, è necessario specificare l'account di archiviazione e il servizio di archiviazione a cui si connette. È necessario un endpoint privato separato per ogni servizio di archiviazione in un account di archiviazione a cui è necessario accedere, ovvero [BLOB](../blobs/storage-blobs-overview.md), [Data Lake storage Gen2](../blobs/data-lake-storage-introduction.md), [file](../files/storage-files-introduction.md), [Code](../queues/storage-queues-introduction.md), [tabelle](../tables/table-storage-overview.md)o [siti web statici](../blobs/storage-blob-static-website.md).

> [!TIP]
> Creare un endpoint privato separato per l'istanza secondaria del servizio di archiviazione per migliorare le prestazioni di lettura sugli account RA-GRS.

Per la disponibilità in lettura in un [account di archiviazione con ridondanza geografica e accesso in lettura](storage-redundancy-grs.md#read-access-geo-redundant-storage), sono necessari endpoint privati distinti per le istanze primarie e secondarie del servizio. Non è necessario creare un endpoint privato per l'istanza secondaria per il **failover**. L'endpoint privato si connette automaticamente alla nuova istanza primaria dopo il failover.

#### <a name="resources"></a>resources

Per informazioni più dettagliate sulla creazione di un endpoint privato per l'account di archiviazione, fare riferimento agli articoli seguenti:

- [Connettersi privatamente a un account di archiviazione dall'esperienza dell'account di archiviazione nell'portale di Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato usando il centro collegamenti privati nel portale di Azure](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato usando l'interfaccia della riga di comando](../../private-link/create-private-endpoint-cli.md)
- [Creare un endpoint privato usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Connessione agli endpoint privati

I client in un VNet che usa l'endpoint privato devono usare la stessa stringa di connessione per l'account di archiviazione, perché i client si connettono all'endpoint pubblico. Si fa affidamento sulla risoluzione DNS per instradare automaticamente le connessioni da VNet all'account di archiviazione tramite un collegamento privato.

> [!IMPORTANT]
> Usare la stessa stringa di connessione per connettersi all'account di archiviazione usando endpoint privati, come si farebbe diversamente. Non connettersi all'account di archiviazione usando l'URL del sottodominio '*privatelink*'.

Per impostazione predefinita, viene creata una [zona DNS privata](../../dns/private-dns-overview.md) collegata a VNet con gli aggiornamenti necessari per gli endpoint privati. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario apportare altre modifiche alla configurazione DNS. La sezione sulle [modifiche DNS](#dns-changes-for-private-endpoints) riportata di seguito descrive gli aggiornamenti necessari per gli endpoint privati.

## <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato, il record di risorse DNS CNAME per l'account di archiviazione viene aggiornato a un alias in un sottodominio con il prefisso '*privatelink*'. Per impostazione predefinita, si crea anche una [zona DNS privata](../../dns/private-dns-overview.md), che corrisponde al sottodominio "*privatelink*", con i record di risorse DNS a per gli endpoint privati.

Quando si risolve l'URL dell'endpoint di archiviazione dall'esterno del VNet con l'endpoint privato, questo viene risolto nell'endpoint pubblico del servizio di archiviazione. Quando viene risolto da VNet che ospita l'endpoint privato, l'URL dell'endpoint di archiviazione viene risolto nell'indirizzo IP dell'endpoint privato.

Per l'esempio illustrato in precedenza, i record di risorse DNS per l'account di archiviazione ' StorageAccountA ', quando risolti dall'esterno della VNet che ospita l'endpoint privato, saranno:

| name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | endpoint pubblico del servizio di archiviazione \<\>                   |
| endpoint pubblico del servizio di archiviazione \<\>                   | A     | Indirizzo IP pubblico del servizio di archiviazione \<\>                 |

Come indicato in precedenza, è possibile negare o controllare l'accesso per i client esterni a VNet tramite l'endpoint pubblico usando il firewall di archiviazione.

I record di risorse DNS per StorageAccountA, in caso di risoluzione da parte di un client in VNet che ospita l'endpoint privato, saranno:

| name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Questo approccio consente di accedere all'account di archiviazione **usando la stessa stringa di connessione** per i client in VNet che ospitano gli endpoint privati, nonché i client esterni al VNet.

Se si usa un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo per l'endpoint dell'account di archiviazione nell'indirizzo IP dell'endpoint privato. È necessario configurare il server DNS per delegare il sottodominio di collegamento privato alla zona DNS privata per la VNet o configurare i record A per "*StorageAccountA.privatelink.blob.Core.Windows.NET*" con l'indirizzo IP dell'endpoint privato.

> [!TIP]
> Quando si usa un server DNS personalizzato o locale, è necessario configurare il server DNS per risolvere il nome dell'account di archiviazione nel sottodominio "privatelink" all'indirizzo IP dell'endpoint privato. A tale scopo, è possibile delegare il sottodominio "privatelink" alla zona DNS privata del VNet o configurare la zona DNS nel server DNS e aggiungere i record A DNS.

I nomi di zona DNS consigliati per gli endpoint privati per i servizi di archiviazione sono:

| Servizio di archiviazione        | Nome zona                            |
| :--------------------- | :----------------------------------- |
| Servizio BLOB           | `privatelink.blob.core.windows.net`  |
| Archiviazione Data Lake di seconda generazione | `privatelink.dfs.core.windows.net`   |
| Servizio file           | `privatelink.file.core.windows.net`  |
| Servizio di accodamento          | `privatelink.queue.core.windows.net` |
| Servizio tabelle          | `privatelink.table.core.windows.net` |
| Siti web statici        | `privatelink.web.core.windows.net`   |

#### <a name="resources"></a>resources

Per ulteriori informazioni sulla configurazione del server DNS per supportare endpoint privati, fare riferimento agli articoli seguenti:

- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per endpoint privati](/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemi noti

### <a name="copy-blob-support"></a>Copia supporto BLOB

Durante l'anteprima, non sono supportati i comandi [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) emessi per gli account di archiviazione a cui si accede tramite endpoint privati quando l'account di archiviazione di origine è protetto da un firewall.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Vincoli di accesso alle archiviazione per i client in reti virtuali con endpoint privati

I client in reti virtuali con endpoint privati esistenti devono affrontare vincoli per l'accesso ad altri account di archiviazione con endpoint privati. Si supponga, ad esempio, che un VNet N1 disponga di un endpoint privato per un account di archiviazione a1 per, ad esempio, il servizio BLOB. Se l'account di archiviazione a2 ha un endpoint privato in un VNet N2 per il servizio BLOB, i client in VNet N1 devono accedere anche al servizio BLOB dell'account a2 usando un endpoint privato. Se per l'account di archiviazione a2 non sono presenti endpoint privati per il servizio BLOB, i client in VNet N1 possono accedere al servizio BLOB senza un endpoint privato.

Questo vincolo è il risultato delle modifiche DNS apportate quando l'account a2 crea un endpoint privato.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regole del gruppo di sicurezza di rete per le subnet con endpoint privati

Attualmente, non è possibile configurare regole del [gruppo di sicurezza di rete](../../virtual-network/security-overview.md) (NSG) per le subnet con endpoint privati. Una soluzione alternativa limitata a questo problema consiste nell'implementare le regole di accesso per gli endpoint privati nelle subnet di origine, sebbene questo approccio potrebbe richiedere un sovraccarico di gestione superiore.
