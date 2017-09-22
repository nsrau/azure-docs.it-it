---
title: Distribuire il nozzle di Azure Log Analytics per il monitoraggio di Cloud Foundry | Microsoft Docs
description: "Informazioni dettagliate sulla distribuzione del nozzle loggregator di Cloud Foundry per Azure Log Analytics, la configurazione della console OMS e di Azure Log Analytics e l'utilizzo di questi strumenti per monitorare le metriche delle prestazioni e l'integrità del sistema Foundry Cloud."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuire il nozzle di Azure Log Analytics per il monitoraggio del sistema Cloud Foundry

## <a name="background"></a>Background

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) è un servizio nella Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Consente di raccogliere e analizzare i dati generati dagli ambienti cloud e locali.

Il nozzle di Microsoft Azure Log Analytics è un componente di Foundry Cloud, che inoltra le metriche dal firehose [loggregator di Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) ad Azure Log Analytics. Con il nozzle è possibile raccogliere, visualizzare e analizzare l'integrità del sistema e le metriche delle prestazioni di Cloud Foundry tra più distribuzioni.

In questo documento si apprenderà come distribuire il nozzle nell'ambiente Cloud Foundry e come accedere ai dati dalla console OMS di Azure Log Analytics.

## <a name="prerequisites"></a>Prerequisiti

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Distribuire un ambiente Cloud Foundry (CF) o Pivotal Cloud Foundry (PCF) in Azure

È possibile usare il nozzle con una distribuzione open source di Cloud Foundry (CF) o una distribuzione di Pivotal Cloud Foundry (PCF).

