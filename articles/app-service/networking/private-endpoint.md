---
title: Connettersi privatamente a un'app Web di Azure con un endpoint privato
description: Connettersi in privato a un'app Web usando un endpoint privato di Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 10/09/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: bebc7dcbc18a25b0d6d0761a8ca3ac476e83e581
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183040"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Uso di endpoint privati per l'app Web di Azure

> [!IMPORTANT]
> L'endpoint privato è disponibile per app Web Windows e Linux, in contenitori o non in contenitori, ospitati in questi piani di servizio app: **isolated**, **PremiumV2**, **PremiumV3**, **funzioni Premium** (talvolta definito piano elastico Premium). 

È possibile usare un endpoint privato per l'app Web di Azure per consentire ai client che si trovano nella rete privata di accedere in modo sicuro all'app tramite un collegamento privato. L'endpoint privato usa un indirizzo IP dello spazio indirizzi della VNet di Azure. Il traffico di rete tra un client nella rete privata e l'app Web attraversa la VNet e un collegamento privato sulla rete backbone Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di un endpoint privato per l'app Web consente di:

- Proteggere l'app Web configurando l'endpoint privato, eliminando l'esposizione pubblica.
- Connettersi in modo sicuro all'app Web da reti locali che si connettono alla VNet usando un peering privato VPN o ExpressRoute.
- Evitare l'esfiltrazione dei dati dalla VNet. 

Se è necessaria una connessione sicura tra la VNet e l'app Web, un endpoint servizio è la soluzione più semplice. Se è necessario anche raggiungere l'app Web dall'ambiente locale tramite un gateway di Azure, una VNet con peering a livello di area o un VNet con peering globale, l'endpoint privato è la soluzione.  

Per altre informazioni, vedere [Endpoint servizio][serviceendpoint].

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete (NIC) speciale per l'app Web di Azure in una subnet nella rete virtuale (VNet).
Quando si crea un endpoint privato per l'app Web, tale endpoint fornisce connettività sicura tra i client nella rete privata e l'app Web. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet.
La connessione tra l'endpoint privato e l'app Web usa un [collegamento privato][privatelink] sicuro. L'endpoint privato viene usato solo per i flussi in ingresso nell'app Web. I flussi in uscita non useranno questo endpoint privato, ma è possibile inserire i flussi in uscita nella rete in una subnet diversa tramite la [funzionalità di integrazione VNet][vnetintegrationfeature].

La subnet a cui si collega l'endpoint privato può includere altre risorse, non è necessaria una subnet vuota dedicata.
È anche possibile distribuire l'endpoint privato in un'area diversa da quella dell'app Web. 

> [!Note]
>La funzionalità di integrazione VNet non può usare la stessa subnet dell'endpoint privato. si tratta di una limitazione della funzionalità di integrazione di VNet.

Dal punto di vista della sicurezza:

- Quando si abilitano endpoint privati nell'app Web, si disabilitano tutti gli accessi pubblici.
- È possibile abilitare più endpoint privati in altre VNet e subnet, incluse VNet in altre aree.
- L'indirizzo IP dell'interfaccia di rete dell'endpoint privato deve essere dinamico, ma rimane invariato finché non si elimina l'endpoint privato.
- All'interfaccia di rete dell'endpoint privato non può essere associato un gruppo di sicurezza di rete.
- La subnet che ospita l'endpoint privato può avere un gruppo di sicurezza di rete associato, ma è necessario disabilitare l'imposizione dei criteri di rete per l'endpoint privato. Vedere [Disabilitare i criteri di rete per gli endpoint privati][disablesecuritype]. Di conseguenza, non è possibile filtrare in base a qualsiasi gruppo di sicurezza di rete l'accesso all'endpoint privato.
- Quando si abilita l'endpoint privato per l'app Web, la configurazione delle [restrizioni di accesso][accessrestrictions] dell'app Web non viene valutata.
- È possibile eliminare il rischio di esfiltrazione dei dati dalla VNet rimuovendo tutte le regole NSG in cui la destinazione corrisponde al tag Internet o servizi di Azure. Quando si distribuisce un endpoint privato per un'app Web, è possibile raggiungere questa specifica app Web solo tramite l'endpoint privato. Se è disponibile un'altra app Web, è necessario distribuire un altro endpoint privato dedicato per quest'altra app Web.

