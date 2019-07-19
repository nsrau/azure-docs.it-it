---
title: Panoramica dei back-end multi-tenant, ad esempio app Azure Service, con applicazione Azure gateway
description: Questa pagina offre una panoramica del supporto del gateway applicazione per i back-end multi-tenant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 66e4a578e3f443f4cbc3f6e5467cf9a86adf05fe
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297055"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Supporto del gateway applicazione per back-end multi-tenant, ad esempio il servizio app

Nei progetti di architettura multi-tenant in server Web, più siti Web sono in esecuzione nella stessa istanza del server Web. I nomi host vengono usati per distinguere tra le diverse applicazioni ospitate. Per impostazione predefinita, il gateway applicazione non modifica l'intestazione host HTTP in ingresso dal client e invia l'intestazione invariata al back-end. Questo funziona bene per i membri del pool back-end, ad esempio schede di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni e FQDN, perché questi non si basano su un'intestazione host o un'estensione SNI specifica per la risoluzione nell'endpoint corretto. Tuttavia, esistono molti servizi, ad esempio app Azure app Web del servizio e gestione API di Azure che sono di natura multi-tenant e si basano su un'intestazione host o un'estensione SNI specifica per la risoluzione nell'endpoint corretto. In genere, il nome DNS dell'applicazione, che a sua volta è il nome DNS associato al gateway applicazione, è diverso dal nome di dominio del servizio back-end. Pertanto, l'intestazione host nella richiesta originale ricevuta dal gateway applicazione non corrisponde al nome host del servizio back-end. Per questo motivo, a meno che l'intestazione host nella richiesta dal gateway applicazione al back-end non venga modificata nel nome host del servizio back-end, i back-end multi-tenant non sono in grado di risolvere la richiesta nell'endpoint corretto. 

Il gateway applicazione offre una funzionalità che consente agli utenti di eseguire l'override dell'intestazione host HTTP nella richiesta in base al nome host di back-end. Questa funzionalità garantisce il supporto di back-end multi-tenant come le app Web del servizio app di Azure e la gestione delle API ed è disponibile sia per lo SKU standard v1 e v2 che per lo SKU WAF. 

![sostituzione host](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Questo non è applicabile a app Azure ambiente del servizio (ASE) poiché l'ambiente del servizio app è una risorsa dedicata a differenza di app Azure servizio che è una risorsa multi-tenant.

## <a name="override-host-header-in-the-request"></a>Esegui override dell'intestazione host nella richiesta

La possibilità di specificare un override dell'host è definita nelle [impostazioni http](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e può essere applicata a qualsiasi pool back-end durante la creazione della regola. Sono supportate le seguenti due modalità di override dell'intestazione host e dell'estensione SNI per i back-end multi-tenant:

- Possibilità di impostare il nome host su un valore fisso immesso in modo esplicito nelle impostazioni HTTP. Questa funzionalità garantisce che l'intestazione host venga sottoposta a override in questo valore per tutto il traffico verso il pool back-end in cui vengono applicate le impostazioni HTTP specifiche. Quando si usa SSL end-to-end, il nome host sostituito viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui la farm di un pool back-end prevede un'intestazione host diversa dall'intestazione host in ingresso del cliente.

- È possibile derivare il nome host dall'indirizzo IP o dal nome di dominio completo dei membri del pool back-end. Le impostazioni HTTP forniscono anche un'opzione per scegliere dinamicamente il nome host da un FQDN del membro del pool back-end se configurato con l'opzione per derivare il nome host da un singolo membro del pool back-end. Quando si usa SSL end-to-end, il nome host è derivato dal nome di dominio completo e viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui un pool back-end può avere due o più servizi PaaS multi-tenant, come App Web di Azure e l'intestazione host della richiesta per ogni membro contiene il nome host derivato dal rispettivo nome di dominio completo. Per l'implementazione di questo scenario, viene usata un'opzione nelle impostazioni HTTP denominata [pick hostname from back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) , che eseguirà l'override dinamico dell'intestazione host nella richiesta originale con quella indicata nel pool back-end.  Se, ad esempio, il nome di dominio completo del pool back-end contiene "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", l'intestazione host della richiesta originale contoso.com verrà sostituita a contoso11.azurewebsites.net o contoso22.azurewebsites.net Quando la richiesta viene inviata al server back-end appropriato. 

  ![Scenario con app Web](./media/application-gateway-web-app-overview/scenario.png)

Con questa funzionalità, i clienti specificano le opzioni nelle impostazioni HTTP e nei probe personalizzati in base alla configurazione appropriata. L'impostazione viene quindi collegata a un listener e un pool back-end usando una regola.

## <a name="special-considerations"></a>Considerazioni speciali

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Terminazione SSL e SSL end-to-end con servizi multi-tenant

Con i servizi multi-tenant è supportata sia la terminazione SSL che la crittografia SSL end-to-end. Per la terminazione SSL nel gateway applicazione, il certificato SSL continua a essere necessario per essere aggiunto al listener del gateway applicazione. Tuttavia, nel caso di SSL end-to-end, i servizi di Azure attendibili, ad esempio le app Web del servizio app Azure non richiedono l'inserimento dei backend nel gateway applicazione. Pertanto, non è necessario aggiungere alcun certificato di autenticazione. 

![SSL end-to-end](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Si noti che nell'immagine precedente non è necessario aggiungere certificati di autenticazione quando il servizio app è selezionato come back-end.

### <a name="health-probe"></a>Probe di integrità

L'override dell'intestazione host nelle **impostazioni http** influiscono solo sulla richiesta e sul relativo routing. non ha alcun effetto sul comportamento del probe di integrità. Per il funzionamento della funzionalità end-to-end, sia il probe che le impostazioni HTTP devono essere modificati per riflettere la configurazione corretta. Oltre a fornire la possibilità di specificare un'intestazione host nella configurazione del probe, i probe personalizzati supportano anche la possibilità di derivare l'intestazione host dalle impostazioni HTTP attualmente configurate. Questa configurazione può essere specificata usando il parametro `PickHostNameFromBackendHttpSettings` nella configurazione del probe.

### <a name="redirection-to-app-services-url-scenario"></a>Reindirizzamento allo scenario URL del servizio app

Possono verificarsi scenari in cui il nome host nella risposta dal servizio app può indirizzare il browser dell'utente finale al nome host *. azurewebsites.net anziché al dominio associato al gateway applicazione. Questo problema può verificarsi nei casi seguenti:

- È stato configurato il reindirizzamento nel servizio app. Il reindirizzamento può essere semplice quanto aggiungere una barra finale alla richiesta.
- Si dispone di Azure AD autenticazione che causa il reindirizzamento.

Per risolvere questi casi, vedere [risolvere i problemi relativi al reindirizzamento al problema di URL del servizio app](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare un gateway applicazione con un'app multi-tenant, ad esempio app Azure app Web del servizio come membro del pool back-end, visitando [configurare app Web del servizio app con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
