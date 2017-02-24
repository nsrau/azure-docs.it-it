---
title: Creare record DNS personalizzati per un&quot;app Web | Documentazione Microsoft
description: Come creare record DNS di un dominio personalizzato per un&quot;app Web usando DNS di Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: a2a429873c30f526a0de05d4018f53f3a83bbe28

---

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Creare record DNS per un'app Web in un dominio personalizzato

È possibile usare DNS di Azure per ospitare un dominio personalizzato per le app Web. Se, ad esempio, si crea un'app Web di Azure e si vuole che gli utenti vi accedano usando contoso.com o www.contoso.com come FQDN.

A tale scopo, è necessario creare due record:

* Un record radice "A" che fa riferimento a contoso.com
* Un record "CNAME" per il nome www che punta al record A

Tenere presente che, se si crea un record A per un'app Web in Azure, il record A deve essere aggiornato manualmente se l'indirizzo IP sottostante per l'app Web cambia.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario innanzitutto creare una zona DNS in DNS di Azure e delegare la zona nel registrar a DNS di Azure.

1. Per creare una zona DNS, seguire i passaggi in [Creare una zona DNS](dns-getstarted-create-dnszone.md).
2. Per delegare il DNS a DNS di Azure, seguire i passaggi nell'articolo relativo alla [delega del dominio DNS](dns-domain-delegation.md).

Dopo la creazione di una zona e la relativa delega a DNS di Azure, è quindi possibile creare record per il dominio personalizzato.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Creare un record A per il dominio personalizzato

Un record A viene usato per eseguire il mapping di un nome al relativo indirizzo IP. Nell'esempio seguente si assegnerà @ come record A a un indirizzo IPv4:

### <a name="step-1"></a>Passaggio 1

Creare un record A e assegnarlo a una variabile $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Passaggio 2

Aggiungere al set di record "@" creato in precedenza il valore IPv4 usando la variabile $rs assegnata. Il valore di IPv4 assegnato sarà l'indirizzo IP per l'app Web.

Per trovare l'indirizzo IP per un'app Web, seguire i passaggi in [Configurare un nome di dominio personalizzato nel servizio app di Azure](../app-service-web/web-sites-custom-domain-name.md#vip).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Passaggio 3

Eseguire il commit delle modifiche al set di record. Usare `Set-AzureRMDnsRecordSet` per caricare le modifiche al set di record in DNS di Azure.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Creare un record CNAME per il dominio personalizzato

Se il dominio è già gestito da DNS di Azure (vedere la [delega del dominio DNS](dns-domain-delegation.md)), è possibile usare l'esempio seguente per creare un record CNAME per contoso.azurewebsites.net.

### <a name="step-1"></a>Passaggio 1

Aprire PowerShell, creare un nuovo set di record CNAME e assegnarlo a una variabile $rs. In questo esempio viene creato un tipo di set di record CNAME con una "durata (TTL)" di 600 secondi nella zona DNS denominata "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

L'esempio seguente corrisponde alla risposta.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Passaggio 2

Una volta creato il set di record CNAME, è necessario creare un valore alias che farà riferimento all'app Web

Usando la variabile "$rs" assegnata in precedenza, è possibile usare il seguente comando di PowerShell per creare l'alias per l'app Web contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

L'esempio seguente corrisponde alla risposta.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Passaggio 3

Confermare le modifiche usando il cmdlet `Set-AzureRMDnsRecordSet` :

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

È possibile verificare che il record sia stato creato correttamente eseguendo una query di "www.contoso.com" con nslookup, come mostrato di seguito:

```
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
```

## <a name="create-an-awverify-record-for-web-apps"></a>Creare un record "awverify" per le app Web

Se si decide di usare un record A per l'app Web, è necessario eseguire un processo di verifica per assicurarsi che il dominio personalizzato sia di proprietà dell'utente. Questo passaggio di verifica viene eseguito creando uno speciale record CNAME denominato "awverify". Questa sezione si applica solo ai record A.

### <a name="step-1"></a>Passaggio 1

Creare il record "awverify". In questo esempio verrà creato il record "awverify" per consentire a contoso.com di verificare la proprietà del dominio personalizzato.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

L'esempio seguente corrisponde alla risposta.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Passaggio 2

Dopo aver creato il set di record "awverify", assegnare l'alias del set di record CNAME. Nell'esempio seguente, si assegnerà l'alias del set di record CNAME a awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

L'esempio seguente corrisponde alla risposta.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Passaggio 3

Eseguire il commit delle modifiche usando `Set-AzureRMDnsRecordSet cmdlet`, come mostrato nel comando seguente.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'app Web per l'uso di un dominio personalizzato, seguire i passaggi in [Configurazione di un nome di dominio personalizzato nel servizio app](../app-service-web/web-sites-custom-domain-name.md) .



<!--HONumber=Feb17_HO2-->


