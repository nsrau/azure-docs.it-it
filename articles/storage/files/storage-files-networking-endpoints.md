---
title: Configurazione degli endpoint di rete di File di Azure | Microsoft Docs
description: Panoramica delle opzioni di rete per File di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: c144442ecd93ca87683179adef496a5d68cce98e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525898"
---
# <a name="configuring-azure-files-network-endpoints"></a>Configurazione degli endpoint di rete di File di Azure

File di Azure prevede due tipi principali di endpoint per l'accesso alle condivisioni file di Azure: 
- Endpoint pubblici, che hanno un indirizzo IP pubblico e sono accessibili da qualsiasi parte del mondo.
- Endpoint privati, presenti all'interno di una rete virtuale, che hanno un indirizzo IP privato all'interno dello spazio di indirizzi della rete virtuale.

Gli endpoint pubblici e privati sono disponibili nell'account di archiviazione di Azure. Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code.

Questo articolo è incentrato su come configurare gli endpoint di un account di archiviazione per l'accesso diretto alle condivisioni file di Azure. La maggior parte dei dettagli forniti in questo documento si applica anche al modo in cui Sincronizzazione file di Azure interagisce con gli endpoint pubblici e privati per l'account di archiviazione. Tuttavia, per altre informazioni sulle considerazioni di rete per una distribuzione di Sincronizzazione file di Azure, vedere [Configurazione del proxy di Sincronizzazione file di Azure e impostazioni del firewall](storage-sync-files-firewall-and-proxy.md).

