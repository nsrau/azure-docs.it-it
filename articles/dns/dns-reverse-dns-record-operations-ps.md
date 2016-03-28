<properties 
   pageTitle="Come gestire i record DNS inversi per i servizi tramite PowerShell in Gestione risorse | Microsoft Azure"
   description="Come gestire i record DNS inversi o i record PTR per i servizi di Azure tramite PowerShell in Gestione risorse"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Come gestire i record DNS inversi per i servizi tramite PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## Convalida dei record DNS inversi 
Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

- Il valore “ReverseFqdn” corrisponde al valore “Fqdn” per la risorsa indirizzo IP pubblico per cui è stato specificato o al valore “Fqdn” per qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio “ReverseFqdn” è “contosoapp1.northus.cloudapp.azure.com.”.

- Il valore “ReverseFqdn” si risolve in avanti nel nome o IP dell’indirizzo IP pubblico per cui è stato specificato o nel valore “Fqdn” o IP di qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio “ReverseFqdn” è “app1.contoso.com.”, un alias CName per “contosoapp1.northus.cloudapp.azure.com.”

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un indirizzo IP pubblico viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## Aggiungere il DNS inverso agli indirizzi IP pubblici esistenti
È possibile aggiungere il DNS inverso a un indirizzo IP pubblico esistente usando il cmdlet "Set-AzureRmPublicIpAddress".

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip
	
Se si desidera aggiungere il DNS inverso a un indirizzo IP pubblico esistente che non dispone già di un nome DNS è necessario specificare anche un nome DNS. A tale scopo è possibile usare il cmdlet "Set-AzureRmPublicIpAddress":

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
	PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## Creare un indirizzo IP pubblico con il DNS inverso
È possibile aggiungere un nuovo indirizzo IP pubblico con la proprietà DNS inverso specificata usando il cmdlet "New-AzureRmPublicIpAddress".

	PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
 
## Visualizzare il DNS inverso per gli indirizzi IP pubblici esistenti
È possibile visualizzare il valore configurato per un indirizzo IP pubblico esistente usando il cmdlet "Get-AzureRmPublicIpAddress":

	PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS 

## Rimuovere il DNS inverso da indirizzi IP pubblici esistenti
È possibile rimuovere una proprietà DNS inverso da un indirizzo IP pubblico esistente usando il cmdlet “Set-AzureRmPublicIpAddress”. Questa operazione viene eseguita impostando il valore della proprietà ReverseFqdn su un valore vuoto:

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = ""
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

[AZURE.INCLUDE [DOMANDE FREQUENTI](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0316_2016-->