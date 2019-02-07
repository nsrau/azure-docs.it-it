---
title: Azure Stack datacenter integration - pubblicano endpoint | Microsoft Docs
description: Informazioni su come pubblicare gli endpoint di Azure Stack nel tuo Data Center
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 0bb2f3ffb4b615451abc41d0d8945b4b3efdde53
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816357"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack datacenter integration - pubblicano endpoint

Azure Stack consente di impostare indirizzi IP virtuali (VIP) per i ruoli di infrastruttura. Questi indirizzi VIP sono allocati dal pool di indirizzi IP pubblici. Ogni indirizzo IP virtuale è protetta con un elenco di controllo di accesso (ACL) nel livello di software defined Networking. Gli ACL vengono usati anche per i commutatori fisici (tor e BMC) per rafforzare ulteriormente la soluzione. Viene creata una voce DNS per ogni endpoint nella zona DNS esterna specificati in fase di distribuzione.


Diagramma dell'architettura seguente mostra i vari livelli della rete e gli ACL:

![Immagine strutturali](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porte e protocolli (in ingresso)

Un set di infrastruttura di indirizzi VIP è necessario per la pubblicazione di endpoint di Azure Stack per le reti esterne. Il *Endpoint (indirizzo VIP)* tabella illustra ogni endpoint, la porta necessaria e protocollo. Vedere la documentazione sulla distribuzione di provider di risorse specifico per gli endpoint che richiedono provider di risorse aggiuntive, ad esempio il provider di risorse SQL.

Infrastruttura interna che non sono elencati gli indirizzi VIP perché non sono necessarie per la pubblicazione di Azure Stack.

> [!Note]  
> Utente VIP sono dinamici, definiti dagli utenti stessi senza il controllo dall'operatore di Azure Stack.

|Endpoint (indirizzo VIP)|Un record host DNS|Protocollo|Porte|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portale (amministratore)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (amministratore)|Adminmanagement.  *&lt;area >.&lt; FQDN >*|HTTPS|443<br>30024|
|Portale (utente)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (utente)|Gestione.  *&lt;area >.&lt; FQDN >*|HTTPS|443<br>30024|
|Grafico|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Elenco di revoche di certificati|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP E UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (utente)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (amministratore)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Coda di archiviazione|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabella di archiviazione|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Archiviazione BLOB|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Provider di risorse SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Provider di risorse MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Servizio app|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Gateway VPN|     |     |[Vedere le domande frequenti sul gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porte e URL (in uscita)

Azure Stack supporta solo i server proxy trasparente. In una distribuzione in cui un uplink proxy trasparente per un server proxy tradizionali, è necessario consentire gli URL e le porte seguenti per le comunicazioni in uscita:

> [!Note]  
> Azure Stack non supporta l'uso di Expressroute per raggiungere i servizi di Azure elencati nella tabella seguente.

|Scopo|URL di destinazione|Protocollo|Porte|Rete di origine|
|---------|---------|---------|---------|---------|
|Identità|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|VIP - /27 pubblici<br>Pubblica dell'infrastruttura di rete|
|Diffusione di Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|VIP - /27 pubblici|
|Aggiorna & patch|https://&#42;.azureedge.net|HTTPS|443|VIP - /27 pubblici|
|Registrazione|https://management.azure.com|HTTPS|443|VIP - /27 pubblici|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|VIP - /27 pubblici|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>https://www.microsoft.com/pkiops/crl<br>https://www.microsoft.com/pkiops/certs<br>https://crl.microsoft.com/pki/crl/products<br>https://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|VIP - /27 pubblici<br>Pubblica dell'infrastruttura di rete|
|NTP|(Server IP di NTP fornito per deployment)|UDP|123|VIP - /27 pubblici|
|DNS|(Server DNS dell'indirizzo IP fornito per deployment)|TCP<br>UDP|53|VIP - /27 pubblici|
|CRL|(URL sotto i punti di distribuzione CRL nel certificato)|HTTP|80|VIP - /27 pubblici|
|Backup dell'infrastruttura|(Indirizzo IP o nome di dominio completo del file server di destinazione esterno)|SMB|445|Pubblica dell'infrastruttura di rete|
|LDAP|Foresta di Active Directory specificato per l'integrazione di Graph|TCP<br>UDP|389|VIP - /27 pubblici|
|LDAP SSL|Foresta di Active Directory specificato per l'integrazione di Graph|TCP|636|VIP - /27 pubblici|
|LDAP GC|Foresta di Active Directory specificato per l'integrazione di Graph|TCP|3268|VIP - /27 pubblici|
|LDAP GC SSL|Foresta di Active Directory specificato per l'integrazione di Graph|TCP|3269|VIP - /27 pubblici|
|AD FS|Endpoint dei metadati di AD FS fornita per l'integrazione con AD FS|TCP|443|VIP - /27 pubblici|
|     |     |     |     |     |

> [!Note]  
> Gli URL in uscita sono con carico bilanciato usando Gestione traffico di Azure per fornire la migliore connettività possibili basate sulla posizione geografica. Con carico bilanciato URL, Microsoft è possibile aggiornare e modificare gli endpoint di back-end senza conseguenze per i clienti. Microsoft non condivide l'elenco di indirizzi IP per gli URL con bilanciamento del carico. È consigliabile usare un dispositivo che supporta i filtri dall'URL piuttosto che dall'IP.

> [!Note]  
> In 1809, il servizio backup di infrastruttura comunica al server di file esterno dalla rete VIP pubblica. Prima 1809, il servizio di comunicata attraverso la rete pubblica dell'infrastruttura. Se l'ambiente non consente l'accesso alle risorse di infrastruttura dalla rete VIP pubblica, applicare la versione più recente [hotfix 1809](azure-stack-update-1809.md#post-update-steps) per Azure Stack. Questo hotfix passerà nuovamente il servizio backup di infrastruttura alla rete dell'infrastruttura pubblico. In 1811, se si applica l'hotfix 1809, il servizio backup di infrastruttura rimane nella rete pubblica di infrastruttura. Se non si applica l'aggiornamento rapido, l'aggiornamento sposta nuovamente il servizio alla rete dell'infrastruttura pubblico.

## <a name="next-steps"></a>Passaggi successivi

[Requisiti di infrastruttura a chiave pubblica dello Stack di Azure](azure-stack-pki-certs.md)
