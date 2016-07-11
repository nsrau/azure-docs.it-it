<properties 
   pageTitle="Come gestire i record DNS inversi per i servizi tramite PowerShell nel modello di distribuzione classico | Microsoft Azure"
   description="Come gestire i record DNS inversi o i record PTR per i servizi di Azure tramite PowerShell nel modello di distribuzione classico. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Come gestire i record DNS inversi per i servizi (classico) tramite PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](dns-reverse-dns-record-operations-ps.md).

## Convalida dei record DNS inversi
Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

- Il nome di dominio completo (FQDN) del DNS inverso è il nome del servizio cloud per il quale è stato specificato o il nome di un qualsiasi servizio cloud nella stessa sottoscrizione, ad esempio il DNS inverso è “contosoapp1.cloudapp.net.”.
- Il nome di dominio completo del DNS inverso si risolve in avanti nel nome o indirizzo IP del servizio cloud per cui è stato specificato o nel nome di qualsiasi servizio cloud o indirizzo IP nella stessa sottoscrizione, ad esempio il DNS inverso è “app1.contoso.com.”, un alias CName per contosoapp1.cloudapp.net.

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un servizio cloud viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## Aggiungere il DNS inverso ai servizi cloud esistenti
È possibile aggiungere un record DNS inverso a un servizio cloud esistente usando il cmdlet "Set-AzureService".

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Creare un servizio cloud con il DNS inverso  
È possibile aggiungere un nuovo servizio cloud con la proprietà DNS inverso specificata usando il cmdlet “Set-AzureService”:

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Visualizzare il DNS inverso per i servizi cloud esistenti
È possibile visualizzare il valore configurato per un servizio cloud esistente usando il cmdlet "Get-AzureService".

	PS C:\> Get-AzureService "contosoapp1"

## Rimuovere il DNS inverso dai servizi cloud esistenti
È possibile rimuovere una proprietà DNS inverso da un servizio cloud esistente utilizzando il cmdlet “Set-AzureService”. Questa operazione viene eseguita impostando il valore della proprietà DNS inverso su un valore vuoto:

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [DOMANDE FREQUENTI](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!---HONumber=AcomDC_0629_2016-->