Nei log HTTP Web dell'app Web è presente l'IP di origine del client. Questa funzionalità viene implementata usando il protocollo proxy TCP, che invia la proprietà IP del client all'app Web. Per altre informazioni, vedere [Ottenere informazioni sulla connessione usando il protocollo proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Panoramica globale dell'endpoint privato dell'app Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Quando si usa l'endpoint privato per l'app Web, l'URL richiesto deve corrispondere al nome dell'app Web. Per impostazione predefinita mywebappname.azurewebsites.net.

Per impostazione predefinita, senza endpoint privato, il nome pubblico dell'app Web è un nome canonico per il cluster.
Ad esempio, la risoluzione dei nomi sarà:

|Nome |Type |valore |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Quando si distribuisce un endpoint privato, la voce DNS viene aggiornata in modo che punti al nome canonico mywebapp.privatelink.azurewebsites.net.
Ad esempio, la risoluzione dei nomi sarà:

|Nome |Type |valore |Commento |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<: questo IP pubblico non è l'endpoint privato. verrà visualizzato un errore 403.|

È necessario configurare un server DNS privato o una zona privata di DNS di Azure, per i test è possibile modificare la voce host del computer di test.
La zona DNS che è necessario creare è: **privatelink.azurewebsites.NET**. Registrare il record per l'app Web con un record A e l'IP dell'endpoint privato.
Ad esempio, la risoluzione dei nomi sarà:

|Nome |Type |valore |Commento |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<: Azure crea questa voce nel DNS pubblico di Azure per puntare il servizio app al privatelink e questo è gestito da Microsoft|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<: si gestisce questa voce nel sistema DNS per puntare all'indirizzo IP dell'endpoint privato|

Dopo questa configurazione DNS, è possibile raggiungere l'app Web privata con il nome predefinito mywebappname.azurewebsites.net. È necessario utilizzare questo nome, perché il certificato predefinito viene emesso per *. azurewebsites.net.


Se occorre usare un nome DNS personalizzato, è necessario aggiungere il nome personalizzato nell'app Web. Il nome personalizzato deve essere convalidato come qualsiasi nome personalizzato, usando la risoluzione DNS pubblica. Per altre informazioni, vedere [Convalida DNS personalizzata][dnsvalidation].

Per la console Kudu o l'API REST Kudu (distribuzione con gli agenti self-hosted di Azure DevOps, ad esempio), è necessario creare due record nella zona privata di DNS di Azure o nel server DNS personalizzato. 

| Nome | Type | valore |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure][pricing].

## <a name="limitations"></a>Limitazioni

Quando si usa una funzione di Azure in un piano elastico Premium con endpoint privato, per eseguire la funzione nel portale Web di Azure, è necessario avere accesso diretto alla rete oppure si riceverà un errore HTTP 403. In altre parole, il browser deve essere in grado di raggiungere l'endpoint privato per eseguire la funzione dal portale Web di Azure. 

È possibile connettersi fino a 100 endpoint privato a un'app Web specifica.

Gli slot non possono usare un endpoint privato.

La funzionalità di debug remoto non è disponibile quando l'endpoint privato è abilitato per l'app Web. Si consiglia di distribuire il codice in uno slot e di eseguirne il debug remoto.

Vengono introdotti regolarmente miglioramenti per la funzionalità di collegamento privato e l'endpoint privato. Vedere [questo articolo][pllimitations] per informazioni aggiornate sulle limitazioni.

## <a name="next-steps"></a>Passaggi successivi

- Per distribuire un endpoint privato per l'app Web tramite il portale, vedere la pagina relativa alla [modalità di connessione privata a un'app Web con il portale][howtoguide1]
- Per distribuire endpoint privato per l'app Web usando l'interfaccia della riga di comando di Azure, vedere [come connettersi privatamente a un'app Web con l'interfaccia][howtoguide2] della riga di comando di Azure
- Per distribuire un endpoint privato per l'app Web usando PowerShell, vedere [come connettersi privatamente a un'app Web con PowerShell][howtoguide3]
- Per distribuire un endpoint privato per l'app Web usando il modello di Azure, vedere la pagina relativa alla [modalità di connessione privata a un'app Web con il modello di Azure][howtoguide4]
- Esempio end-to-end: come connettere un'app Web front-end a un'app Web back-end protetta con VNet Injection e un endpoint privato con il modello ARM, vedere questa [Guida introduttiva][howtoguide5]
- Esempio end-to-end: come connettere un'app Web front-end a un'app Web back-end protetta con VNet Injection e un endpoint privato con la bonifica, vedere questo [esempio][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md