---
title: Panoramica dei back-end multi-tenant, ad esempio servizio App di Azure, con il Gateway applicazione di Azure
description: Questa pagina offre una panoramica del supporto del gateway applicazione per i back-end multi-tenant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 256fb42be8fec056ed7d10cfc4197a1b5a33fac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807172"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Termina con supporto del Gateway applicazione per il backup di multi-tenant, ad esempio servizio App

In progetti di architettura multi-tenant nel server web, più siti Web sono in esecuzione nella stessa istanza del server web. I nomi host vengono utilizzati per distinguere tra le diverse applicazioni ospitate. Per impostazione predefinita, il gateway applicazione non modifica l'intestazione host HTTP in ingresso dal client e invia l'intestazione invariata al back-end. Questo è ottimale per i membri del pool back-end, ad esempio schede di rete, i set di scalabilità, gli indirizzi IP pubblici, indirizzi IP di interno e nome di dominio completo come queste non fare affidamento su un'intestazione host specifica estensione SNI o per risolvere l'endpoint corretto. Tuttavia, esistono numerosi servizi, ad esempio App web del servizio App di Azure e gestione API di Azure che sono di natura multi-tenant e si basano su un'intestazione host specifica estensione SNI o per risolvere l'endpoint corretto. In genere, il nome DNS dell'applicazione, che a sua volta è il nome DNS associato al gateway applicazione, è diverso dal nome di dominio del servizio back-end. Pertanto, l'intestazione host nella richiesta originale ha ricevuta dal gateway applicazione non è lo stesso nome host del servizio back-end. Per questo motivo, a meno che l'intestazione host nella richiesta dal gateway applicazione per il back-end viene modificato il nome host del servizio back-end, back-end multi-tenant non sono in grado di risolvere la richiesta all'endpoint corretto. 

Il gateway applicazione offre una funzionalità che consente agli utenti di eseguire l'override dell'intestazione host HTTP nella richiesta in base al nome host di back-end. Questa funzionalità garantisce il supporto di back-end multi-tenant come le app Web del servizio app di Azure e la gestione delle API ed è disponibile sia per lo SKU standard v1 e v2 che per lo SKU WAF. 

![sostituzione dell'host](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Non applicabile all'ambiente del servizio App di Azure (ASE) dall'ambiente del servizio App è una risorsa dedicata a differenza del servizio App di Azure che è una risorsa di multi-tenant.

## <a name="override-host-header-in-the-request"></a>Eseguire l'override dell'intestazione host nella richiesta

La possibilità di specificare una sostituzione dell'host è definita nel [delle impostazioni HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) e può essere applicato a qualsiasi pool back-end durante la creazione della regola. Due modi seguenti dell'intestazione host e SNI per i back-end multi-tenant viene sottoposto a override è supportato:

- La possibilità di impostare il nome host su un valore fisso immesso in modo esplicito nelle impostazioni HTTP. Questa funzionalità garantisce che l'intestazione host viene eseguito l'override a questo valore per tutto il traffico al pool back-end in cui vengono applicate le impostazioni HTTP specifiche. Quando si usa SSL end-to-end, il nome host sostituito viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui la farm di un pool back-end prevede un'intestazione host diversa dall'intestazione host in ingresso del cliente.

- È possibile derivare il nome host dall'indirizzo IP o dal nome di dominio completo dei membri del pool back-end. Impostazioni HTTP offrono anche un'opzione per selezionare in modo dinamico il nome host dal nome di dominio completo del membro di un pool back-end se è configurato con la possibilità di derivare il nome host da un membro del pool di back-end singolo. Quando si usa SSL end-to-end, il nome host è derivato dal nome di dominio completo e viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui un pool back-end può avere due o più servizi PaaS multi-tenant, come App Web di Azure e l'intestazione host della richiesta per ogni membro contiene il nome host derivato dal rispettivo nome di dominio completo. Per implementare questo scenario, si usa un'opzione nelle impostazioni HTTP del chiamato [selezionare il nome host di indirizzi back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) che eseguirà l'override in modo dinamico l'intestazione host nella richiesta originale a quella indicata nel pool di back-end.  Ad esempio, se il nome FQDN del pool back-end contiene "contoso11.azurewebsites.net" e "contoso22.azurewebsites.net", intestazione host della richiesta originale che sia contoso.com avranno contoso11.azurewebsites.net o contoso22.azurewebsites.net Quando la richiesta viene inviata al server back-end appropriato. 

  ![Scenario con app Web](./media/application-gateway-web-app-overview/scenario.png)

Con questa funzionalità, i clienti specificano le opzioni nelle impostazioni HTTP e nei probe personalizzati in base alla configurazione appropriata. L'impostazione viene quindi collegata a un listener e un pool back-end usando una regola.

## <a name="special-considerations"></a>Considerazioni speciali

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>La terminazione SSL e SSL end to end con servizi multi-tenant

Sia la terminazione SSL-to-end della crittografia SSL è supportata con servizi multi-tenant. Per la terminazione SSL nel gateway applicazione, certificato SSL continua a essere necessari da aggiungere al listener del gateway applicazione. Tuttavia, in caso di SSL end to end, Azure attendibile servizi, ad esempio App web del servizio App di Azure non richiedono l'inserimento nella whitelist back-end nel gateway applicazione. Pertanto, non è necessario aggiungere i certificati di autenticazione. 

![SSL end-to-end](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Si noti che nell'immagine precedente, non è necessario aggiungere i certificati di autenticazione quando si seleziona servizio App come back-end.

### <a name="health-probe"></a>Probe di integrità

Si esegue l'override l'intestazione host nella **delle impostazioni HTTP** influisce solo sulla richiesta e del routing. non ha alcun impatto sul comportamento dei probe di integrità. Per il funzionamento della funzionalità end-to-end, sia il probe che le impostazioni HTTP devono essere modificati per riflettere la configurazione corretta. Oltre a offrire la possibilità di specificare un'intestazione host nella configurazione del probe, i probe personalizzati supportano anche la possibilità di derivare l'intestazione host dalle impostazioni HTTP attualmente configurate. Questa configurazione può essere specificata usando il parametro `PickHostNameFromBackendHttpSettings` nella configurazione del probe.

### <a name="redirection-to-app-services-url-scenario"></a>Reindirizzamento a uno scenario di URL del servizio App

Possono esistere scenari in cui il nome host nella risposta dal servizio App può indirizzare il browser dell'utente finale per la *. azurewebsites.net nome host invece del dominio associato al Gateway applicazione. Questo problema può presentarsi quando:

- È necessario il reindirizzamento configurato nel servizio App. Il reindirizzamento può essere semplice quanto l'aggiunta di una barra finale alla richiesta.
- È necessario l'autenticazione di Azure AD che causa il reindirizzamento.

Per risolvere questi casi, vedere [risolvere i problemi il reindirizzamento al problema di URL del servizio App](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare un gateway applicazione con un'app multi-tenant, ad esempio app web del servizio App di Azure come membro del pool back-end, vedere [le app web di configurare il servizio App con il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-web-app)
