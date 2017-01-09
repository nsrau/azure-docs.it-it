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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Creare una zona DNS di Azure con l'interfaccia della riga di comando

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)

Questo articolo illustra i passaggi per creare una zona DNS usando l'interfaccia della riga di comando di Azure tra piattaforme, disponibile per Windows, Mac e Linux. È anche possibile creare una zona DNS con PowerShell o il portale di Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* È necessario installare la versione più recente dell'interfaccia della riga di comando di Azure, disponibile per Windows, Linux o Mac. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## <a name="step-1---sign-in-and-create-a-resource-group"></a>Passaggio 1 - Accedere e creare un gruppo di risorse

### <a name="switch-cli-mode"></a>Passare alla modalità interfaccia della riga di comando

DNS di Azure usa Gestione risorse di Azure. Assicurarsi di passare alla modalità interfaccia della riga di comando per usare i comandi di Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali. Tenere presente che è possibile usare solo account OrgID.

```azurecli
azure login
```

### <a name="select-the-subscription"></a>Selezionare la sottoscrizione

Controllare le sottoscrizioni per l'account.

```azurecli
azure account list
```

Scegliere le sottoscrizioni ad Azure da usare.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrare il provider di risorse

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. Per poter usare il servizio DNS di Azure, la sottoscrizione di Azure deve essere registrata per l'uso di questo provider di risorse. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Passaggio 2: Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `azure network dns zone create` . Per visualizzare la guida per questo comando, digitare `azure network dns zone create -h`.

L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>Passaggio 3 - Verifica

### <a name="view-records"></a>Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

* Il record "Start of Authority" (SOA), presente nella directory principale di ogni zona DNS.
* I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

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

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando "azure network dns-record-set show" precedente. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

L'esempio seguente usa "DIG" per eseguire una query sul dominio contoso.com usando i server dei nomi assegnati per la zona DNS. La query deve puntare a un server dei nomi per cui si è usato il server dei nomi *@\< per la zona\>* e il nome della zona con "DIG".

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

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, [creare set di record e record](dns-getstarted-create-recordset-cli.md) per creare i record DNS per il dominio Internet.




<!--HONumber=Dec16_HO2-->


