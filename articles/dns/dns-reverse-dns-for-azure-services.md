---
title: DNS inverso per i servizi di Azure | Microsoft Docs
description: Informazioni su come configurare ricerche DNS inverse per i servizi ospitati in Azure
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: 0c5d12e9d6b5ddbee2a930e4e537b8180b7a9c7b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurare il DNS inverso per i servizi ospitati in Azure

Questo articolo illustra come configurare ricerche DNS inverse per i servizi ospitati in Azure.

I servizi in Azure usano gli indirizzi IP assegnati da Azure e di proprietà di Microsoft. Questi record DNS inversi (record PTR) devono essere creati nelle zone di ricerca DNS inversa corrispondenti di Microsoft. Questo articolo illustra come eseguire questa operazione.

Questo scenario non deve essere confuso con la possibilità di [eseguire l'hosting di zone di ricerca DNS inversa per gli intervalli di indirizzi IP assegnati in DNS Azure](dns-reverse-dns-hosting.md). In questo caso, gli intervalli di indirizzi IP rappresentati dalle zone di ricerca inversa devono essere assegnati all'organizzazione, in genere dal provider di servizi Internet.

Prima di leggere questo articolo, è necessario leggere [Panoramica del DNS inverso e supporto in Azure](dns-reverse-dns-overview.md).

Nel servizio DNS di Azure le risorse di calcolo, ad esempio macchine virtuali, set di scalabilità di macchine virtuali o cluster di Service Fabric, vengono esposte tramite una risorsa PublicIpAddress. Le ricerche DNS inverse vengono configurate usando la proprietà 'ReverseFqdn' di PublicIpAddress.


Il DNS inverso non è attualmente supportato per il servizio app di Azure.

## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inversi

Le terze parti non devono poter creare record DNS inversi per il mapping di servizi di Azure nei domini DNS dell'utente. Per evitare che questo accada, Azure consente di creare un record DNS inverso solo quando il nome di dominio specificato nel record DNS inverso è identico a, o si risolve con, il nome DNS o l'indirizzo IP di un PublicIpAddress o di un servizio cloud nella stessa sottoscrizione di Azure.

Questa convalida viene eseguita solo quando il record DNS inverso viene impostato o modificato. La convalida non viene ripetuta periodicamente.

Si supponga ad esempio che la risorsa PublicIpAddress abbia il nome DNS contosoapp1.northus.cloudapp.azure.com e l'indirizzo IP 23.96.52.53. La proprietà ReverseFqdn per PublicIpAddress può essere specificata come segue:
* Il nome DNS di PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Il nome DNS di un PublicIpAddress diverso nella stessa sottoscrizione, ad esempio contosoapp2.westus.cloudapp.azure.com
* Un nome DNS personale, ad esempio app1.contoso.com, purché questo nome sia *prima* configurato come record CNAME per contosoapp1.northus.cloudapp.azure.com o per un PublicIpAddress diverso nella stessa sottoscrizione.
* Un nome DNS personale, ad esempio app1.contoso.com, purché questo nome sia *prima* configurato come record A per l'indirizzo IP 23.96.52.53 o per l'indirizzo IP di un PublicIpAddress diverso nella stessa sottoscrizione.

Gli stessi vincoli si applicano al DNS inverso per i servizi cloud.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS inverso per le risorse PublicIpAddress

Questa sezione offre istruzioni dettagliate sulla configurazione del DNS inverso per risorse PublicIpAddress nel modello di distribuzione di Resource Manager tramite Azure PowerShell, l'interfaccia della riga di comando di Azure 1.0 o l'interfaccia della riga di comando di Azure 2.0. La configurazione del DNS inverso per le risorse PublicIpAddress non è attualmente supportata dal portale di Azure.

Azure supporta attualmente il DNS inverso solo per le risorse PublicIpAddress IPv4. Non è supportato per IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Aggiungere il DNS inverso a un PublicIpAddress esistente

#### <a name="powershell"></a>PowerShell

Per aggiungere il DNS inverso a un PublicIpAddress esistente:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Per aggiungere il DNS inverso a un PublicIpAddress esistente che non ha già un nome DNS è necessario specificare anche un nome DNS:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

Per aggiungere il DNS inverso a un PublicIpAddress esistente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Per aggiungere il DNS inverso a un PublicIpAddress esistente che non ha già un nome DNS è necessario specificare anche un nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Per aggiungere il DNS inverso a un PublicIpAddress esistente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Per aggiungere il DNS inverso a un PublicIpAddress esistente che non ha già un nome DNS è necessario specificare anche un nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Creare un indirizzo IP pubblico con il DNS inverso

Per creare un nuovo PublicIpAddress con la proprietà di DNS inverso già specificata:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Visualizzare il DNS inverso di un PublicIpAddress esistente

Per visualizzare il valore configurato per un PublicIpAddress esistente:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Rimuovere il DNS inverso da indirizzi IP pubblici esistenti

Per rimuovere una proprietà di DNS inverso da un PublicIpAddress esistente:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurare il DNS inverso per i servizi cloud

Questa sezione offre istruzioni dettagliate sulla configurazione del DNS inverso per i servizi cloud nel modello di distribuzione classica tramite Azure PowerShell. La configurazione del DNS inverso per i servizi cloud non è supportata nel portale di Azure, nell'interfaccia della riga di comando di Azure 1.0 e nell'interfaccia della riga di comando di Azure 2.0.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Aggiungere il DNS inverso ai servizi cloud esistenti

Per aggiungere un record DNS inverso a un servizio cloud esistente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Creare un servizio cloud con il DNS inverso

Per creare un nuovo servizio cloud con la proprietà di DNS inverso già specificata:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Visualizzare il DNS inverso per i servizi cloud esistenti

Per visualizzare la proprietà di DNS inverso per un servizio cloud esistente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Rimuovere il DNS inverso dai servizi cloud esistenti

Per rimuovere una proprietà di DNS inverso da un servizio cloud esistente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>domande frequenti

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto costano i record DNS inversi?

Sono gratuiti.  Non sono previsti costi aggiuntivi per i record DNS inversi o le query.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>I record DNS inversi verranno risolti da Internet?

Sì. Dopo aver impostato la proprietà di DNS inverso per il servizio di Azure, Azure gestisce tutte le deleghe DNS e le zone DNS necessarie per assicurare che il record di DNS inverso si risolva per tutti gli utenti di Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Vengono creati record DNS inversi per i servizi di Azure?

No. Il DNS inverso sarà una funzionalità che prevede il consenso esplicito. Se si sceglie di non configurare alcun record DNS inverso predefinito, non ne verrà creato alcuno.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual è il formato per il nome di dominio completo (FQDN)?

I nomi di dominio completi vengono specificati in ordine progressivo e devono terminare con un punto, ad esempio "app1.contoso.com.".

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Cosa accade se i controlli di convalida per il DNS inverso specificati danno esito negativo?

Se il controllo di convalida per il DNS dà esito negativo, la configurazione del record DNS inverso non riesce. Correggere il valore DNS inverso e riprovare.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>È possibile configurare il DNS inverso per il servizio app di Azure?

No. Il DNS inverso non è supportato per il servizio app di Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>È possibile configurare più record DNS inversi per il servizio di Azure?

No. Azure supporta un solo record DNS inverso per ogni servizio cloud di Azure o PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>È possibile configurare il DNS inverso per le risorse PublicIpAddress IPv6?

No. Azure supporta attualmente il DNS inverso solo per le risorse PublicIpAddress IPv4 e i servizi cloud.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>È possibile inviare messaggi di posta elettronica a domini esterni dai servizi di calcolo di Azure?

No. [I servizi di calcolo di Azure non supportano l'invio di messaggi di posta elettronica a domini esterni](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul DNS inverso, vedere [Risoluzione DNS inversa su Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Informazioni su come eseguire l'[hosting della zona di ricerca inversa per l'intervallo di indirizzi IP assegnato dal provider di servizi Internet in DNS Azure](dns-reverse-dns-for-azure-services.md).

