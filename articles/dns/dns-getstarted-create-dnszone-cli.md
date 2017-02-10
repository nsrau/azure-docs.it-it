---
title: Creare una zona DNS con l&quot;interfaccia della riga di comando| Documentazione Microsoft
description: Informazioni su come creare le zone DNS in DNS di Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona DNS usando l&quot;interfaccia della riga di comando di Azure.
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Creare una zona DNS di Azure con l'interfaccia della riga di comando

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)

Questo articolo illustra i passaggi per creare una zona DNS usando l'interfaccia della riga di comando di Azure tra piattaforme, disponibile per Windows, Mac e Linux. È anche possibile creare una zona DNS con [Azure PowerShell](dns-getstarted-create-dnszone.md) o il [portale di Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `azure network dns zone create` . Per visualizzare la guida per questo comando, digitare `azure network dns zone create -h`.

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificare la zona DNS

### <a name="view-records"></a>Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

* Il record *Start of Authority* (SOA), presente nella directory principale di ogni zona DNS.
* I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md).

Per visualizzare questi record, usare `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> I set di record alla radice, o *vertice*, di una zona DNS usano **@** come nome del set di record.

### <a name="test-name-servers"></a>Verifica server dei nomi

È possibile verificare che la zona DNS sia presente sui server di nomi DNS di Azure usando gli strumenti DNS come nslookup, DIG o il cmdlet di PowerShell `Resolve-DnsName`.

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando `azure network dns record-set list`.

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




<!--HONumber=Feb17_HO1-->


