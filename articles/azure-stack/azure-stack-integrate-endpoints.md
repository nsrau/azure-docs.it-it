---
title: 'Azure dello Stack di integrazione di datacenter: pubblicare endpoint'
description: Viene descritto come pubblicare endpoint dello Stack di Azure nel Data Center
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 01/16/2018
ms.author: victorh
keywords: 
ms.openlocfilehash: 1cc74cb2214918d6bfd0c0827cf5d9832b84f317
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure dello Stack di integrazione di datacenter: pubblicare endpoint

*Si applica a: Azure Stack integrate di sistemi*

Stack di Azure consente di impostare vari endpoint (gli indirizzi VIP - indirizzi IP virtuali) per i ruoli di infrastruttura. Questi indirizzi VIP sono allocati dal pool di indirizzi IP pubblici. Ogni indirizzo IP virtuale è protetta con un elenco di controllo di accesso (ACL) nel livello di rete definita dal software. Gli ACL vengono usati anche tra i commutatori fisici (tori e BMC) per un'ulteriore protezione avanzata della soluzione. Una voce DNS viene creata per ogni endpoint nella zona DNS esterna che è stata specificata in fase di distribuzione.


Il diagramma dell'architettura seguente illustra i vari livelli della rete e gli ACL:

![Diagramma dell'architettura](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porte e protocolli (in ingresso)

Nella tabella seguente sono elencati i VIP infrastruttura necessari per una pubblicazione di endpoint Azure Stack a reti esterne. L'elenco Mostra ogni endpoint, la porta richiesta e protocollo. Gli endpoint necessari per i provider di risorse aggiuntive, come il provider di risorse SQL e altri, sono vedere la documentazione sulla distribuzione di provider di risorse specifico.

Gli indirizzi VIP infrastruttura interna che non sono elencate perché non sono necessarie per la pubblicazione dello Stack di Azure.

> [!NOTE]
> Gli indirizzi VIP utente sono dinamici, definiti dagli utenti stessi senza il controllo dall'operatore dello Stack di Azure.


|Endpoint (VIP)|Un record host DNS|Protocollo|Porte|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portale (amministratore)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Gestione risorse di Azure (amministratore)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portale (utente)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Gestione risorse di Azure (utente)|Gestione.  *&lt;area >.&lt; nome di dominio completo >*|HTTPS|443<br>30024|
|Grafico|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Elenco di revoche di certificati|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP / UDP|53|
|Chiave dell'insieme di credenziali (utente)|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Chiave dell'insieme di credenziali (amministratore)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Coda di archiviazione|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabella di archiviazione|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Archiviazione BLOB|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Porte e gli URL (in uscita)

Stack di Azure supporta solo i server proxy trasparente. In una distribuzione in cui un uplink proxy trasparente per un server proxy tradizionali, è necessario consentire le seguenti porte e gli URL per le comunicazioni in uscita:


|Scopo|URL|Protocollo|Porte|
|---------|---------|---------|---------|
|Identità|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Diffusione di Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Patch e aggiornamento|https://&#42;.azureedge.net|HTTPS|443|
|Registrazione|https://management.azure.com|HTTPS|443|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|

## <a name="firewall-publishing"></a>Pubblicazione di firewall

Le porte elencate nella sezione precedente riguardano comunicazione in ingresso durante la pubblicazione di servizi di Azure Stack attraverso un firewall esistenti.

È consigliabile che per utilizzare un dispositivo firewall sicura dello Stack di Azure. Tuttavia, non è un requisito. Sebbene i firewall consente ad esempio attacchi distribuiti di tipo denial of service (DDOS) e l'ispezione del contenuto, possono anche diventare un collo di bottiglia della velocità effettiva per servizi di archiviazione di Azure come BLOB, tabelle e code.

In base al modello di identità (Azure Active Directory o AD FS), Microsoft potrà o potrebbe non essere necessario pubblicare l'endpoint ADFS. Se viene utilizzata una modalità di distribuzione disconnesso, è necessario pubblicare l'endpoint ADFS. (Per ulteriori informazioni, vedere l'argomento di identità di integrazione Datacenter).

Gli endpoint di chiave dell'insieme di credenziali (amministratore), Gestione risorse di Azure (amministratore) e portale dell'amministratore non richiedono necessariamente pubblicazione esterna. A seconda dello scenario. Ad esempio, come un provider di servizi, è opportuno limitare la superficie di attacco e amministrare solo Stack di Azure all'interno della rete e non da Internet.

Per un'organizzazione, la rete esterna può essere la rete azienda esistente. In questo caso, è necessario pubblicare gli endpoint per il funzionamento di Azure Stack dalla rete aziendale.

## <a name="edge-firewall-scenario"></a>Scenario di firewall perimetrale

In una distribuzione di bordo, Stack di Azure viene distribuito direttamente dietro il router perimetrale (fornito dal provider del servizio), con o senza un firewall trovano davanti.

![Diagramma dell'architettura di una distribuzione di bordo dello Stack di Azure](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

In genere, gli indirizzi IP instradabili pubblici vengono specificati per il pool di indirizzi VIP pubblico in fase di distribuzione in una distribuzione di bordo. Questo scenario consente a un utente provare l'esperienza del cloud self-controllata completo come un pubblica cloud come Azure.

### <a name="using-nat"></a>Utilizzo di NAT

Anche se non è consigliata a causa dell'overhead, è possibile utilizzare Network Address Translation (NAT) per la pubblicazione di endpoint. Per la pubblicazione di endpoint che è completamente controllata dagli utenti, questo richiede una regola NAT per ogni utente VIP che contiene tutte le porte che utilizzabili dall'utente.

Un'altra considerazione è che Azure non supporta la configurazione di un tunnel VPN a un endpoint tramite NAT in uno scenario di cloud ibrido con Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Scenario firewall di rete intranet/Enterprise/perimetrale

In una distribuzione intranet/enterprise/perimetrale, Stack di Azure viene distribuito all'interno del firewall di secondo, ovvero in genere parte di una rete perimetrale (detta anche DMZ).

![Scenario firewall di Azure Stack](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Se gli indirizzi IP instradabili pubblici sono stati specificati per il pool di indirizzi VIP pubblico dello Stack di Azure, questi indirizzi logicamente appartengano alla rete perimetrale e richiedono regole firewall primario di pubblicazione.

### <a name="using-nat"></a>Utilizzo di NAT

Se gli indirizzi IP instradabili pubblici vengono utilizzati per il pool di indirizzi VIP pubblico dello Stack di Azure, NAT viene utilizzato nel firewall secondario per pubblicare endpoint dello Stack di Azure. In questo scenario, è necessario configurare le regole di pubblicazione nel firewall primario oltre il bordo e del firewall secondario. Se si desidera utilizzare NAT, tenere presente quanto segue:

- NAT comporta un overhead aggiuntivo per la gestione di regole del firewall perché gli utenti di controllare i propri endpoint e le proprie regole di pubblicazione definita dal software (SDN) dello stack di rete. Gli utenti devono contattare l'operatore di Stack di Azure per ottenere i relativi indirizzi IP virtuali pubblicate e per aggiornare l'elenco delle porte.
- Durante l'utilizzo NAT limita l'esperienza utente, offre controllo completo all'operatore tramite le richieste di pubblicazione.
- Per scenari di cloud ibrido con Azure, è consigliabile che Azure non supporta la configurazione di un tunnel VPN a un endpoint tramite NAT.


## <a name="next-steps"></a>Passaggi successivi

[Integrazione di Azure Data Center di Stack - sicurezza](azure-stack-integrate-security.md)