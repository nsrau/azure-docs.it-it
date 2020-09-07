---
title: Avvio rapido - Introduzione all'app di esempio Piggy Metrics - Azure Spring Cloud
description: Descrive l'app di esempio Piggy Metrics usata nella distribuzione in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046783"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Introduzione all'app di esempio Piggy Metrics

In questa guida di avvio rapido viene usata un'app di esempio per la gestione del bilancio personale, denominata Piggy Metrics, per illustrare come distribuire un'app nel servizio Azure Spring Cloud. Piggy Metrics illustra il modello di architettura di microservizi mettendo in evidenza la suddivisione dei servizi. Si vedrà come viene distribuita in Azure con potenti funzionalità di Azure Spring Cloud, come l'individuazione dei servizi, il server di configurazione, i log, le metriche e la traccia distribuita.

Per seguire gli esempi di distribuzione di Azure Spring Cloud è sufficiente il percorso del codice sorgente, fornito in base alle esigenze.

## <a name="functional-services"></a>Servizi funzionali
L'app Piggy Metrics è scomposta in tre microservizi principali. Sono tutti applicazioni distribuibili in modo indipendente organizzate per dominio aziendale.

* **Servizio account (da distribuire)** : contiene la logica di input utente generale e la relativa convalida: entrate/spese, risparmi e impostazioni account.
* **Servizio statistiche (non usato in questa guida di avvio rapido)** : esegue calcoli sui principali parametri delle statistiche e acquisisce le serie temporali per ogni account. Il punto dati contiene valori, normalizzati sulla valuta di base e il periodo di tempo. Questi dati vengono usati per monitorare le dinamiche del flusso di cassa per la durata dell'account.
* **Servizio di notifica (non usato in questa guida di avvio rapido)** : archivia le informazioni di contatto e le impostazioni di notifica degli utenti, ad esempio la frequenza di promemoria e backup. Il ruolo di lavoro pianificato raccoglie le informazioni necessarie da altri servizi e invia messaggi di posta elettronica ai clienti che hanno effettuato la sottoscrizione.

## <a name="infrastructure-services"></a>Servizi di infrastruttura
I sistemi distribuiti presentano diversi modelli comuni che consentono il funzionamento dei servizi principali. Azure Spring Cloud offre strumenti avanzati che migliorano il comportamento delle applicazioni Spring Boot per l'implementazione di questi modelli: 

* **Servizio di configurazione (ospitato da Azure Spring Cloud)** : Azure Spring Cloud Config è un servizio di configurazione centralizzato a scalabilità orizzontale per i sistemi distribuiti. Usa un repository collegabile che attualmente supporta l'archiviazione locale, Git e Subversion.
* **Individuazione dei servizi (ospitato da Azure Spring Cloud)** : consente il rilevamento automatico dei percorsi di rete per le istanze del servizio, che potrebbero avere indirizzi assegnati dinamicamente a causa della scalabilità automatica, degli errori e degli aggiornamenti.
* **Servizio di autenticazione (da distribuire)** : le responsabilità di autorizzazione sono completamente estratte in un server distinto, che concede i token OAuth2 per i servizi di risorse back-end. Il server di autenticazione esegue l'autorizzazione degli utenti e consente la comunicazione sicura da computer a computer all'interno di un perimetro.
* **Gateway API (da distribuire)** : i tre servizi principali espongono un'API esterna al client. Nei sistemi reali, il numero di funzioni può crescere molto rapidamente man mano che aumenta la complessità del sistema. Nel rendering di una pagina Web complessa potrebbero essere coinvolti centinaia di servizi. Il gateway API è un singolo punto di ingresso nel sistema, usato per gestire le richieste e indirizzarle al servizio back-end appropriato o per richiamare più servizi back-end, aggregando i risultati. 

## <a name="sample-usage-of-piggy-metrics"></a>Esempio di utilizzo di Piggy Metrics
Per informazioni dettagliate sull'implementazione, vedere [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). Gli esempi fanno riferimento al codice sorgente necessario.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Effettuare il provisioning di un'istanza di Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
