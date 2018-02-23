---
title: Gestire record DNS in DNS di Azure usando l'interfaccia della riga di comando 2.0 | Microsoft Docs
description: Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi dell'interfaccia della riga di comando 2.0 per le operazioni sui set di record e i record.
services: dns
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: kumud
ms.openlocfilehash: 6f8a462a8de93d75f0555397bea548d2b5122c09
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli-20"></a>Gestire record e recordset DNS in DNS di Azure con l'interfaccia della riga di comando 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](dns-operations-recordsets-portal.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Questo articolo descrive come gestire i record DNS per la zona DNS usando l'interfaccia della riga di comando di Azure 2.0 multipiattaforma, disponibile per Windows, Mac e Linux. È anche possibile gestire i record DNS tramite [Azure PowerShell](dns-operations-recordsets.md) o il [portale di Azure](dns-operations-recordsets-portal.md).

Gli esempi contenuti in questo articolo presuppongono che l'utente abbia [installato l'interfaccia della riga di comando di Azure 2.0, eseguito l'accesso e creato una zona DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introduzione

Prima di creare record DNS nel servizio DNS di Azure, è necessario comprendere il modo in cui quest'ultimo organizza i record DNS nei set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Per altre informazioni sui record DNS nel servizio DNS di Azure, vedere [Zone e record DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Creare un record DNS

Per creare un record DNS, usare il comando `az network dns record-set <record-type> set-record` (dove `<record-type>` è il tipo di record, ossia a, srv, txt e così via). Per altre informazioni, vedere `az network dns record-set --help`.

Quando si crea un record è necessario specificare il nome del gruppo di risorse, della zona e del set di record, il tipo di record e i dettagli del record da creare. Il nome assegnato al set di record deve essere un nome *relativo*, ovvero deve escludere il nome della zona.

Il comando crea il set di record, se non esiste già. In caso contrario, il comando aggiunge il record specificato al set di record esistente.

