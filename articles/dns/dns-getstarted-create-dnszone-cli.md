---
title: Creare una zona DNS con l&quot;interfaccia della riga di comando| Documentazione Microsoft
description: Informazioni su come creare zone DNS per il DNS di Azure dettagliato per avviare l&quot;hosting del dominio DNS utilizzando CLI
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e2b93d8788f8f36ff6bc04a33d1f35a86cd49973


---
# <a name="create-an-azure-dns-zone-using-cli"></a>Creare una zona DNS di Azure con l'interfaccia della riga di comando
> [!div class="op_single_selector"]
> * [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)
> 
> 

Questo articolo illustra i passaggi per creare una zona DNS con l'interfaccia della riga di comando. È anche possibile creare una zona DNS con PowerShell o il portale di Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Per queste istruzioni viene usata l'interfaccia della riga di comando di Microsoft Azure. Assicurarsi di eseguire l'aggiornamento alla versione più recente (0.9.8 o successiva) dell'interfaccia della riga di comando di Azure per usare i comandi del servizio DNS di Azure. Digitare `azure -v` per verificare quale versione dell'interfaccia della riga di comando di Azure è attualmente installata nel computer.

## <a name="step-1-set-up-azure-cli"></a>Passaggio 1: Configurare l'interfaccia della riga di comando di Azure
### <a name="1-install-azure-cli"></a>1. Installare l'interfaccia da riga di comando di Azure
È possibile installare l'interfaccia della riga di comando di Azure per Windows, Linux o MAC. È necessario completare i passaggi seguenti prima di poter gestire DNS di Azure tramite l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). I comandi di DNS richiedono la versione 0.9.8 o successiva dell'interfaccia della riga di comando di Azure.

Per trovare tutti i comandi del provider di rete nell'interfaccia della riga di comando di Azure, usare il comando seguente:

    azure network

### <a name="2-switch-cli-mode"></a>2. Passare alla modalità interfaccia della riga di comando
DNS di Azure usa Gestione risorse di Azure. Assicurarsi di passare alla modalità interfaccia della riga di comando per usare i comandi di Azure Resource Manager.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. Accedere con l'account Azure
Verrà richiesto di eseguire l'autenticazione con le proprie credenziali. Tenere presente che è possibile usare solo account aziendali (ORGID).

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Selezionare la sottoscrizione
Scegliere le sottoscrizioni ad Azure da usare.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. Creare un gruppo di risorse
Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. Registra
Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. Per poter usare il servizio DNS di Azure, la sottoscrizione di Azure deve essere registrata per l'uso di questo provider di risorse. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

    azure provider register --namespace Microsoft.Network


## <a name="step-2-create-a-dns-zone"></a>Passaggio 2: Creare una zona DNS
Una zona DNS viene creata utilizzando il comando `azure network dns zone create` . È possibile creare facoltativamente una zona DNS con tag. I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

Nel servizio DNS di Azure i nomi delle zone devono essere specificati senza la terminazione **"."**, ad esempio "**contoso.com**" invece di "**contoso.com.**".

### <a name="to-create-a-dns-zone"></a>Per creare una zona DNS
L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*.

Usare l'esempio per creare la zona DNS, sostituendo i valori con quelli personalizzati.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Per creare una zona DNS e tag.
L'interfaccia della riga di comando DNS di Azure supporta i tag nelle zone DNS specificate con il parametro facoltativo *-Tag* . L'esempio seguente illustra come creare una zona DNS con due tag, project = demo ed env = test.

Usare l'esempio seguente per creare una zona DNS e i tag, sostituendo i valori con quelli personalizzati.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Visualizzare i record
La creazione di una zona DNS comporta anche la creazione dei record DNS seguenti:

* Il record "Start of Authority" (SOA), presente nella directory principale di ogni zona DNS.
* I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

Per visualizzare questi record, usare `azure network dns-record-set show`.<BR>
*Sintassi: network dns record-set show <resource-group> <dns-zone-name><name> <type>*

Nell'esempio seguente l'esecuzione del comando con il gruppo di risorse *myresourcegroup*, il nome del set di record *"@"* (per un record radice) e il tipo *SOA* produrrà l'output seguente:

    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
 Per visualizzare i record NS creati con la zona, usare il comando seguente:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

> [!NOTE]
> I set di record alla radice (o *vertice*) di una zona DNS usano **@** come nome del set di record.
> 
> 

## <a name="test"></a>Test
È possibile testare la zona DNS usando strumenti DNS come nslookup, DIG o il cmdlet di PowerShell `Resolve-DnsName` .

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando "azure network dns-record-set show" precedente. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

L'esempio seguente usa DIG per eseguire una query sul dominio contoso.com usando i server dei nomi assegnati per la zona DNS. La query deve puntare a un server dei nomi per cui si è usato *@<name server for the zone>* e al nome della zona tramite DIG.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
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
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset-cli.md) per avviare la risoluzione dei nomi per il dominio Internet.




<!--HONumber=Nov16_HO2-->


