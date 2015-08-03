<properties 
   pageTitle="Creare record DNS personalizzati per un'app Web | Microsoft Azure" 
   description="Come creare record DNS di un dominio personalizzato per un'app Web usando DNS di Azure. Procedura dettagliata per verificare la proprietà del dominio usando il record CNAME o A" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/20/2015"
   ms.author="joaoma"/>

# Creare record DNS per un'app Web in un dominio personalizzato

È possibile usare DNS di Azure per ospitare un dominio personalizzato per le app Web. Ad esempio, si supponga di creare un'app Web di Azure e di volere che gli utenti vi accedano usando contoso.com o www.contoso.com come FQDN. In questo scenario, sarebbe necessario creare due record: un record A radice, che fa riferimento a contoso.com, e un record CNAME per il nome www, che fa riferimento al record A.

> [AZURE.NOTE]Tenere presente che, se si crea un record A per un'app Web in Azure, il record A deve essere aggiornato manualmente se l'indirizzo IP sottostante per l'app Web cambia.

Prima di poter creare i record per il dominio personalizzato, è necessario creare una zona DNS in DNS di Azure e delegare la zona nel registrar a DNS di Azure. Per creare una zona DNS, seguire i passaggi in [Introduzione a DNS di Azure](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Per delegare il DNS a DNS di Azure, seguire i passaggi in [Delegare un dominio a DNS di Azure](../dns-domain-delegation).
 
## Creare un record A per il dominio personalizzato

Un record A viene usato per eseguire il mapping di un nome al relativo indirizzo IP. Nell'esempio seguente si assegnerà @ come record A a un indirizzo IPv4:

### Passaggio 1
 
Creare un record A e assegnarlo a una variabile $rs
	
	PS C:\>$rs=New-AzureDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Passaggio 2

Aggiungere al set di record "@" creato in precedenza il valore IPv4 usando la variabile $rs assegnata. Il valore di IPv4 assegnato sarà l'indirizzo IP per l'app Web.

> [AZURE.NOTE]Per trovare l'indirizzo IP per l'app Web, seguire i passaggi in [Configurare un nome di dominio personalizzato nel Servizio app di Azure](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Passaggio 3

Eseguire il commit delle modifiche al set di record. Usare Set-AzureDnsRecordSet per caricare le modifiche al set di record in DNS di Azure:

	Set-AzureDnsRecordSet -RecordSet $rs

## Creazione di un record CNAME per il dominio personalizzato

Presumendo che il dominio sia già gestito da DNS di Azure (vedere la [delega del dominio DNS](../dns-domain-delegation)), è possibile usare il seguente esempio per creare un record CNAME per contoso.azurewebsites.net:

### Passaggio 1

Aprire PowerShell, creare un nuovo set di record CNAME e assegnarlo a una variabile $rs:

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

Verrà creato un tipo di set di record CNAME con una "durata (TTL)" di 600 secondi nella zona DNS denominata "contoso.com".

### Passaggio 2

Una volta creato il set di record CNAME, è necessario creare un valore alias che farà riferimento all'app Web

Usando la variabile "$rs" assegnata in precedenza, è possibile usare il seguente comando di PowerShell per creare l'alias per l'app Web contoso.azurewebsites.net.

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Passaggio 3

Eseguire il commit delle modifiche usando il cmdlet Set-AzureDnsRecordSet:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

È possibile verificare che il record sia stato creato correttamente eseguendo una query di "www.contoso.com" con nslookup, come mostrato di seguito:

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Creare un record awverify per le app Web (soltanto record A)

Se si decide di usare un record A per l'app Web, è necessario eseguire un processo di verifica per consentire ad Azure di assicurarsi che il dominio personalizzato sia di proprietà dell'utente. Questo passaggio di verifica viene eseguito creando uno speciale record CNAME denominato "awverify".

Nel seguente esempio, il record "awverify" verrà creato per permettere a contoso.com di verificare la proprietà del dominio personalizzato:

### Passaggio 1

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Passaggio 2

Una volta creato il set di record awverify, è necessario assegnare l'alias del set di record CNAME ad awverify.contoso.azurewebsites.net, come mostrato nel comando seguente:

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Passaggio 3

Eseguire il commit delle modifiche usando il cmdlet Set-AzureDnsRecordSet, come mostrato nel comando seguente:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

Ora è possibile continuare a seguire i passaggi in [Configurazione di un nome di dominio personalizzato nel servizio app](../web-sites-custom-domain-name) per configurare l'app Web per l'uso di un dominio personalizzato.

## Vedere anche

[Gestire le zone DNS](../dns-operations-dnszones)

[Gestire i record DNS](../dns-operations-recordsets)

[Panoramica di Gestione traffico](../traffic-manager-overview)

[Automatizzare le operazioni di Azure con .NET SDK](../dns-sdk)


 

<!---HONumber=July15_HO4-->