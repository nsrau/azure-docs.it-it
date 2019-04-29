---
title: Delegare un sottodominio DNS di Azure con Azure PowerShell
description: Informazioni su come delegare un sottodominio DNS di Azure con Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 40b2a4d98e6269d9740856ba44c1043af75ce1b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791185"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegare un sottodominio DNS di Azure con Azure PowerShell

È possibile usare Azure PowerShell per delegare un sottodominio DNS. Se ad esempio si è proprietari del dominio contoso.com, è possibile delegare un sottodominio denominato *engineering* a un'altra zona distinta che può essere amministrata separatamente dalla zona di contoso.com.

Se si preferisce, è possibile delegare un sottodominio usando il [portale di Azure](delegate-subdomain.md).

> [!NOTE]
> Contoso.com viene usato come esempio in questo articolo. Sostituire contoso.com con il nome del proprio dominio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Prerequisiti

Per delegare un sottodominio DNS di Azure occorre prima delegare il dominio pubblico alla zona DNS di Azure. Per istruzioni sulla configurazione dei server dei nomi per la delega, vedere [Esercitazione: Ospitare il dominio in DNS di Azure](./dns-delegate-domain-azure-dns.md). Una volta delegato il dominio alla zona del servizio DNS di Azure, è possibile delegare il sottodominio.

## <a name="create-a-zone-for-your-subdomain"></a>Creare una zona per il sottodominio

Creare prima di tutto la zona per il sottodominio **engineering**.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Prendere nota dei server dei nomi

Prendere nota dei quattro server dei nomi relativi al sottodominio "engineering".

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Creare un record di test

Creare un record **A** nella zona "engineering" da usare per il test.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Creare un record NS

Creare quindi un record server dei nomi (NS) per la zona **engineering** nella zona contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testare la delega

Usare nslookup per testare la delega.

1. Aprire una finestra di PowerShell.
2. Al prompt dei comandi digitare `nslookup www.engineering.contoso.com.`
3. Si dovrebbe ricevere una risposta non autorevole che mostra l'indirizzo **10.10.10.10**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare il DNS inverso per i servizi ospitati in Azure](dns-reverse-dns-for-azure-services.md).