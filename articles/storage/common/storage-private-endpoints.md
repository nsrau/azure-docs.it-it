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
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949469"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Uso di endpoint privati per archiviazione di Azure (anteprima)

Archiviazione di Azure consente di usare [endpoint privati](../../private-link/private-endpoint-overview.md) per i client in una rete virtuale (VNet) per accedere in modo sicuro ai dati in un account di archiviazione tramite un [collegamento privato](../../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello spazio di indirizzi VNet per il servizio dell'account di archiviazione. Il traffico di rete tra i client nella VNet e l'account di archiviazione attraversa il VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di endpoint privati per l'account di archiviazione consente di:
- Proteggere l'account di archiviazione configurando il firewall di archiviazione per bloccare tutte le connessioni nell'endpoint pubblico per il servizio di archiviazione.
- Aumenta la sicurezza per la rete virtuale (VNet), consentendo di bloccare exfiltration di dati da VNet.
- Connettersi in modo sicuro agli account di archiviazione da reti locali che si connettono alla VNet tramite [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../../expressroute/expressroute-locations.md) con peering privato.

## <a name="conceptual-overview"></a>Panoramica concettuale
![Panoramica degli endpoint privati per archiviazione di Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Un endpoint privato è un'interfaccia di rete speciale nella [rete virtuale](../../virtual-network/virtual-networks-overview.md) (VNet) per un servizio di Azure. Fornisce connettività sicura tra i client nella VNet e l'account di archiviazione. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet. La connessione tra l'endpoint privato e il servizio di archiviazione usa un collegamento privato protetto.

Le applicazioni in VNet possono connettersi al servizio di archiviazione tramite l'endpoint privato senza interruzioni, usando le stesse stringhe di connessione e i meccanismi di autorizzazione che verrebbero usati in caso contrario. Gli endpoint privati possono essere usati con tutti i protocolli supportati dall'account di archiviazione, inclusi REST e SMB.

Quando si crea un endpoint privato per un servizio di archiviazione in VNet, viene inviata una richiesta di consenso per l'approvazione al proprietario dell'account di archiviazione. Se l'utente che richiede la creazione dell'endpoint privato è anche un proprietario dell'account di archiviazione, questa richiesta di consenso viene approvata automaticamente.

I proprietari dell'account di archiviazione possono approvare o rifiutare le richieste di consenso, nonché visualizzare o gestire gli endpoint privati, tramite la scheda "endpoint privati" per l'account di archiviazione nell' [portale di Azure](https://portal.azure.com).

È possibile proteggere l'account di archiviazione in modo che accetti solo le connessioni da VNet, [configurando il firewall di archiviazione](storage-network-security.md#change-the-default-network-access-rule) per negare l'accesso tramite il relativo endpoint pubblico per impostazione predefinita. Non è necessaria una regola del firewall di archiviazione per consentire il traffico da un VNet con un endpoint privato, perché le regole del firewall di archiviazione si applicano solo al relativo endpoint pubblico. Gli endpoint privati si basano invece sul flusso di autorizzazioni per concedere alle subnet l'accesso al servizio di archiviazione.

### <a name="private-endpoints-for-storage-service"></a>Endpoint privati per il servizio di archiviazione

Quando si crea l'endpoint privato, è necessario specificare l'account di archiviazione e il servizio di archiviazione a cui si connette. È necessario un endpoint privato per ogni servizio di archiviazione in un account di archiviazione a cui è necessario l'accesso, ovvero [BLOB](../blobs/storage-blobs-overview.md), [Data Lake storage Gen2](../blobs/data-lake-storage-introduction.md), [file](../files/storage-files-introduction.md), [Code](../queues/storage-queues-introduction.md), [tabelle](../tables/table-storage-overview.md)o [siti web statici](../blobs/storage-blob-static-website.md).

Per garantire la disponibilità in lettura per un [account di archiviazione con ridondanza geografica e accesso in lettura](storage-redundancy-grs.md#read-access-geo-redundant-storage), sono necessari endpoint privati distinti per le istanze primarie e secondarie del servizio.

#### <a name="resources"></a>resources

Per informazioni più dettagliate sulla creazione di un endpoint privato per l'account di archiviazione, fare riferimento agli articoli seguenti:

- [Connettersi privatamente a un account di archiviazione dall'esperienza dell'account di archiviazione nell'portale di Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Creare un endpoint privato usando il centro collegamenti privati nel portale di Azure](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato usando l'interfaccia della riga di comando](../../private-link/create-private-endpoint-cli.md)
- [Creare un endpoint privato usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato per un servizio di archiviazione, si aggiorna il record di risorse DNS CNAME per l'endpoint di archiviazione in un alias in un sottodominio con il prefisso "*privatelink*". Per impostazione predefinita, viene creata anche una [zona DNS privata](../../dns/private-dns-overview.md) collegata a VNet. Questa zona DNS privata corrisponde al sottodominio con il prefisso "*privatelink*" e contiene i record di risorse DNS a per gli endpoint privati.

Quando si risolve l'URL dell'endpoint di archiviazione dall'esterno della VNet in cui viene creato l'endpoint privato, questo viene comunque risolto nell'endpoint pubblico per il servizio di archiviazione. Quando viene risolto da VNet che ospita l'endpoint privato, l'URL dell'endpoint di archiviazione viene risolto nell'indirizzo IP dell'endpoint privato.

Per l'esempio illustrato in precedenza, i record di risorse DNS per l'account di archiviazione ' StorageAccountA ', quando risolti dall'esterno della VNet che ospita l'endpoint privato, saranno:

| name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<endpoint pubblico\>                                   |
| \<endpoint pubblico\>                                   | A     | Indirizzo IP pubblico del servizio di archiviazione \<\>                 |

Come indicato in precedenza, è possibile negare tutti gli accessi tramite l'endpoint pubblico usando il firewall di archiviazione.

I record di risorse DNS per StorageAccountA, in caso di risoluzione da parte di un client in VNet che ospita l'endpoint privato, saranno:

| name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Questo approccio consente di accedere all'account di archiviazione usando la stessa stringa di connessione del VNet che ospita gli endpoint privati, oltre ai client esterni al VNet. È possibile usare il firewall di archiviazione per negare l'accesso a tutti i client all'esterno di VNet.

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemi noti

### <a name="copy-blob-failures"></a>Copia errori BLOB

Attualmente, i comandi di [copia BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) emessi per gli account di archiviazione a cui si accede tramite endpoint privati hanno esito negativo quando l'account di archiviazione di origine è protetto da un firewall.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Vincoli di accesso alle archiviazione per i client in reti virtuali con endpoint privati

Client in reti virtuali che dispongono di vincoli di endpoint privato di archiviazione esistenti durante l'accesso ad altri account di archiviazione con endpoint privati. Si supponga, ad esempio, che un VNet N1 disponga di un endpoint privato per un account di archiviazione a1 per, ad esempio, il servizio BLOB. Se l'account di archiviazione a2 ha un endpoint privato in un VNet N2 per il servizio BLOB, i client in VNet N1 devono accedere anche al servizio BLOB nell'account a2 usando un endpoint privato. Se per l'account di archiviazione a2 non sono presenti endpoint privati per il servizio BLOB, i client in VNet N1 possono accedere al servizio BLOB senza un endpoint privato.

Questo vincolo è il risultato delle modifiche DNS apportate quando l'account a2 crea un endpoint privato.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>Regole NSG per le subnet con endpoint privati

Al momento non è possibile configurare le regole del [gruppo di sicurezza di rete](../../virtual-network/security-overview.md) (NSG) per le subnet con endpoint privati. Una soluzione alternativa limitata a questo problema consiste nell'implementare le regole di accesso per gli endpoint privati nelle subnet di origine, sebbene questo approccio potrebbe richiedere un sovraccarico di gestione superiore.
