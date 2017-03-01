---
title: Introduzione al test in ambiente di produzione per le app Web
description: "Informazioni sulla funzionalità di test in ambiente di produzione in app Web del servizio app di Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 4623468d-886e-4203-8012-8f86deb2790b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f9d423e87938e55daeb07268b11c930fb62755b6
ms.openlocfilehash: 8fa7ca18fd2ea3a3f672854a0b2e0397cfdbb2bc
ms.lasthandoff: 12/14/2016


---
# <a name="get-started-with-test-in-production-for-web-apps"></a>Introduzione al test in ambiente di produzione per le app Web
Il test in ambiente di produzione, ovvero il test in diretta dell'app Web con il traffico dei clienti live, è una strategia di test che gli sviluppatori di app integrano sempre più spesso nella metodologia di [sviluppo Agile](https://en.wikipedia.org/wiki/Agile_software_development) . Consente di testare la qualità delle app con il traffico utente live nell'ambiente di produzione, invece dei dati sintetizzati in un ambiente di test. Esponendo la nuova app agli utenti reali, è possibile venire a conoscenza dei veri problemi che potrebbero verificarsi nell'app una volta distribuita. È possibile verificare la funzionalità, le prestazioni e il valore degli aggiornamenti app rispetto al volume, alla velocità e alla varietà del traffico utente reale, che non è mai possibile prevedere in un ambiente di test.

## <a name="traffic-routing-in-app-service-web-apps"></a>Routing del traffico in app Web del servizio app
Con la funzionalità Routing del traffico nel [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) è possibile indirizzare una parte del traffico utente live a uno o più [slot di distribuzione](web-sites-staged-publishing.md) e quindi analizzare l'app con [Azure Application Insights](/services/application-insights/), [Azure HDInsight](/services/hdinsight/) o uno strumento di terze parti, ad esempio [New Relic](/marketplace/partners/newrelic/newrelic/), per convalidare la modifica. Con il servizio app, ad esempio, è possibile implementare gli scenari seguenti:

* Individuare i bug funzionali o trovare i colli di bottiglia delle prestazioni negli aggiornamenti prima della distribuzione a livello di sito
* Effettuare "voli di prova controllati" delle modifiche misurando la metrica di usabilità nell'app beta
* Passare gradualmente a un nuovo aggiornamento e, in caso di errore, tornare semplicemente alla versione corrente 
* Ottimizzare i risultati aziendali dell'app eseguendo [test A/B](https://en.wikipedia.org/wiki/A/B_testing) o [test multivariati](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) in più slot di distribuzione

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Requisiti per l'uso di Routing del traffico nelle app Web
* L'app Web deve essere eseguita nel piano **Standard** o **Premium**, come richiesto per slot di distribuzione multipli.
* Per il corretto funzionamento di Routing del traffico, i cookie devono essere abilitati nel browser degli utenti. Routing del traffico usa i cookie per aggiungere un browser client a uno slot di distribuzione per tutta la durata della sessione client.
* Routing del traffico supporta scenari di test in ambiente di produzione avanzati grazie ai cmdlet di Azure PowerShell.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Instradare un segmento di traffico a uno slot di distribuzione
A livello di base in ogni scenario di test in ambiente di produzione, si instrada una percentuale predefinita di traffico live a uno slot di distribuzione non di produzione. A questo scopo, eseguire la procedura seguente.

> [!NOTE]
> Questi passaggi presuppongono che sia già disponibile uno [slot di distribuzione non di produzione](web-sites-staged-publishing.md) in cui sia già stato [distribuito](web-sites-deploy.md) il contenuto dell'app Web.
> 
> 

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel pannello dell'app Web fare clic su **Impostazioni** > **Routing del traffico**.
   ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Selezionare lo slot a cui instradare il traffico e la percentuale del traffico totale desiderata, quindi fare clic su **Salva**.
   
    ![](./media/app-service-web-test-in-production/02-select-slot.png)
4. Passare al pannello dello slot di distribuzione. Dovrebbe essere visualizzato il traffico live che viene instradato allo slot.
   
    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Una volta configurato Routing del traffico, la percentuale specificata di client verrà instradata casualmente allo slot non di produzione. È importante, tuttavia, notare che un client, una volta automaticamente instradato a uno slot specifico, verrà aggiunto a tale slot per tutta la durata della sessione client. Per aggiungere la sessione utente, viene usato un cookie. Se si osservano le richieste HTTP, si noterà un cookie `TipMix` in ogni richiesta successiva.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Forzare le richieste client in uno slot specifico
Oltre al routing automatico del traffico, il servizio app può instradare le richieste a uno slot specifico. Ciò risulta utile se si vuole consentire agli utenti di acconsentire esplicitamente o rifiutare esplicitamente l'app beta. A questo scopo, si usa il parametro di query `x-ms-routing-name` .

Per reinstradare gli utenti a uno slot specifico usando `x-ms-routing-name`, è necessario verificare che lo slot sia già stato aggiunto all'elenco di Routing del traffico. Poiché si vuole eseguire il routing esplicito a uno slot, la percentuale di routing effettiva non è importante. Se necessario, è possibile creare un "collegamento beta" su cui gli utenti possono fare clic per accedere all'app beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Consentire agli utenti di rifiutare esplicitamente l'app beta
Per permettere agli utenti di rifiutare esplicitamente l'app beta, ad esempio, è possibile inserire questo collegamento nella pagina Web:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

La stringa `x-ms-routing-name=self` specifica lo slot di produzione. Una volta che il browser client accede al collegamento, non solo viene reindirizzato allo slot di produzione, ma ogni richiesta successiva conterrà il cookie `x-ms-routing-name=self` che aggiunge la sessione allo slot di produzione.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Consentire agli utenti di acconsentire esplicitamente all'app beta
Per permettere agli utenti di acconsentire esplicitamente all'app beta, impostare lo stesso parametro di query sul nome dello slot non di produzione, ad esempio:

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Altre risorse
* [Configurare ambienti di staging per le app Web nel servizio app di Azure](web-sites-staged-publishing.md)
* [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
* [Agile Software Development con il servizio app di Azure](app-service-agile-software-development.md)
* [Usare efficacemente gli ambienti DevOps per le app Web](app-service-web-staged-publishing-realworld-scenarios.md)


