---
title: Avvio rapido - Introduzione all'app di esempio - Azure Spring Cloud
description: Descrive l'app di esempio usata in questa serie di argomenti di avvio rapido per la distribuzione in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903611"
---
# <a name="introduction-to-the-sample-app"></a>Introduzione all'app di esempio

::: zone pivot="programming-language-csharp"
Questa serie di argomenti di avvio rapido usa un'app di esempio costituita da due microservizi per illustrare come distribuire un'app .NET Core Steeltoe nel servizio Azure Spring Cloud. Si useranno le funzionalità Azure Spring Cloud, come l'individuazione dei servizi, il server di configurazione, i log, le metriche e la traccia distribuita.

## <a name="functional-services"></a>Servizi funzionali

L'app di esempio è costituita da due microservizi:

* Il servizio `planet-weather-provider` restituisce il testo relativo alle previsioni meteorologiche in risposta a una richiesta HTTP che specifica il nome del pianeta. Può ad esempio restituire "very warm" per il pianeta Mercurio. Ottiene i dati delle previsioni meteorologiche dal server di configurazione. Il server di configurazione ottiene i dati delle previsioni meteorologiche da un file YAML in un repository Git, ad esempio:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* Il servizio `solar-system-weather` restituisce i dati relativi a quattro pianeti in risposta a una richiesta HTTP. Ottiene i dati creando quattro richieste HTTP per `planet-weather-provider`. Usa il servizio di individuazione del server Eureka per chiamare `planet-weather-provider`. Restituisce codice JSON, ad esempio:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Nella figura seguente viene illustrata l'architettura dell'app di esempio:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagramma dell'app di esempio":::

## <a name="code-repository"></a>Repository di codice

L'app di esempio è contenuta nella cartella [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) del repository Azure-Samples/Azure-Spring-Cloud-Samples in GitHub.

Le istruzioni degli argomenti di avvio rapido seguenti fanno riferimento al codice sorgente in base alle esigenze.

::: zone-end

::: zone pivot="programming-language-java"
In questo argomento di avvio rapido viene usata un'app di esempio per la gestione del bilancio personale, denominata PiggyMetrics, per illustrare come distribuire un'app nel servizio Azure Spring Cloud. PiggyMetrics illustra il modello di architettura di microservizi mettendo in evidenza la suddivisione dei servizi. Si vedrà come viene distribuita in Azure con potenti funzionalità di Azure Spring Cloud, come l'individuazione dei servizi, il server di configurazione, i log, le metriche e la traccia distribuita.

Per seguire gli esempi di distribuzione di Azure Spring Cloud è sufficiente il percorso del codice sorgente, fornito in base alle esigenze.

## <a name="functional-services"></a>Servizi funzionali

L'app PiggyMetrics è scomposta in tre microservizi principali. Sono tutti applicazioni distribuibili in modo indipendente organizzate per dominio aziendale.

* **Servizio account (da distribuire)** : contiene la logica di input utente generale e la relativa convalida: entrate/spese, risparmi e impostazioni account.
* **Servizio statistiche (non usato in questa guida di avvio rapido)** : esegue calcoli sui principali parametri delle statistiche e acquisisce le serie temporali per ogni account. Il punto dati contiene valori, normalizzati sulla valuta di base e il periodo di tempo. Questi dati vengono usati per monitorare le dinamiche del flusso di cassa per la durata dell'account.
* **Servizio di notifica (non usato in questa guida di avvio rapido)** : archivia le informazioni di contatto e le impostazioni di notifica degli utenti, ad esempio la frequenza di promemoria e backup. Il ruolo di lavoro pianificato raccoglie le informazioni necessarie da altri servizi e invia messaggi di posta elettronica ai clienti che hanno effettuato la sottoscrizione.

## <a name="infrastructure-services"></a>Servizi di infrastruttura

I sistemi distribuiti presentano diversi modelli comuni che consentono il funzionamento dei servizi principali. Azure Spring Cloud offre strumenti avanzati che migliorano il comportamento delle applicazioni Spring Boot per l'implementazione di questi modelli: 

* **Servizio di configurazione (ospitato da Azure Spring Cloud)** : Azure Spring Cloud Config è un servizio di configurazione centralizzato a scalabilità orizzontale per i sistemi distribuiti. Usa un repository collegabile che attualmente supporta l'archiviazione locale, Git e Subversion.
* **Individuazione dei servizi (ospitato da Azure Spring Cloud)** : consente il rilevamento automatico dei percorsi di rete per le istanze del servizio, che potrebbero avere indirizzi assegnati dinamicamente a causa della scalabilità automatica, degli errori e degli aggiornamenti.
* **Servizio di autenticazione (da distribuire)** : le responsabilità di autorizzazione sono completamente estratte in un server distinto, che concede i token OAuth2 per i servizi di risorse back-end. Il server di autenticazione esegue l'autorizzazione degli utenti e consente la comunicazione sicura da computer a computer all'interno di un perimetro.
* **Gateway API (da distribuire)** : i tre servizi principali espongono un'API esterna al client. Nei sistemi reali, il numero di funzioni può crescere molto rapidamente man mano che aumenta la complessità del sistema. Nel rendering di una pagina Web complessa potrebbero essere coinvolti centinaia di servizi. Il gateway API è un singolo punto di ingresso nel sistema, usato per gestire le richieste e indirizzarle al servizio back-end appropriato o per richiamare più servizi back-end, aggregando i risultati. 

## <a name="sample-usage-of-piggymetrics"></a>Esempio di utilizzo di PiggyMetrics

Per informazioni dettagliate sull'implementazione, vedere [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). Gli esempi fanno riferimento al codice sorgente necessario.
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Effettuare il provisioning di un'istanza di Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