Se viene creato un nuovo set di record, per la durata (TTL) viene usato un valore predefinito di 3600. Per istruzioni su come usare TTL diversi, vedere [Creare un set di record DNS](#create-a-dns-record-set).

L'esempio seguente mostra come creare un record "A" denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*. L'indirizzo IP del record "A" è *1.2.3.4*.

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Per creare un set di record nell'apice della zona, in questo caso "contoso.com", usare il nome record "@", incluse le virgolette:

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Creare un set di record DNS

Negli esempi precedenti, il record DNS è stato aggiunto a un set di record esistente oppure il set di record è stato creato *in modo implicito*. È anche possibile creare il set di record *in modo esplicito* prima di aggiungere i record. Il servizio DNS di Azure supporta set di record vuoti, che possono fungere da segnaposto per riservare un nome DNS prima della creazione di record DNS. I set di record vuoti sono visibili nel piano di controllo del servizio DNS di Azure, ma non vengono visualizzati nei server dei nomi del servizio DNS di Azure.

I set di record vengono creati usando il comando `az network dns record-set <record-type> create`. Per altre informazioni, vedere `az network dns record-set <record-type> create --help`.

La creazione esplicita del set di record consente di specificare proprietà per il set come [Durata (TTL)](dns-zones-records.md#time-to-live) e metadati. È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore.

L'esempio seguente crea un set di record vuoto di tipo "A" con un TTL di 60 secondi, usando il parametro `--ttl` (forma breve `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

L'esempio seguente crea un set di record con due voci di metadati, "dept=finance" e "environment=production", usando il parametro `--metadata`:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Dopo aver creato un set di record vuoto, è possibile aggiungervi i record usando `azure network dns record-set <record-type> set-record` come descritto in [Creare un record DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Creare record di altri tipi

Dopo aver visto in dettaglio come creare record di tipo A, gli esempi seguenti illustrano come creare record di altri tipi supportati dal servizio DNS di Azure.

I parametri usati per specificare i dati del record variano a seconda del tipo di record. Per un record di tipo "A", ad esempio, specificare l'indirizzo IPv4 con il parametro `--ipv4-address <IPv4 address>`. I parametri per ogni tipo di record possono essere elencati usando `az network dns record-set <record-type> set-record --help`.

In ogni caso viene illustrato come creare un record singolo. Il record viene aggiunto al set di record esistente oppure viene creato un set di record in modo implicito. Per ulteriori informazioni sulla creazione di set di record e sulla definizione esplicita dei parametri di un set di record, vedere [Creare un set di record DNS](#create-a-dns-record-set).

Non vengono forniti esempi per la creazione di set di record SOA, dato che vengono creati ed eliminati con ogni zona DNS e non è possibile crearli o eliminarli separatamente. Tuttavia, [è possibile modificare i record SOA, come illustrato in uno degli esempi successivi](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Creare un record AAAA

```azurecli
az network dns record-set aaaa set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Creare un record CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Creare un record CNAME

> [!NOTE]
> Gli standard DNS non accettano record CNAME nel dominio radice di una zona (`--Name "@"`) né set di record contenenti più di un record.
> 
> Per altre informazioni, vedere[Record CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Creare un record MX

In questo esempio viene usato il nome del set di record "@" per creare il record MX al vertice della zona, in questo caso "contoso.com".

```azurecli
az network dns record-set mx set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Creare un record NS

```azurecli
az network dns record-set ns set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Creare un record PTR

In questo caso "my-arpa-zone.com" è la zona ARPA che rappresenta l'intervallo IP dell'utente. Ogni record PTR impostato in questa zona corrisponde a un indirizzo IP che rientra nell'intervallo IP.  Il nome del record "10" è l'ultimo ottetto dell'indirizzo IP all'interno di questo intervallo di indirizzi rappresentato dal record.

```azurecli
az network dns record-set ptr set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Creare un record SRV

Quando si crea un [set di record SRV](dns-zones-records.md#srv-records), specificare il *\_servizio* e il *\_protocollo* nel nome del set di record. Non è necessario includere "@" nel nome del set di record durante la creazione di un set di record SRV nel dominio radice della zona.

```azurecli
az network dns record-set srv set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Creare un record TXT

L'esempio seguente mostra come creare un record TXT. Per altre informazioni sulla lunghezza massima delle stringhe supportata nei record TXT, vedere [Record TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Ottenere un set di record

Per recuperare un set di record esistente, usare `az network dns record-set <record-type> show`. Per altre informazioni, vedere `az network dns record-set <record-type> show --help`.

Come per la creazione di un record o di un set di record, il nome assegnato al set di record deve essere un nome *relativo*, ovvero deve escludere il nome della zona. È anche necessario specificare il tipo di record, la zona contenente il set di record e il gruppo di risorse contenente la zona.

L'esempio seguente recupera il record "A" denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Elencare i set di record

È possibile elencare tutti i record in una zona DNS usando il comando `az network dns record-set list` . Per altre informazioni, vedere `az network dns record-set list --help`.

Questo esempio restituisce tutti i set di record nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*, a prescindere dal nome o dal tipo di record:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Questo esempio restituisce tutti i set di record corrispondenti al tipo di record specificato, in questo caso, i record "A":

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Aggiungere un record a un set di record esistente

È possibile usare `az network dns record-set <record-type> set-record` sia per creare un record in un nuovo set di record che per aggiungere un record a un set di record esistente.

Per ulteriori informazioni, vedere [Creare un record DNS](#create-a-dns-record) e [Creare record di altri tipi](#create-records-of-other-types) sopra.

## <a name="remove-a-record-from-an-existing-record-set"></a>Rimuovere un record da un set di record esistente.

Per rimuovere un record DNS da un set di record esistente, usare `az network dns record-set <record-type> remove-record`. Per altre informazioni, vedere `az network dns record-set <record-type> remove-record -h`.

Questo comando elimina un record DNS da un set di record. Se viene eliminato l'ultimo record in un set di record, viene eliminato anche il set stesso. Per mantenere il record vuoto, usare l'opzione `--keep-empty-record-set`.

È necessario specificare il record da eliminare e la zona da cui eliminarlo usando gli stessi parametri di quando si crea un record tramite `az network dns record-set <record-type> set-record`. I parametri vengono descritti in [Creare un record DNS](#create-a-dns-record) e [Creare record di altri tipi](#create-records-of-other-types) sopra.

L'esempio seguente elimina il record "A" con valore "1.2.3.4" dal set di record denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificare un set di record esistente

Ogni set di record contiene un [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadati](dns-zones-records.md#tags-and-metadata) e record DNS. Nelle sezioni seguenti viene illustrato come modificare ognuna di queste proprietà.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Per modificare un record A, AAAA, CAA, MX, NS, PTR, SRV o TXT

Per modificare un record esistente di tipo A, AAAA, CAA, MX, NS, PTR, SRV o TXT, è necessario prima aggiungere un nuovo record e quindi eliminare il record esistente. Per istruzioni dettagliate su come eliminare e aggiungere record, vedere le sezioni precedenti di questo articolo.

Nell'esempio seguente viene illustrato come modificare un record "A", dall'indirizzo IP 1.2.3.4 all'indirizzo IP 5.6.7.8:

```azurecli
az network dns record-set a set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Non è possibile aggiungere, rimuovere o modificare record nel set di record NS creato automaticamente nel dominio radice della zona, vale a dire `--Name "@"`, incluse le virgolette. Per questo set di record, le uniche modifiche consentite sono quelle relative alla durata (TTL) e ai metadati del set di record.

### <a name="to-modify-a-cname-record"></a>Per modificare un record CNAME

A differenza della maggior parte degli altri tipi di record, un set di record CNAME può contenere solo un singolo record.  Pertanto, non è possibile sostituire il valore corrente aggiungendo un nuovo record e rimuovendo il record esistente, come con gli altri tipi di record.

Per modificare un record CNAME, usare invece `az network dns record-set cname set-record`. Per altre informazioni, vedere `az network dns record-set cname set-record --help`

Nell'esempio viene modificato il set di record CNAME *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*, in modo che punti a "www.fabrikam.net" invece che al valore esistente:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Per modificare un record SOA

A differenza della maggior parte degli altri tipi di record, un set di record CNAME può contenere solo un singolo record.  Pertanto, non è possibile sostituire il valore corrente aggiungendo un nuovo record e rimuovendo il record esistente, come con gli altri tipi di record.

Per modificare il record SOA, usare invece `az network dns record-set soa update`. Per altre informazioni, vedere `az network dns record-set soa update --help`.

Nell'esempio seguente viene illustrato come impostare la proprietà "email" del record SOA della zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Per modificare i record NS al vertice della zona

Il set di record NS al vertice della zona viene creato automaticamente con ogni zona DNS. Contiene i nomi dei server dei nomi DNS di Azure assegnati alla zona.

È possibile aggiungere ulteriori server dei nomi a questo set di record NS per supportare domini coesistenti con più provider DNS. È anche possibile modificare il valore TTL e i metadati per questo set di record. Tuttavia, non è possibile rimuovere o modificare i server dei nomi DNS di Azure già popolati.

Notare che questo si applica solo al set di record NS al vertice della zona. Gli altri set di record NS nella zona (usati per delegare le zone figlio) possono essere modificati senza vincoli.

L'esempio seguente mostra come aggiungere un ulteriore server dei nomi al set di record NS al vertice della zona:

```azurecli
az network dns record-set ns set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Per modificare il valore TTL di un set di record esistente

Per modificare il valore TTL di un set di record esistente, usare `azure network dns record-set <record-type> update`. Per altre informazioni, vedere `azure network dns record-set <record-type> update --help`.

Nell'esempio seguente viene illustrato come modificare il valore TTL di un set di record, impostandolo in questo caso su 60 secondi:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Per modificare i metadati di un set di record esistente

È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore. Per modificare i metadati di un set di record esistente, usare `az network dns record-set <record-type> update`. Per altre informazioni, vedere `az network dns record-set <record-type> update --help`.

L'esempio seguente mostra come modificare un set di record con due voci di metadati, "dept=finance" e "environment=production". Si noti che i metadati esistenti vengono *sostituiti* dai valori assegnati.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Eliminare un set di record

È possibile eliminare i set di record usando il commando `az network dns record-set <record-type> delete` . Per altre informazioni, vedere `azure network dns record-set <record-type> delete --help`. Eliminando un set di record vengono eliminati anche tutti i record in esso contenuti.

> [!NOTE]
> Non è possibile eliminare i set di record SOA e NS dal dominio radice della zona (`--name "@"`).  Tali set di record vengono creati automaticamente durante la creazione della zona e vengono eliminati automaticamente quando la zona viene eliminata.

L'esempio seguente elimina il set di record "A" denominato *www* dalla zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Viene chiesto di confermare l'operazione di eliminazione. Per eliminare la richiesta, usare lo switch `--yes`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [zone e record nel servizio DNS di Azure](dns-zones-records.md).
<br>
Informazioni su come [proteggere zone e record](dns-protect-zones-recordsets.md) quando si usa il servizio DNS di Azure.
