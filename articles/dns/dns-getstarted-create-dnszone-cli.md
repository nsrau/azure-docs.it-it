---
title: Creare una zona DNS con l&quot;interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come creare le zone DNS in DNS di Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona DNS usando l&quot;interfaccia della riga di comando di Azure 2.0.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Creare una zona DNS di Azure con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-create-dnszone-cli.md)

Questo articolo illustra i passaggi per creare una zona DNS usando l'interfaccia della riga di comando di Azure tra piattaforme, disponibile per Windows, Mac e Linux. È anche possibile creare una zona DNS con [Azure PowerShell](dns-getstarted-create-dnszone.md) o il [portale di Azure](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md): l'interfaccia della riga di comando per il modello di distribuzione classico e di gestione delle risorse.
* [Interfaccia della riga di comando di Azure 2.0](dns-getstarted-create-dnszone-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse.

## <a name="introduction"></a>Introduzione

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configurare l'interfaccia della riga di comando di Azure 2.0 per DNS di Azure

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

* Installare la versione più recente dell'interfaccia della riga di comando di Azure, disponibile per Windows, Linux o Mac. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Aprire una finestra della console ed eseguire l'autenticazione con le credenziali. Per altre informazioni, vedere Accedere ad Azure dall'interfaccia della riga di comando di Azure

```azurecli
az login
```

### <a name="select-the-subscription"></a>Selezionare la sottoscrizione

Controllare le sottoscrizioni per l'account.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Scegliere le sottoscrizioni ad Azure da usare.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `az network dns zone create` . Per visualizzare la guida per questo comando, digitare `az network dns zone create --help`.

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificare la zona DNS

### <a name="view-records"></a>Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

* Il record *Start of Authority* (SOA), presente nella directory principale di ogni zona DNS.
* I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md).

Per visualizzare questi record, usare `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

La risposta per `az network dns record-set list` è illustrata di seguito:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> I set di record alla radice, o *vertice*, di una zona DNS usano **@** come nome del set di record.

### <a name="test-name-servers"></a>Verifica server dei nomi

È possibile verificare che la zona DNS sia presente sui server di nomi DNS di Azure usando gli strumenti DNS come nslookup, DIG o il cmdlet di PowerShell `Resolve-DnsName`.

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando `az network dns record-set list`.

L'esempio seguente usa "dig" per eseguire una query sul dominio contoso.com usando i server dei nomi assegnati alla zona DNS. Assicurarsi di sostituire i valori corretti per la propria zona.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, [creare record e set di record DNS](dns-getstarted-create-recordset-cli.md) nella zona.


