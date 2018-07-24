---
title: 'Guida introduttiva: Creare una zona e un record DNS di Azure con Azure PowerShell'
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di una guida introduttiva dettagliata per creare e gestire la prima zona e il primo record DNS con Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 07/16/2018
ms.author: victorh
ms.openlocfilehash: e5801e9ed512a32d793f7b4b71be86174f656ab0
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089979"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Guida introduttiva: Creare una zona e un record DNS di Azure con Azure PowerShell

In questa guida introduttiva vengono creati una zona e un record DNS con Azure PowerShell. È anche possibile eseguire questi passaggi tramite il [portale di Azure](dns-getstarted-portal.md) o l'[interfaccia della riga di comando di Azure](dns-getstarted-cli.md). 

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare infine la zona DNS su Internet, è necessario configurare i server dei nomi per il dominio. Ogni passaggio è illustrato di seguito.

DNS di Azure supporta anche la creazione di domini privati. Per istruzioni dettagliate sulla creazione della prima zona e del primo record DNS privati, vedere [Introduzione alle zone private di DNS di Azure con PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare la zona DNS, creare un gruppo di risorse per contenere la zona DNS:

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Viene creata una zona DNS con il cmdlet `New-AzureRmDnsZone` . L'esempio seguente crea una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. Usare l'esempio per creare una zona DNS, sostituendo i valori con quelli personalizzati.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Creare un record DNS

I set di record vengono creati usando il cmdlet `New-AzureRmDnsRecordSet`. L'esempio seguente crea un record con il nome relativo "www" nella zona DNS "contoso.com" nel gruppo di risorse "MyResourceGroup". Il nome completo del set di record è "www.contoso.com". Il tipo di record è "A", con indirizzo IP "1.2.3.4", e la durata (TTL) è 3600 secondi.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>Visualizzare i record

Per elencare i record DNS nella zona, usare:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Aggiornare i server dei nomi

Dopo essersi assicurati che la zona e i record DNS siano stati configurati correttamente, è necessario configurare il nome di dominio in modo che usi i server dei nomi di DNS Azure. Ciò consente agli altri utenti su Internet di trovare i record DNS.

I server dei nomi per la zona vengono specificati tramite il cmdlet `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

I server dei nomi devono essere configurati con il registrar dei nomi di dominio, in cui è stato acquistato il nome di dominio. Il registrar offrirà l'opzione per la configurazione dei server dei nomi per il dominio. Per altre informazioni, vedere [Delegare un dominio al servizio DNS Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Quando non sono più necessarie, è possibile eliminare tutte le risorse create in questa guida introduttiva eliminando il gruppo di risorse:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la prima zona e il primo record DNS con Azure PowerShell, è possibile creare i record per un'app Web in un dominio personalizzato.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)