Prima di seguire questa guida pratica, è consigliabile leggere [Considerazioni sulla rete per File di Azure](storage-files-networking-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- In questo articolo si presuppone che sia già stata creata una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Questo articolo presuppone che sia già stata creata una condivisione file di Azure in un account di archiviazione a cui si vuole connettersi dall'ambiente locale. Per informazioni su come creare una condivisione file di Azure, vedere [Creare una condivisione file di Azure](storage-how-to-create-file-share.md).
- Se si intende usare Azure PowerShell, [installare l'ultima versione](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="endpoint-configurations"></a>Configurazioni degli endpoint

È possibile configurare gli endpoint per limitare l'accesso di rete all'account di archiviazione. Per limitare l'accesso a un account di archiviazione consentendolo solo a una rete virtuale, sono disponibili due approcci:

- [Creare uno o più endpoint privati per l'account di archiviazione](#create-a-private-endpoint) e limitare tutto l'accesso all'endpoint pubblico. In questo modo si garantisce che solo il traffico originato dalle reti virtuali desiderate possa accedere alle condivisioni file di Azure nell'account di archiviazione.
- [Limitare l'endpoint pubblico a una o più reti virtuali](#restrict-public-endpoint-access). Questa operazione può essere eseguita usando una funzionalità della rete virtuale denominata *endpoint di servizio*. Quando si limita il traffico a un account di archiviazione tramite un endpoint del servizio, si sta ancora accedendo all'account di archiviazione tramite l'indirizzo IP pubblico, ma è possibile accedere solo dalle posizioni specificate nella configurazione.

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato

La creazione di un endpoint privato per l'account di archiviazione comporterà la distribuzione delle risorse di Azure seguenti:

- **Endpoint privato**: una risorsa di Azure che rappresenta l'endpoint privato dell'account di archiviazione. È possibile considerarlo come una risorsa che connette un account di archiviazione e un'interfaccia di rete.
- **Interfaccia di rete**: l'interfaccia di rete che gestisce un indirizzo IP privato all'interno della rete virtuale/subnet specificata. Si tratta esattamente della stessa risorsa che viene distribuita quando si distribuisce una macchina virtuale, ma, invece di essere assegnata a una VM, appartiene all'endpoint privato.
- **Zona DNS privato**: se non è stato ancora distribuito un endpoint privato per questa rete virtuale, verrà distribuita una nuova zona DNS privato. In questa zona DNS verrà inoltre creato un record A DNS. Se in questa rete virtuale è già stato distribuito un endpoint privato, nella zona DNS esistente verrà aggiunto un nuovo record A per l'account di archiviazione. La distribuzione di una zona DNS è facoltativa, ma è consigliata e diventa obbligatoria se le condivisioni file di Azure vengono montate con un'entità servizio di AD oppure se si usa l'API FileREST.

> [!Note]  
> Questo articolo usa il suffisso DNS dell'account di archiviazione per le aree pubbliche di Azure, `core.windows.net`. Questo commento si applica anche ai cloud sovrani di Azure, ad esempio il cloud di Azure per il governo degli Stati Uniti e il cloud di Azure Cina. È sufficiente sostituire i suffissi appropriati per l'ambiente in uso. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Se è disponibile una macchina virtuale all'interno della rete virtuale o è stato configurato l'inoltro DNS come descritto nell'articolo [Configurazione dell'inoltro DNS per File di Azure](storage-files-networking-dns.md), è possibile verificare se l'endpoint privato è stato configurato correttamente eseguendo i comandi seguenti in PowerShell, dalla riga di comando o nel terminale (per Windows, Linux o macOS). È necessario sostituire `<storage-account-name>` con il nome dell'account di archiviazione appropriato:

```
nslookup <storage-account-name>.file.core.windows.net
```

Se tutte le impostazioni sono corrette, verrà visualizzato l'output seguente, dove `192.168.0.5` è l'indirizzo IP privato dell'endpoint privato nella rete virtuale (output mostrato per Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Se è disponibile una macchina virtuale all'interno della rete virtuale o è stato configurato l'inoltro DNS come descritto nell'articolo [Configurazione dell'inoltro DNS per File di Azure](storage-files-networking-dns.md), è possibile verificare se l'endpoint privato è stato configurato correttamente eseguendo i comandi seguenti:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Se tutte le impostazioni sono corrette, verrà visualizzato l'output seguente, dove `192.168.0.5` è l'indirizzo IP privato dell'endpoint privato nella rete virtuale:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Se è disponibile una macchina virtuale all'interno della rete virtuale o è stato configurato l'inoltro DNS come descritto nell'articolo [Configurazione dell'inoltro DNS per File di Azure](storage-files-networking-dns.md), è possibile verificare se l'endpoint privato è stato configurato correttamente eseguendo i comandi seguenti:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Se tutte le impostazioni sono corrette, verrà visualizzato l'output seguente, dove `192.168.0.5` è l'indirizzo IP privato dell'endpoint privato nella rete virtuale. È comunque necessario usare storageaccount.file.core.windows.net per montare la condivisione file anziché il `privatelink` percorso.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="restrict-public-endpoint-access"></a>Limitare l'accesso all'endpoint pubblico

Per limitare l'accesso all'endpoint pubblico è prima necessario disabilitare l'accesso generale all'endpoint pubblico. La disabilitazione dell'accesso all'endpoint pubblico non influisca sugli endpoint privati. Dopo che l'endpoint pubblico è stato disabilitato, è possibile selezionare reti o indirizzi IP specifici che possono continuare ad accedervi. In generale, la maggior parte dei criteri firewall per un account di archiviazione limita l'accesso di rete a una o più reti virtuali.

#### <a name="disable-access-to-the-public-endpoint"></a>Disabilitare l'accesso all'endpoint pubblico

Se si disabilita l'accesso all'endpoint pubblico, l'account di archiviazione rimane comunque accessibile tramite i relativi endpoint privati. Altrimenti le richieste valide inviate all'endpoint pubblico dell'account di archiviazione verranno rifiutate. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Limitare l'accesso all'endpoint pubblico consentendolo solo a reti virtuali specifiche

Quando si limita l'account di archiviazione consentendolo solo a specifiche reti virtuali, si autorizzano le richieste inviate all'endpoint pubblico dall'interno di tali reti virtuali. Questa operazione può essere eseguita usando una funzionalità della rete virtuale denominata *endpoint di servizio*. Si può usare con o senza endpoint privati.

# <a name="portal"></a>[Portale](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Vedere anche

- [Considerazioni sulla rete per File di Azure](storage-files-networking-overview.md)
- [Configurazione dell'inoltro DNS per File di Azure](storage-files-networking-dns.md)
- [Configurazione della VPN da sito a sito per File di Azure](storage-files-configure-s2s-vpn.md)
