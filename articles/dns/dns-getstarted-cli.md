---
title: "Guida introduttiva: Creare una zona e un record DNS di Azure con l'interfaccia della riga di comando di Azure"
description: "Guida introduttiva: Informazioni su come creare una zona e un record DNS in DNS di Azure. Si tratta di una guida dettagliata per creare e gestire la prima zona e il primo record DNS con l'interfaccia della riga di comando di Azure."
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b5d842c2d6ff84a0f17c4e8be0bfade018edc48b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959986"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Guida introduttiva: Creare una zona e un record DNS di Azure con l'interfaccia della riga di comando di Azure

Questo articolo illustra i passaggi per creare la prima zona e il primo record DNS con l'interfaccia della riga di comando di Azure disponibile per Windows, Mac e Linux. È anche possibile eseguire questi passaggi tramite il [portale di Azure](dns-getstarted-portal.md) o [Azure PowerShell](dns-getstarted-powershell.md).

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

DNS di Azure supporta anche le zone DNS private. Per altre informazioni sulle zone DNS private, vedere [Using Azure DNS for private domains](private-dns-overview.md) (Uso di Azure DNS per domini privati). Per un esempio su come creare una zona DNS privata, vedere [Introduzione alle Zone private di DNS di Azure con l'interfaccia della riga di comando](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Prima di creare la zona DNS, creare un gruppo di risorse per contenere la zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS viene creata utilizzando il comando `az network dns zone create` . Per visualizzare la guida per questo comando, digitare `az network dns zone create -h`.

L'esempio seguente crea una zona DNS denominata *contoso.xyz* nel gruppo di risorse *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Creare un record DNS

Per creare un record DNS, usare il comando `az network dns record-set [record type] add-record`. Per informazioni sui record A, vedere `azure network dns record-set A add-record -h`.

L'esempio seguente crea un record con il nome relativo "www" nella zona DNS "contoso.xyz" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record è "www.contoso.xyz". Il tipo di record è "A" con indirizzo IP "10.10.10.10" e un valore TTL predefinito di 3600 secondi (1 ora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, eseguire:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testare la risoluzione dei nomi

Dopo aver creato una zona DNS di test, con un record "A" di test, è possibile testare la risoluzione dei nomi con uno strumento denominato *nslookup*. 

**Per testare la risoluzione dei nomi DNS:**

1. Eseguire il cmdlet seguente per ottenere l'elenco di server dei nomi per la propria zona:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Copiare uno dei nomi di server dei nomi dall'output del passaggio precedente.

1. Aprire un prompt dei comandi ed eseguire il comando seguente:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Ad esempio:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Verrà visualizzata una schermata simile alla seguente:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Il nome host **www\.contoso.xyz** viene risolto in **10.10.10.10**, esattamente come è stato configurato. Questo risultato conferma il corretto funzionamento della risoluzione dei nomi.

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Quando non sono più necessarie, è possibile eliminare tutte le risorse create in questa guida introduttiva eliminando il gruppo di risorse:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la prima zona e il primo record DNS con l'interfaccia della riga di comando di Azure, è possibile creare i record per un'app Web in un dominio personalizzato.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
