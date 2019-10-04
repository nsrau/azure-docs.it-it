---
title: "Esercitazione: Creare record personalizzati di DNS di Azure per un'app Web"
description: In questa esercitazione verranno creati record DNS di un dominio personalizzato per un'app Web usando DNS di Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 9d7a277db7550c1850ec0c9d555553064ab19f7c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730264"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Esercitazione: Creare record DNS in un dominio personalizzato per un'app Web 

È possibile configurare DNS di Azure per ospitare un dominio personalizzato per le app Web. Ad esempio, è possibile creare un'app Web di Azure e consentire agli utenti di accedervi usando www\.contoso.com o contoso.com come nome di dominio completo (FQDN).

> [!NOTE]
> Contoso.com viene usato come esempio in questa esercitazione. Sostituire contoso.com con il nome del proprio dominio.

A tale scopo, è necessario creare tre record:

* Un record radice "A" che fa riferimento a contoso.com
* Un record "TXT" radice per la verifica
* Un record "CNAME" per il nome www che punta al record A

Tenere presente che, se si crea un record A per un'app Web in Azure, il record A deve essere aggiornato manualmente se l'indirizzo IP sottostante per l'app Web cambia.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un record A e TXT per il dominio personalizzato
> * Creare un record CNAME per il dominio personalizzato
> * Testare i nuovi record
> * Aggiungere nomi host personalizzati all'app Web
> * Testare i nomi host personalizzati


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Per il test deve essere disponibile un nome di dominio che possa essere ospitato in DNS di Azure. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.
* [Creare un'app del servizio app](../app-service/app-service-web-get-started-html.md) oppure usare un'app creata per un'altra esercitazione.

* Creare una zona DNS in DNS di Azure e delegare la zona nel registrar a DNS di Azure.

   1. Per creare una zona DNS, seguire i passaggi in [Creare una zona DNS](dns-getstarted-create-dnszone.md).
   2. Per delegare la zona a DNS di Azure, seguire i passaggi nell'articolo relativo alla [delega del dominio DNS](dns-delegate-domain-azure-dns.md).

Dopo la creazione di una zona e la relativa delega a DNS di Azure, è quindi possibile creare record per il dominio personalizzato.

## <a name="create-an-a-record-and-txt-record"></a>Creare un record e un record TXT

Un record A viene usato per eseguire il mapping di un nome al relativo indirizzo IP. Nell'esempio seguente assegnare "\@" come record A usando l'indirizzo IPv4 dell'app Web. \@ in genere rappresenta il dominio radice.

### <a name="get-the-ipv4-address"></a>Ottenere l'indirizzo IPv4

Nel riquadro di spostamento a sinistra della pagina Servizi app nel portale di Azure selezionare **Domini personalizzati**. 

![Menu del dominio personalizzato](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Nella pagina **Domini personalizzati** copiare l'indirizzo IPv4 dell'applicazione:

![Passaggio all'app di Azure nel portale](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Creazione di un record A

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Creare il record TXT

Servizi app usa questo record solo in fase di configurazione per verificare che si sia i proprietari del dominio personalizzato. Dopo che il dominio personalizzato è stato convalidato e configurato nel servizio app, è possibile eliminare il record TXT.

> [!NOTE]
> Se si vuole verificare il nome di dominio senza indirizzare il traffico di produzione all'app Web, è sufficiente specificare il record TXT per il passaggio della verifica.  La verifica non richiede un record A o CNAME oltre al record TXT.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Creazione di un record CNAME

Se il dominio è già gestito da DNS di Azure (vedere la [delega del dominio DNS](dns-domain-delegation.md)), è possibile usare l'esempio seguente per creare un record CNAME per contoso.azurewebsites.net.

Aprire Azure PowerShell e creare un nuovo record CNAME. In questo esempio viene creato un set di record di tipo CNAME con una durata (TTL) di 600 secondi nella zona DNS denominata "contoso.com" con l'alias per l'app Web contoso.azurewebsites.net.

### <a name="create-the-record"></a>Creare il record

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

L'esempio seguente corrisponde alla risposta:

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

## <a name="test-the-new-records"></a>Testare i nuovi record

È possibile verificare che i record siano stati creati correttamente eseguendo una query di "www.contoso.com" e "contoso.com" con nslookup, come mostrato di seguito:

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

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Aggiungere i nomi host personalizzati

A questo punto, è possibile aggiungere i nomi host personalizzati all'app Web:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testare i nomi host personalizzati

Aprire un browser e passare a `http://www.<your domainname>` e `http://<you domain name>`.

> [!NOTE]
> Assicurarsi di includere il prefisso `http://`. In caso contrario, il browser potrebbe tentare di prevedere automaticamente un URL.

Dovrebbe essere visualizzata la stessa pagina per entrambi gli URL. Ad esempio:

![Servizio app Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create in questa esercitazione non sono più necessarie, è possibile eliminare il gruppo di risorse **myresourcegroup**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare zone private in DNS di Azure.

> [!div class="nextstepaction"]
> [Introduzione alle zone private di DNS di Azure con PowerShell](private-dns-getstarted-powershell.md)
