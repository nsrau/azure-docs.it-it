---
title: 'Guida introduttiva: Creare una zona e un record DNS di Azure - Azure PowerShell'
titleSuffix: Azure DNS
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di una guida introduttiva dettagliata per creare e gestire la prima zona e il primo record DNS con Azure PowerShell.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 0d3852824702bbc88925981c3c665b1b3fda7404
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072176"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Guida introduttiva: Creare una zona e un record DNS di Azure con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In questa guida introduttiva vengono creati una zona e un record DNS con Azure PowerShell. È anche possibile eseguire questi passaggi tramite il [portale di Azure](dns-getstarted-portal.md) o l'[interfaccia della riga di comando di Azure](dns-getstarted-cli.md). 

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

DNS di Azure supporta anche la creazione di domini privati. Per istruzioni dettagliate sulla creazione della prima zona e del primo record DNS privati, vedere [Introduzione alle zone private di DNS di Azure con PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Prima di creare la zona DNS, creare un gruppo di risorse per contenere la zona DNS:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzDnsZone` . L’esempio seguente crea una zona DNS denominata *contoso.xyz* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Creare un record DNS

I set di record vengono creati usando il cmdlet `New-AzDnsRecordSet`. L'esempio seguente crea un record con il nome relativo "www" nella zona DNS "contoso.xyz" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record è "www.contoso.xyz". Il tipo di record è "A", con indirizzo IP "10.10.10.10", e la durata (TTL) è 3.600 secondi.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testare la risoluzione dei nomi

Dopo aver creato una zona DNS di test, con un record "A" di test, è possibile testare la risoluzione dei nomi con uno strumento denominato *nslookup*. 

**Per testare la risoluzione dei nomi DNS:**

1. Eseguire il cmdlet seguente per ottenere l'elenco di server dei nomi per la propria zona:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
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

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la prima zona e il primo record DNS con Azure PowerShell, è possibile creare i record per un'app Web in un dominio personalizzato.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)

