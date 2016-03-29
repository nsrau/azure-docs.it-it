<properties 
   pageTitle="Come gestire i record DNS inversi per i servizi tramite l’interfaccia della riga di comando di Azure in Gestione risorse | Microsoft Azure"
   description="Come gestire i record DNS inversi o i record PTR per i servizi di Azure tramite l’interfaccia della riga di comando di Azure in Gestione risorse"
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

# Come gestire i record DNS inversi per i servizi tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## Convalida dei record DNS inversi 
Per impedire che qualcun altro possa creare record DNS inversi mappati ai propri domini DNS, Azure consente la creazione di un record DNS inverso solo se una delle condizioni seguenti è vera:

- Il valore “ReverseFqdn” corrisponde al valore “Fqdn” per la risorsa indirizzo IP pubblico per cui è stato specificato o al valore “Fqdn” per qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio “ReverseFqdn” è “contosoapp1.northus.cloudapp.azure.com.”.

- Il valore “ReverseFqdn” si risolve in avanti nel nome o IP dell’indirizzo IP pubblico per cui è stato specificato o nel valore “Fqdn” o IP di qualsiasi indirizzo IP pubblico nella stessa sottoscrizione, ad esempio “ReverseFqdn” è “app1.contoso.com.”, un alias CName per “contosoapp1.northus.cloudapp.azure.com.”

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inverso per un indirizzo IP pubblico viene impostata o modificata. La convalida non viene ripetuta periodicamente.

## Aggiungere il DNS inverso agli indirizzi IP pubblici esistenti
È possibile aggiungere il DNS inverso a un indirizzo IP pubblico usando azure network public-ip set:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Se si desidera aggiungere il DNS inverso a un indirizzo IP pubblico esistente che non dispone già di un nome DNS è necessario specificare anche un nome DNS. A tale scopo è possibile usare azure network public-ip set:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## Creare un indirizzo IP pubblico con il DNS inverso
È possibile aggiungere un nuovo indirizzo IP pubblico con la proprietà DNS inverso specificata usando azure network public-ip create.

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
 
## Visualizzare il DNS inverso per gli indirizzi IP pubblici esistenti
È possibile visualizzare il valore configurato per un indirizzo IP pubblico esistente usando azure network public-ip show.

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Rimuovere il DNS inverso da indirizzi IP pubblici esistenti
È possibile rimuovere la proprietà DNS inverso da un indirizzo IP pubblico usando azure network public-ip set. Questa operazione viene eseguita impostando il valore della proprietà ReverseFqdn su un valore vuoto:

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[AZURE.INCLUDE [DOMANDE FREQUENTI](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!-----HONumber=AcomDC_0316_2016-->