* [Distribuire Cloud Foundry in Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuire Pivotal Cloud Foundry in Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installare gli strumenti da riga di comando di Cloud Foundry per la distribuzione del nozzle

Il nozzle viene eseguito come applicazione nell'ambiente Cloud Foundry ed è quindi necessaria l'interfaccia della riga di comando di Cloud Foundry per distribuire l'applicazione. È necessaria anche una autorizzazione di accesso al firehose loggregator e al controller del cloud; per creare e configurare l'utente è necessario un client da riga di comando UAA.

* [Installare l'interfaccia della riga di comando di Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installare il client da riga di comando UAA Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Verificare che sia installato Rubygems prima di configurare il client da riga di comando UAA.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Creare un'area di lavoro OMS in Azure

#### <a name="create-the-oms-workspace-manually"></a>Creare manualmente l'area lavoro OMS

È possibile creare manualmente l'area di lavoro OMS e caricare le visualizzazioni e gli avvisi OMS preconfigurati al termine della distribuzione del nozzle.

1. Nel portale di Azure cercare l'elenco di servizi nel Marketplace per Log Analytics, quindi selezionare Log Analytics.
2. Fare clic su Crea e quindi selezionare le opzioni per gli elementi seguenti:

* Area di lavoro OMS: immettere un nome per l'area di lavoro.
* Sottoscrizione: se sono disponibili più sottoscrizioni, scegliere quella con la distribuzione di Cloud Foundry.
* Gruppo di risorse: è possibile creare un nuovo gruppo di risorse o usare quello con la distribuzione di Cloud Foundry.
* Percorso
* Piano tariffario: fare clic su OK per completare.
> Per altre informazioni, vedere [Introduzione a Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>Creare l'area di lavoro OMS tramite il modello OMS

È possibile creare l'area di lavoro OMS, caricare le visualizzazioni e gli avvisi OMS preconfigurati, il tutto tramite la [soluzione Azure Log Analytics di OMS per Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)

## <a name="deploy-the-nozzle"></a>Distribuire il nozzle

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuire il nozzle come parte di Ops manager di Pivotal Cloud Foundry

Se è già stato distribuito Pivotal Cloud Foundry tramite Ops Manager, seguire questi passaggi per [installare e configurare il nozzle per Pivotal Cloud Foundry (PCF)](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html); il nozzle viene installato come parte di Ops Manager.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Distribuire il nozzle come applicazione in Cloud Foundry

Se non si usa Ops Manager di Pivotal Cloud Foundry, è necessario effettuare il push del nozzle come applicazione.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Accedere alla propria distribuzione di Cloud Foundry come amministratore tramite l'interfaccia della riga di comando di Cloud Foundry

Nella casella di sviluppo eseguire il comando seguente:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN" è il nome di dominio di Cloud Foundry. È possibile recuperarlo tramite la ricerca di "SYSTEM_DOMAIN" nel file manifesto della distribuzione di Cloud Foundry. 
> "CF_User" è il nome di amministratore di Cloud Foundry. È possibile recuperare il nome e la password eseguendo una ricerca nella sezione "scim", cercando nome e "cf_admin_password" nel file manifesto della distribuzione di Cloud Foundry.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Creare un utente Cloud Foundry e concedere i privilegi necessari

Nella casella di sviluppo eseguire i comandi seguenti:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN" è il nome di dominio di Cloud Foundry. È possibile recuperarlo tramite la ricerca di "SYSTEM_DOMAIN" nel file manifesto della distribuzione di Cloud Foundry.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>Scaricare la versione più recente del nozzle di Azure Log Analytics

Nella casella di sviluppo eseguire il comando seguente:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>Impostare le variabili di ambiente in "manifest.yml" nella directory corrente

Si tratta del manifesto dell'app per il nozzle, quindi è necessario sostituire il valore con le informazioni specifiche dell'area di lavoro OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Eseguire il push dell'applicazione dalla casella di sviluppo

Verificare che sia aperta la cartella "oms-log-analytics-firehose-nozzle" ed eseguire:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Convalidare l'installazione del nozzle

### <a name="from-apps-manager-for-pcf"></a>Da Apps Manager (per Pivotal Cloud Foundry)

1. Accedere a Ops Manager, verificare che il riquadro sia visualizzato nel dashboard di installazione.
2. Accedere ad Apps Manager, verificare che lo spazio creato per il nozzle sia elencato nel report di utilizzo e che lo stato sia normale.

### <a name="from-dev-box"></a>Dalla casella di sviluppo

Nella finestra dell'interfaccia della riga di comando di Cloud Foundry della casella di sviluppo digitare:
```
cf apps
```
Verificare che l'applicazione nozzle di OMS sia in esecuzione.

## <a name="view-the-data-in-oms-portal"></a>Visualizzare i dati nel portale OMS

### <a name="1-import-oms-view"></a>1. Visualizzazione dell'importazione OMS

Dal portale di OMS passare a **Visualizza finestra di progettazione** -> **Importa** -> **Sfoglia**, selezionare uno dei file omsview, ad esempio, *Cloud Foundry.omsview* e salvare la visualizzazione. A questo punto viene visualizzato un **riquadro** nella pagina di panoramica di OMS. Fare clic su **riquadro**; vengono visualizzate le metriche.

Gli operatori possono personalizzare le visualizzazioni o creare nuove visualizzazioni tramite **Visualizza finestra di progettazione**.

*"Cloud Foundry.omsview"* è una versione di anteprima del modello di visualizzazione OMS di Cloud Foundry; un modello predefinito completamente configurato è in corso di elaborazione. Inviare eventuali commenti e suggerimenti alla [sezione dedicata ai problemi](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Creazione di regole di avviso

Gli operatori possono [creare avvisi](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), personalizzare le query e i valori di soglia in base alle esigenze. Di seguito è riportato un set di avvisi consigliati.

| Query di ricerca                                                                  | Genera l'avviso in base a | Descrizione                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Numero di risultati < 1   | **bbs.Domain.cf-apps** indica se il dominio cf-apps è aggiornato, vale a dire che le richieste di delle app CF dal controller cloud vengono sincronizzate con bbs.LRPsDesired (Diego-desired AIs) per l'esecuzione. Nessun dato ricevuto significa che il dominio cf-apps non è aggiornato nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Numero di risultati > 0   | Per le celle Diego, 0 indica uno stato di integrità e 1 uno stato di non integrità. Impostare l'avviso se più **celle Diego non integre** vengono rilevate nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Numero di risultati > 0 | 1 indica che il sistema è integro e 0 che il sistema non è integro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Numero di risultati > 0   | Consul genera periodicamente lo stato di integrità. 0 indica che il sistema è integro, mentre 1 indica che l'emettitore di route rileva che **Consul è inattivo**. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Numero di risultati > 0 | Il numero delta di messaggi **eliminato** intenzionalmente da Doppler a causa di congestione. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Numero di risultati > 0   | Loggregator genera **LGR** per indicare problemi con il processo di registrazione, ad esempio quando l'output dei messaggi del log è troppo alto. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Numero di risultati > 0   | Quando il nozzle riceve l'avviso di consumer lento da loggregator, invia la ValueMetric **slowConsumerAlert** a OMS. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Numero di risultati > 0   | Se il numero delta di **eventi persi** raggiunge una soglia, è possibile che si stia verificando un problema nel nozzle. |

## <a name="scale"></a>Scalabilità

### <a name="scale-the-nozzle"></a>Ridimensionare il nozzle

Si consiglia agli operatori di iniziare con almeno due istanze del nozzle. Il firehose distribuisce il carico di lavoro tra tutte le istanze del nozzle.
Per assicurarsi che il nozzle rimanga aggiornato con il traffico di dati proveniente dal firehose, l'operatore deve configurare l'avviso **slowConsumerAlert** elencato nella sezione relativa alla creazione di regole di avviso; dopo l'impostazione dell'avviso seguire le [indicazioni sul nozzle lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) per determinare se è necessario eseguire un ridimensionamento.
Per aumentare le istanze del nozzle, usare Apps Manager o l'interfaccia della riga di comando di Cloud Foundry per incrementare i numeri di istanze o le risorse di memoria o del disco per il nozzle.

### <a name="scale-the-loggregator"></a>Ridimensionare il loggregator

Il loggregator invia il messaggio di log **LGR** per indicare problemi con il processo di registrazione. L'operatore può monitorare l'avviso per determinare se è necessario ridimensionare il loggregator.
Per ridimensionare il loggregator, l'operatore può aumentare la dimensione del buffer Doppler o aggiungere altre istanze del server Doppler nel manifesto di Cloud Foundry; per informazioni dettagliate, controllare le [indicazioni generali sul ridimensionamento di loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aggiornare

Per aggiornare il nozzle con una versione più recente, scaricare la nuova versione, seguire i passaggi nella sezione relativa alla distribuzione ed eseguire di nuovo il push dell'applicazione.

Per rimuovere il nozzle, eseguire la procedura seguente:

### <a name="from-the-ops-manager"></a>Da Ops Manager

1. Accedere a Ops Manager
2. Individuare il riquadro relativo al nozzle di Microsoft Azure Log Analytics per PCF
3. Fare clic sull'icona a forma di cestino e confermare l'azione di eliminazione.

### <a name="from-the-dev-box"></a>Dalla casella di sviluppo

Nella finestra dell'interfaccia della riga di comando di Cloud Foundry digitare:
```
cf delete <App Name> -r
```

Se si rimuove il nozzle, i dati nel portale di OMS non vengono rimossi automaticamente e la scadenza dipende dall'impostazione di conservazione di Log Analytics di OMS.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Il nozzle di Azure Log Analytics è open source; inviare eventuali domande e commenti e suggerimenti alla [sezione github](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Per aprire una richiesta di supporto tecnico Azure, usare la categoria di servizi di macchina virtuale con Cloud Foundry. 

## <a name="next-step"></a>Passaggio successivo

Oltre alle metriche Cloud Foundry analizzate nel nozzle, è possibile usare l'agente OMS per ottenere informazioni dettagliate sui dati operativi a livello di macchina virtuale (syslog, prestazioni, avvisi e inventario); l'agente viene installato come componente aggiuntivo Bosh nelle macchine virtuali di Cloud Foundry.
> Per informazioni dettagliate, vedere [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Distribuire l'agente distribuzione nella distribuzione Cloud Foundry).
