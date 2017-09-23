---
title: Gestire record DNS in DNS di Azure usando l'interfaccia della riga di comando 1.0 | Microsoft Docs
description: Gestione dei set di record e dei record DNS in DNS di Azure quando si ospita il dominio in DNS di Azure. Tutti i comandi dell'interfaccia della riga di comando 1.0 per le operazioni sui set di record e i record.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 307b327e4c04a0461e39930114eb193791cbda9a
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017

---

# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Gestire record DNS in DNS di Azure con l'interfaccia della riga di comando 1.0

> [!div class="op_single_selector"]
> * [portale di Azure](dns-operations-recordsets-portal.md)
> * [Interfaccia della riga di comando di Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Questo articolo descrive come gestire i record DNS per la zona DNS usando l'interfaccia della riga di comando multipiattaforma di Azure, disponibile per Windows, Mac e Linux. È anche possibile gestire i record DNS con [Azure PowerShell](dns-operations-recordsets.md) o il [portale di Azure](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](dns-operations-recordsets-cli-nodejs.md): l'interfaccia della riga di comando per il modello di distribuzione classico e di gestione delle risorse.
* [Interfaccia della riga di comando di Azure 2.0](dns-operations-recordsets-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse.

Gli esempi contenuti in questo articolo presuppongono che l'utente abbia [installato l'interfaccia della riga di comando di Azure 1.0, eseguito l'accesso e creato una zona DNS](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Introduzione

Prima di creare record DNS nel servizio DNS di Azure, è necessario comprendere il modo in cui quest'ultimo organizza i record DNS nei set di record DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Per altre informazioni sui record DNS nel servizio DNS di Azure, vedere [Zone e record DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Creare un record DNS

Per creare un record DNS, usare il comando `azure network dns record-set add-record`. Per altre informazioni, vedere `azure network dns record-set add-record -h`.

Quando si crea un record è necessario specificare il nome del gruppo di risorse, della zona e del set di record, il tipo di record e i dettagli del record da creare. Il nome assegnato al set di record deve essere un nome *relativo*, ovvero deve escludere il nome della zona.

Il comando crea il set di record, se non esiste già. In caso contrario, il comando aggiunge il record specificato al set di record esistente.

Se viene creato un nuovo set di record, per la durata (TTL) viene usato un valore predefinito di 3600. Per istruzioni su come usare TTL diversi, vedere [Creare un set di record DNS](#create-a-dns-record-set).

L'esempio seguente mostra come creare un record "A" denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*. L'indirizzo IP del record "A" è *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Per creare un record nel dominio radice, in questo caso "contoso.com", usare il nome record "@", incluse le virgolette:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Creare un set di record DNS

Negli esempi precedenti, il record DNS è stato aggiunto a un set di record esistente oppure il set di record è stato creato *in modo implicito*. È anche possibile creare il set di record *in modo esplicito* prima di aggiungere i record. Il servizio DNS di Azure supporta set di record vuoti, che possono fungere da segnaposto per riservare un nome DNS prima della creazione di record DNS. I set di record vuoti sono visibili nel piano di controllo del servizio DNS di Azure, ma non vengono visualizzati nei server dei nomi del servizio DNS di Azure.

I set di record vengono creati usando il comando `azure network dns record-set create`. Per altre informazioni, vedere `azure network dns record-set create -h`.

La creazione esplicita del set di record consente di specificare proprietà per il set come [Durata (TTL)](dns-zones-records.md#time-to-live) e metadati. È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore.

L'esempio seguente crea un set di record vuoto impostato con un TTL di 60 secondi, usando il parametro `--ttl` (forma breve `-l`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

L'esempio seguente crea un set di record con due voci di metadati, "dept=finance" e "environment=production", usando il parametro `--metadata` (forma breve `-m`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

Dopo aver creato un set di record vuoto, è possibile aggiungervi i record usando `azure network dns record-set add-record` come descritto in [Creare un record DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Creare record di altri tipi

Dopo aver visto in dettaglio come creare record di tipo A, gli esempi seguenti illustrano come creare record di altri tipi supportati dal servizio DNS di Azure.

I parametri usati per specificare i dati del record variano a seconda del tipo di record. Per un record di tipo "A", ad esempio, specificare l'indirizzo IPv4 con il parametro `-a <IPv4 address>`. I parametri per ogni tipo di record possono essere elencati usando `azure network dns record-set add-record -h`.

In ogni caso viene illustrato come creare un record singolo. Il record viene aggiunto al set di record esistente oppure viene creato un set di record in modo implicito. Per ulteriori informazioni sulla creazione di set di record e sulla definizione esplicita dei parametri di un set di record, vedere [Creare un set di record DNS](#create-a-dns-record-set).

Non vengono forniti esempi per la creazione di set di record SOA, dato che vengono creati ed eliminati con ogni zona DNS e non è possibile crearli o eliminarli separatamente. Tuttavia, [è possibile modificare i record SOA, come illustrato in uno degli esempi successivi](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Creare un record AAAA

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>Creare un record CNAME

> [!NOTE]
> Gli standard DNS non accettano record CNAME nel dominio radice di una zona (`-Name "@"`) né set di record contenenti più di un record.
> 
> Per altre informazioni, vedere[Record CNAME](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Creare un record MX

In questo esempio viene usato il nome del set di record "@" per creare il record MX al vertice della zona, in questo caso "contoso.com".

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Creare un record NS

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Creare un record PTR

In questo caso "my-arpa-zone.com" è la zona ARPA che rappresenta l'intervallo IP dell'utente. Ogni record PTR impostato in questa zona corrisponde a un indirizzo IP che rientra nell'intervallo IP.  Il nome del record "10" è l'ultimo ottetto dell'indirizzo IP all'interno di questo intervallo di indirizzi rappresentato dal record.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Creare un record SRV

Quando si crea un [set di record SRV](dns-zones-records.md#srv-records), specificare il *\_servizio* e il *\_protocollo* nel nome del set di record. Non è necessario includere "@" nel nome del set di record durante la creazione di un set di record SRV nel dominio radice della zona.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>Creare un record TXT

L'esempio seguente mostra come creare un record TXT. Per altre informazioni sulla lunghezza massima delle stringhe supportata nei record TXT, vedere [Record TXT](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Ottenere un set di record

Per recuperare un set di record esistente, usare `azure network dns record-set show`. Per altre informazioni, vedere `azure network dns record-set show -h`.

Come per la creazione di un record o di un set di record, il nome assegnato al set di record deve essere un nome *relativo*, ovvero deve escludere il nome della zona. È anche necessario specificare il tipo di record, la zona contenente il set di record e il gruppo di risorse contenente la zona.

L'esempio seguente recupera il record "A" denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Elencare i set di record

È possibile elencare tutti i record in una zona DNS usando il comando `azure network dns record-set list` . Per altre informazioni, vedere `azure network dns record-set list -h`.

Questo esempio restituisce tutti i set di record nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*, a prescindere dal nome o dal tipo di record:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

Questo esempio restituisce tutti i set di record corrispondenti al tipo di record specificato, in questo caso, i record "A":

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Aggiungere un record a un set di record esistente

È possibile usare `azure network dns record-set add-record` sia per creare un record in un nuovo set di record che per aggiungere un record a un set di record esistente.

Per ulteriori informazioni, vedere [Creare un record DNS](#create-a-dns-record) e [Creare record di altri tipi](#create-records-of-other-types) sopra.

## <a name="remove-a-record-from-an-existing-record-set"></a>Rimuovere un record da un set di record esistente.

Per rimuovere un record DNS da un set di record esistente, usare `azure network dns record-set delete-record`. Per altre informazioni, vedere `azure network dns record-set delete-record -h`.

Questo comando elimina un record DNS da un set di record. Se viene eliminato l'ultimo record in un set di record, **non** viene eliminato anche il set stesso. Si avrà invece un set di record vuoto. Per eliminare il set di record, vedere [Eliminare un set di record](#delete-a-record-set).

È necessario specificare il record da eliminare e la zona da cui eliminarlo usando gli stessi parametri di quando si crea un record tramite `azure network dns record-set add-record`. I parametri vengono descritti in [Creare un record DNS](#create-a-dns-record) e [Creare record di altri tipi](#create-records-of-other-types) sopra.

Questo comando richiede una conferma. Questo messaggio può essere soppresso mediante lo switch `--quiet` (forma breve `-q`).

L'esempio seguente elimina il record "A" con valore "1.2.3.4" dal set di record denominato *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*. La richiesta di conferma viene soppressa.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Modificare un set di record esistente

Ogni set di record contiene un [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadati](dns-zones-records.md#tags-and-metadata) e record DNS. Nelle sezioni seguenti viene illustrato come modificare ognuna di queste proprietà.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>Per modificare un record A, AAAA, MX, NS, PTR, SRV o TXT

Per modificare un record esistente di tipo A, AAAA, MX, NS, PTR, SRV o TXT è necessario innanzitutto aggiungere un nuovo record e quindi eliminare il record esistente. Per istruzioni dettagliate su come eliminare e aggiungere record, vedere le sezioni precedenti di questo articolo.

Nell'esempio seguente viene illustrato come modificare un record "A", dall'indirizzo IP 1.2.3.4 all'indirizzo IP 5.6.7.8:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Per modificare un record CNAME

Per modificare un record CNAME, usare `azure network dns record-set add-record` per aggiungere il nuovo valore di record. A differenza di altri tipi di record, un set di record CNAME può contenere solo un singolo record. Pertanto, il record esistente viene *sostituito* quando si aggiunge il nuovo record e non deve essere eliminato separatamente.  Verrà richiesto di accettare la sostituzione.

Nell'esempio viene modificato il set di record CNAME *www* nella zona *contoso.com* nel gruppo di risorse *MyResourceGroup*, in modo che punti a "www.fabrikam.net" invece che al valore esistente:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Per modificare un record SOA

Usare `azure network dns record-set set-soa-record` per modificare la SOA per una zona DNS specificata. Per altre informazioni, vedere `azure network dns record-set set-soa-record -h`.

Nell'esempio seguente viene illustrato come impostare la proprietà 
"email" del record SOA della zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>Per modificare i record NS al vertice della zona

Il set di record NS al vertice della zona viene creato automaticamente con ogni zona DNS. Contiene i nomi dei server dei nomi DNS di Azure assegnati alla zona.

È possibile aggiungere ulteriori server dei nomi a questo set di record NS per supportare domini coesistenti con più provider DNS. È anche possibile modificare il valore TTL e i metadati per questo set di record. Tuttavia, non è possibile rimuovere o modificare i server dei nomi DNS di Azure già popolati.

Notare che questo si applica solo al set di record NS al vertice della zona. Gli altri set di record NS nella zona (usati per delegare le zone figlio) possono essere modificati senza vincoli.

L'esempio seguente mostra come aggiungere un ulteriore server dei nomi al set di record NS al vertice della zona:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Per modificare il valore TTL di un set di record esistente

Per modificare il valore TTL di un set di record esistente, usare `azure network dns record-set set`. Per altre informazioni, vedere `azure network dns record-set set -h`.

Nell'esempio seguente viene illustrato come modificare il valore TTL di un set di record, impostandolo in questo caso su 60 secondi:

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Per modificare i metadati di un set di record esistente

È possibile usare i [metadati del set di record](dns-zones-records.md#tags-and-metadata) per associare dati specifici dell'applicazione a ogni set di record, sotto forma di coppie chiave-valore. Per modificare i metadati di un set di record esistente, usare `azure network dns record-set set`. Per altre informazioni, vedere `azure network dns record-set set -h`.

L'esempio seguente mostra come modificare un set di record con due voci di metadati, "dept=finance" e "environment=production", usando il parametro `--metadata` (forma breve `-m`). Si noti che i metadati esistenti vengono *sostituiti* dai valori assegnati.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>Eliminare un set di record

È possibile eliminare i set di record usando il commando `azure network dns record-set delete` . Per altre informazioni, vedere `azure network dns record-set delete -h`. Eliminando un set di record vengono eliminati anche tutti i record in esso contenuti.

> [!NOTE]
> Non è possibile eliminare i set di record SOA e NS dal dominio radice della zona (`-Name "@"`).  Tali set di record vengono creati automaticamente durante la creazione della zona e vengono eliminati automaticamente quando la zona viene eliminata.

L'esempio seguente elimina il set di record "A" denominato *www* dalla zona *contoso.com* nel gruppo di risorse *MyResourceGroup*:

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

Viene chiesto di confermare l'operazione di eliminazione. Per eliminare la richiesta, usare lo switch `--quiet` (forma breve `-q`).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [zone e record nel servizio DNS di Azure](dns-zones-records.md).
<br>
Informazioni su come [proteggere zone e record](dns-protect-zones-recordsets.md) quando si usa il servizio DNS di Azure.

