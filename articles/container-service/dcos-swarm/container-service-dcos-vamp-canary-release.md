---
title: Versione canary con Vamp nel cluster DC/OS di Azure | Microsoft Docs
description: Come usare Vamp per i servizi della versione canary e applicare filtri intelligenti al traffico in un cluster Azure DC/OS del servizio contenitore di Azure
services: container-service
author: gggina
manager: rasquill
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 4a20091b59f2643ea71cce99c159a5075706e35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Microservizi della versione canary con Vamp in un cluster DC/OS del servizio contenitore di Azure

In questa procedura dettagliata viene configurato Vamp nel servizio contenitore di Azure con un cluster DC/OS. Viene rilasciata la versione canary del servizio demo "sava" di Vamp che risolvere un problema di incompatibilità del servizio con Firefox applicando filtri intelligenti al traffico. 

> [!TIP] 
> In questa procedura dettagliata Vamp viene eseguito in un cluster DC/OS, ma è anche possibile usarlo con Kubernetes come agente di orchestrazione.
>

## <a name="about-canary-releases-and-vamp"></a>Informazioni sulle versioni canary e su Vamp


La [versione canary](https://martinfowler.com/bliki/CanaryRelease.html) è una strategia di distribuzione intelligente adottata da organizzazioni innovative come Netflix, Facebook e Spotify. È un buon approccio che consente di ridurre i problemi, introdurre reti di sicurezza e aumentare l'innovazione. Perché quindi non lo usato tutte le società? L'estensione di una pipeline CI/CD per includere le strategie canary aggiunge complessità e richiede un'ampia esperienza e una vasta conoscenza di sviluppo. Questa è una motivazione sufficiente affinché società e organizzazioni di piccole dimensioni non riescano ad avviare il processo. 

[Vamp](http://vamp.io/) è un sistema open source progettato per facilitare la transizione e portare le funzioni della versione canary all'utilità di pianificazione del contenitore preferita. La funzionalità canary di Vamp va oltre le implementazioni basate su percentuale. È possibile filtrare il traffico e dividerlo in base a una vasta gamma di condizioni, ad esempio in base a destinatari specifici, intervalli di indirizzi IP o dispositivi. Vamp tiene traccia e analizza le metriche delle prestazioni, consentendo l'automazione in base a dati reali. È possibile configurare il ripristino automatico dello stato precedente in caso di errori o aumentare le prestazioni delle varianti dei singoli servizi in base al carico o alla latenza.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurare il servizio contenitore di Azure con DC/OS



1. [Distribuire un cluster DC/OS](container-service-deployment.md) con un master e due agenti di dimensioni predefinite. 

2. [Creare un tunnel SSH](../container-service-connect.md) per connettersi al cluster DC/OS. Questo articolo presuppone l'esecuzione del tunneling per il cluster sulla porta locale 80.


## <a name="set-up-vamp"></a>Configurare Vamp

Ora che si dispone di un cluster DC/OS in esecuzione, è possibile installare Vamp dall'interfaccia utente DC/OS (http://localhost:80). 

![Interfaccia utente di DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

L'installazione viene eseguita in due fasi:

1. **Distribuire Elasticsearch**.

2. Quindi **distribuire Vamp** installando il pacchetto Universo DC/OS di Vamp.

### <a name="deploy-elasticsearch"></a>Distribuire Elasticsearch

Per la raccolta delle metriche e l'aggregazione Vamp richiede Elasticsearch. Per distribuire uno stack Vamp Elasticsearch compatibile è possibile usare le [immagini magneticio](https://hub.docker.com/r/magneticio/elastic/).

1. Nell'interfaccia utente DC/OS passare a **Servizi** e fare clic su **Distribuisci servizio**.

2. Selezionare **JSON mode** (Modalità JSON) dal popup **Deploy New Service** (Distribuisci il nuovo servizio).

  ![Selezione della modalità JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Incollare il JSON seguente. Questa configurazione esegue il contenitore con 1 GB di RAM e un controllo di integrità di base sulla porta Elasticsearch.
  
  ```JSON
  {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
  }
  ```
  

3. Fare clic su **Distribuisci**.

  DC/OS consente di distribuire il contenitore Elasticsearch. Nella pagina **Servizi** è possibile monitorare l'avanzamento.  

  ![distribuzione di Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Distribuire Vamp

Quando Elasticsearch segnala lo stato **In esecuzione**, è possibile aggiungere il pacchetto Universo DC/OS di Vamp. 

1. Passare a **Universe** (Universo) e cercare **vamp**. 
  ![Vamp in Universe (Universo) DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Fare clic su **Installa** accanto ai pacchetto vamp e scegliere **Advanced Installation** (Installazione avanzata).

3. Scorrere verso il basso e immettere il seguente URL di Elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

  ![Immettere l'URL di Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Fare clic su **Verifica e installa**, quindi fare clic su **Installa** per avviare la distribuzione.  

  DC/OS distribuisce tutti i componenti necessari di Vamp. Nella pagina **Servizi** è possibile monitorare l'avanzamento.
  
  ![Distribuire Vamp come pacchetto universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Dopo aver completato la distribuzione, è possibile accedere all'interfaccia utente di Vamp:

  ![Servizio Vamp su DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Interfaccia utente di Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Distribuire il primo servizio

Ora che Vamp è in esecuzione, è possibile distribuire un servizio da un progetto. 

Nella forma più semplice, un [progetto Vamp](http://vamp.io/documentation/using-vamp/blueprints/) descrive gli endpoint, ovvero i gateway, i cluster e i servizi da distribuire. Vamp usa i cluster per raggruppare diverse varianti dello stesso servizio in gruppi logici per la versione canary o i test A/B.  

Questo scenario usa un'applicazione monolitica di esempio denominata [**sava**](https://github.com/magneticio/sava), che è alla versione 1.0. Il monolite viene compresso in un contenitore Docker, disponibile nell'hub Docker in magneticio/sava:1.0.0. L'app generalmente viene eseguita sulla porta 8080, ma in questo caso si desidera esporla nella porta 9050. Distribuire l'app tramite Vamp usando un progetto semplice.

1. Andare in **Deployments** (Distribuzioni).

2. Fare clic su **Aggiungi**.

3. Incollare il progetto YAML seguente. Questo progetto contiene un cluster con solo una variante di servizio, che verrà modificata in un passaggio successivo:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Fare clic su **Salva**. Vamp avvia la distribuzione.

La distribuzione viene elencata nella pagina **Distribuzioni**. Fare clic sulla distribuzione per monitorarne lo stato.

![Interfaccia utente Vamp: distribuzione sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![servizio sava nell'interfaccia utente di Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Sono stati creati due gateway, elencati nella pagina **Gateways** (Gateway):

* un endpoint stabile per accedere al servizio in esecuzione, sulla porta 9050 
* un gateway interno gestito da Vamp, altre informazioni su questo gateway verranno date in un secondo momento. 

![Interfaccia utente di Vamp: gateway sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Il servizio sava è stato distribuito, ma non è possibile accedervi dall'esterno perché Azure Load Balancer non può ancora inoltrarvi il traffico. Per accedere al servizio, aggiornare la configurazione di rete di Azure.


## <a name="update-the-azure-network-configuration"></a>Aggiornare la configurazione di Azure

Vamp ha distribuito il servizio sava nei nodi dell'agente DC/OS, esponendo un endpoint stabile sulla porta 9050. Per accedere al servizio dall'esterno del cluster DC/OS, apportare le modifiche seguenti alla configurazione di rete di Azure della distribuzione del cluster: 

1. **Configurare Azure Load Balancer** per gli agenti, la risorsa denominata **dcos-agent-lb-xxxx**, con un probe di integrità e una regola per inoltrare il traffico sulla porta 9050 alle istanze sava. 

2. **Aggiornare il gruppo di sicurezza di rete** per gli agenti pubblici, la risorsa denominata **XXXX-agent-public-nsg-XXXX**, per consentire il traffico sulla porta 9050.

Per istruzioni dettagliate su come completare queste attività tramite il portale di Azure, vedere [Abilitare l'accesso pubblico a un'applicazione del servizio contenitore di Azure](container-service-enable-public-access.md). Specificare la porta 9050 per tutte le impostazioni della porta.


Dopo aver creato tutti gli elementi, andare nel pannello **Panoramica** del servizio di bilanciamento del carico dell'agente DC/OS, ovvero la risorsa denominata **dcos-agent-lb-xxxx**. Trovare l'**indirizzo IP pubblico** e usarlo per far accedere sava alla porta 9050.

![Portale di Azure: ottenere l'indirizzo IP pubblico](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Eseguire una versione canary

Si supponga di avere una nuova versione dell'applicazione per cui si desidera rilasciare una versione canary nella produzione. È stata inserita in un contenitore come magneticio/sava:1.1.0 ed è pronta all'uso. Vamp consente di aggiungere facilmente nuovi servizi alla distribuzione in esecuzione. Questi servizi "uniti" vengono distribuiti insieme ai servizi esistenti nel cluster e viene assegnato loro un peso pari a 0%. Il traffico non viene indirizzato a un nuovo servizio unito fino a quando la distribuzione del traffico non viene regolata. Il dispositivo di scorrimento del peso nell'interfaccia utente di Vamp offre il controllo completo sulla distribuzione, consentendo di effettuare modifiche incrementali, ad esempio la versione canary, o un ripristino immediato dello stato precedente.

### <a name="merge-a-new-service-variant"></a>Unire una nuova variante di servizio

Per unire il nuovo servizio sava 1.1 con la distribuzione in esecuzione:

1. Nell'interfaccia utente di Vamp fare clic su **Blueprints** (Progetti).

2. Fare clic su **Add** (Aggiungi) e incollare il seguente progetto YAML: questo progetto descrive una nuova variante di servizio, sava: 1.1.0, da distribuire nel cluster esistente, sava_cluster.

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Fare clic su **Salva**. Il progetto viene archiviato ed elencato nella pagina **Blueprints** (Progetti).

4. Aprire il menu di azione nel progetto sava:1.1 e fare clic su **Merge to** (Unisci a).

  ![Interfaccia utente di Vamp: progetti](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selezionare la distribuzione **sava** e fare clic su **Merge** (Unisci).

  ![Interfaccia utente di Vamp: progetto di unione alla distribuzione](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp consente di distribuire la nuova variante di servizio sava:1.1.0 descritta nel progetto insieme a sava:1.0.0 in **sava_cluster** della distribuzione in esecuzione. 

![Interfaccia utente di Vamp: distribuzione sava aggiornato](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Anche il gateway **sava/sava_cluster/webport**, ovvero l'endpoint del cluster, viene aggiornato aggiungendo una route per sava:1.1.0 appena distribuito. A questo punto, il traffico non viene indirizzato qui, infatti **WEIGHT** (PESO) è impostato su 0%.

![Interfaccia utente di Vamp: gateway del cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versione canary

Dopo aver distribuito entrambe le versioni di sava nello stesso cluster, modificare la distribuzione del traffico tra di essi spostando il dispositivo di scorrimento **WEIGHT** (PESO).

1. Fare clic su ![Interfaccia utente di Vamp: modifica](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) accanto a **WEIGHT** (PESO).

2. Impostare la distribuzione del peso su 50%/50% e fare clic su **Save** (Salva).

  ![Interfaccia utente di Vamp: dispositivo di scorrimento del peso del gateway](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Tornare al browser e aggiornare la pagina di sava altre volte. Ora l'applicazione sava passa da una pagina sava:1.0 a una pagina sava:1.1.

  ![alternanza dei servizi sava1.0 e sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Questa alternanza della pagina funziona meglio con la modalità "Incognito" (In incognito) o "Anonymous" (Anonimo) del browser a causa della memorizzazione nella cache delle risorse statiche.
  >

### <a name="filter-traffic"></a>Filtrare il traffico

Si supponga che in seguito alla distribuzione sia stato individuato un problema di incompatibilità in sava:1.1.0 che causa problemi di visualizzazione nel browser Firefox. È possibile impostare Vamp affinché filtri il traffico in ingresso e indirizzi di nuovo tutti gli utenti di Firefox alla versione stabile di sava:1.0.0. Questo filtro consente di risolvere immediatamente le interruzioni per gli utenti di Firefox, mentre gli altri utenti continuano a sfruttare i vantaggi della versione migliorata di sava:1.1.0.

Vamp usa le **condizioni** per filtrare il traffico tra le route in un gateway. Il traffico viene innanzitutto filtrato e indirizzato in base alle condizioni applicate a ogni route. Il traffico restante viene distribuito in base all'impostazione del peso del gateway.

È possibile creare una condizione per filtrare tutti gli utenti di Firefox e indirizzarli alla versione precedente di sava:1.0.0:

1. Nella pagina **Gateways** (Gateway) sava/sava_cluster/webport fare clic su ![Interfaccia utente di Vamp: modifica](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) per aggiungere una **CONDIZIONE** alla route sava/sava_cluster/sava:1.0.0/webport. 

2. Immettere la condizione **user-agent == Firefox** e fare clic su ![Interfaccia utente di Vamp: salva](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp aggiunge la condizione con un livello pari a 0%. Per avviare il filtraggio del traffico, è necessario regolare la forza della condizione.

3. Fare clic su ![Interfaccia utente di Vamp: modifica](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) per modificare la **forza** applicata alla condizione.
 
4. Impostare **STRENGTH** (Forza) al 100% e fare clic su ![Interfaccia utente di Vamp: salva](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) per salvare.

  Vamp ora invia tutto il traffico che corrisponde alla condizione, ovvero tutti gli utenti di Firefox, a sava:1.0.0.

  ![Interfaccia utente di Vamp: condizione applicata al gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Infine, regolare il peso del gateway per l'invio di tutto il traffico rimanente, ovvero tutti gli utenti non Firefox, al nuovo sava:1.1.0. Fare clic su ![Interfaccia utente di Vamp: modifica](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) accanto a **WEIGHT** (PESO) e impostare la distribuzione del peso in modo che il 100% venga indirizzato alla route sava/sava_cluster/sava:1.1.0/webport.

  Tutto il traffico non filtrato dalla condizione ora viene indirizzato alla nuova versione di sava:1.1.0.

6. Per visualizzare il filtro in azione, aprire due diversi browser, Firefox e un altro browser, e accedere al servizio sava da entrambi. Tutte le richieste di Firefox vengono inviate a sava:1.0.0, mentre tutti gli altri browser vengono indirizzati a sava:1.1.0.

  ![Interfaccia utente di Vamp: filtrare il traffico](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Riepilogo

Questo articolo contiene una rapida introduzione a Vamp in un cluster DC/OS. Per i principianti, Vamp è stato messo in esecuzione nel cluster DC/OS del servizio contenitore di Azure, è stato distribuito un servizio con un progetto Vamp ed è stato effettuato l'accesso all'endpoint esposto, ovvero il gateway.

Sono state trattate anche alcune funzioni importanti di Vamp: l'unione di una nuova variante di servizio alla distribuzione in esecuzione e l'introduzione incrementale, quindi il filtraggio del traffico per risolvere un'incompatibilità nota.


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla gestione delle azioni Vamp attraverso le [API REST di Vamp](http://vamp.io/documentation/api/api-reference/).

* Creazione di script di automazione Vamp in Node.js ed esecuzione degli stessi come [flussi di lavoro di Vamp](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Vedere altre [esercitazioni su Vamp](http://vamp.io/documentation/tutorials/overview/).

