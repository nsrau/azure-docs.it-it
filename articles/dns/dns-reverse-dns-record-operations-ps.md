---
title: Gestire i record DNS inversi in DNS di Azure con PowerShell | Documentazione Microsoft
description: DNS di Azure consente di gestire i record DNS inversi o i record PTR per i servizi di Azure con PowerShell in Resource Manager
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 78de3b8dd2d8bd0992bfbacb1079825dca486442


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>Gestire i record DNS inversi per i servizi di Azure con PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Per altre informazioni sul modello di distribuzione classica, vedere [Come gestire i record DNS inversi per i servizi di Azure (classico) con Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inversi

Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

* Il valore "ReverseFqdn" corrisponde al valore "Fqdn" per la risorsa indirizzo IP pubblico per cui è stato specificato o al valore "Fqdn" per qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio "ReverseFqdn" è "contosoapp1.northus.cloudapp.azure.com.".
* Il valore "ReverseFqdn" si risolve in avanti nel nome o IP dell'indirizzo IP pubblico per cui è stato specificato o nel valore "Fqdn" o IP di qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio "ReverseFqdn" è "app1.contoso.com.", un alias CName per "contosoapp1.northus.cloudapp.azure.com."

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un indirizzo IP pubblico viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Aggiungere il DNS inverso agli indirizzi IP pubblici esistenti

È possibile aggiungere il DNS inverso a un indirizzo IP pubblico usando il cmdlet `Set-AzureRmPublicIpAddress`:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Se si desidera aggiungere il DNS inverso a un indirizzo IP pubblico esistente che non dispone già di un nome DNS è necessario specificare anche un nome DNS. A tale scopo è possibile usare il cmdlet "Set-AzureRmPublicIpAddress":

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Creare un indirizzo IP pubblico con il DNS inverso

È possibile aggiungere un nuovo indirizzo IP pubblico con la proprietà DNS inverso specificata usando il cmdlet `New-AzureRmPublicIpAddress`:

```powershell
New-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Visualizzare il DNS inverso per gli indirizzi IP pubblici esistenti

È possibile visualizzare il valore configurato per un indirizzo IP pubblico esistente usando il cmdlet `Get-AzureRmPublicIpAddress`:

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS"
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Rimuovere il DNS inverso da indirizzi IP pubblici esistenti

È possibile rimuovere il DNS inverso da un indirizzo IP pubblico usando il cmdlet `Set-AzureRmPublicIpAddress`. Questa operazione viene eseguita impostando il valore della proprietà ReverseFqdn su un valore vuoto:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Feb17_HO2-->


