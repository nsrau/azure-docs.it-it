---
title: Gestire set di record e record DNS in DNS di Azure usando l'interfaccia della riga di comando | Microsoft Docs
description: Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi di CLI per le operazioni sui set di record e i record.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: jtuliani

---
# Gestire record e set di record DNS con l'interfaccia della riga di comando
> [!div class="op_single_selector"]
> * [Portale di Azure](dns-operations-recordsets-portal.md)
> * [Interfaccia della riga di comando di Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)
> 
> 

Questo articolo descrive come gestire i set di record e i record per la zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure.

È importante comprendere la differenza tra i set di record DNS e i singoli record DNS. Un set di record è una raccolta di record con lo stesso nome e lo stesso tipo in una zona. Per altre informazioni, vedere [Informazioni sui set di record e sui record](dns-getstarted-create-recordset-cli.md).

## Configurare l'interfaccia della riga di comando multipiattaforma di Azure
DNS di Azure è un servizio solo di Gestione risorse di Azure. Non dispone di un'API Gestione dei servizi di Azure. Assicurarsi che l'interfaccia della riga di comando di Azure sia configurata per l'uso della modalità Resource Manager usando il comando `azure config mode arm`.

Se viene visualizzato il messaggio **errore: "dns" non è un comando di Azure**, probabilmente è perché si sta usando l’interfaccia della riga di comando di Azure in modalità Azure Service Management e non in modalità Gestione risorse.

## Creare un nuovo set di record e record
Per creare un set di record nel portale di Azure, vedere [Creare record e set di record](dns-getstarted-create-recordset-cli.md).

## Recuperare un set di record
Per recuperare un set di record esistente, usare `azure network dns record-set show`. Specificare il gruppo di risorse, il nome della zona, il nome relativo del set di record e il tipo di record. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati.

    azure network dns record-set show myresourcegroup contoso.com www A


## Elencare i set di record
È possibile elencare tutti i record in una zona DNS usando il comando `azure network dns record-set list`. È necessario specificare il nome del gruppo di risorse e il nome della zona.

### Per elencare tutti i set di record
Questo esempio restituisce tutti i set di record, indipendentemente dal nome o dal tipo di record:

    azure network dns record-set list myresourcegroup contoso.com

### Per elencare i set di record di un determinato tipo
Questo esempio restituisce tutti i set di record corrispondenti al tipo di record specificato, in questo caso, i record "A":

    azure network dns record-set list myresourcegroup contoso.com A


## Aggiungere un record a un set di record
Aggiungere record al set di record usando il comando `azure network dns record-set add-record`. I parametri per l'aggiunta di record a un set di record variano a seconda del tipo del set di record che si sta impostando. Ad esempio, quando si usa un set di record di tipo "A" sarà possibile specificare solo i record con il parametro `-a <IPv4 address>`.

Per creare un set di record, usare il comando `azure network dns record-set create`. Specificare il gruppo di risorse, il nome della zona, il nome relativo del set di record, il tipo di record e la durata (TTL). Se il parametro `--ttl` non è definito, verrà impostato "quattro" come valore in secondi predefinito.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Dopo aver creato il set di record "A", aggiungere l'indirizzo IPv4 usando il comando `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Gli esempi seguenti illustrano come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## Aggiornare un record in un set di record
### Per aggiungere un altro indirizzo IP (1.2.3.4) a un set di record "A" ("www") esistente:
    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### Per rimuovere un valore esistente da un set di record
usare `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## Rimuovere un record da un set di record
È possibile rimuovere i record da un set di record usando `azure network dns record-set delete-record`. Il record che viene rimosso deve corrispondere esattamente a un record esistente in tutti i parametri.

La rimozione dell'ultimo record da un set di record non elimina il set di record. Per altre informazioni, vedere la sezione [Eliminare un set di record](#delete) di questo articolo.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### Rimuovere un record AAAA da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Rimuovere un record CNAME da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### Rimuovere un record MX da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Rimuovere un record NS da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Rimuovere un record PTR da un set di record esistente
In questo caso 'my-arpa-zone.com' è la zona ARPA che rappresenta l'intervallo IP dell'utente. Ogni record PTR impostato in questa zona corrisponde a un indirizzo IP che rientra nell'intervallo IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### Rimuovere un record SRV da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### Rimuovere un record TXT da un set di record esistente
    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Eliminare un set di record
È possibile eliminare i set di record usando il cmdlet `Remove-AzureRmDnsRecordSet`. Non è possibile eliminare i set di record SOA ed NS al vertice della zona (name = "@") che sono stati creati automaticamente quando è stata creata la zona. Verranno eliminati automaticamente quando si elimina la zona.

Nell'esempio seguente, il set di record "A" "test-a" verrà rimosso dalla zona DNS "contoso.com":

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

L'opzione *-q* facoltativa può essere usata per eliminare la richiesta di conferma.

## Passaggi successivi
Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](dns-overview.md). Per informazioni sull'automazione di DNS, vedere [Creare zone e set di record DNS con .NET SDK](dns-sdk.md).

Per usare i record DNS inversi, vedere [Come gestire i record DNS inversi per i servizi tramite l'interfaccia della riga di comando di Azure](dns-reverse-dns-record-operations-cli.md).

<!---HONumber=AcomDC_0928_2016-->