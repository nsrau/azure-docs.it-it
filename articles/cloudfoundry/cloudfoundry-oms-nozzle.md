---
title: Distribuire il nozzle di Log Analytics di Azure per il monitoraggio di Cloud Foundry | Microsoft Docs
description: "Istruzioni dettagliate per la distribuzione del nozzle loggregator di Cloud Foundry per Log Analytics di Azure. Usare il nozzle per monitorare le metriche di prestazioni e integrità del sistema di Cloud Foundry."
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
ms.openlocfilehash: 0d13d39d2921c51c537534a5b000564a9df91880
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuire il nozzle di Azure Log Analytics per il monitoraggio del sistema Cloud Foundry

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) è un servizio nella Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Consente di raccogliere e analizzare i dati generati dagli ambienti cloud e locali.

Il nozzle di Log Analytics di Azure (da qui in avanti il nozzle) è un componente di Cloud Foundry, che inoltra le metriche dal firehose [loggregator di Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) a Log Analytics di Azure. Il nozzle consente di raccogliere, visualizzare e analizzare le metriche delle prestazioni e dell'integrità del sistema Cloud Foundry tra più distribuzioni.

Questo documento illustra come distribuire il nozzle nell'ambiente Cloud Foundry e come accedere ai dati dalla console OMS di Log Analytics di Azure.

## <a name="prerequisites"></a>Prerequisiti

I passaggi seguenti sono prerequisiti per la distribuzione del nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Distribuire un ambiente Cloud Foundry o Pivotal Cloud Foundry in Azure

È possibile usare il nozzle con una distribuzione open source di Cloud Foundry (CF) o una distribuzione di Pivotal Cloud Foundry (PCF).

* [Distribuire Cloud Foundry in Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuire Pivotal Cloud Foundry in Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installare gli strumenti da riga di comando di Cloud Foundry per la distribuzione del nozzle

Il nozzle viene eseguito come un'applicazione nell'ambiente Cloud Foundry. Per distribuire l'applicazione è necessaria l'interfaccia della riga di comando di Cloud Foundry.

Il nozzle deve anche disporre delle autorizzazioni di accesso al firehose loggregator e al controller cloud. Per creare e configurare l'utente, è necessario il servizio UAA.

* [Installare l'interfaccia della riga di comando di Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installare il client della riga di comando UAA di Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Verificare che sia installato Rubygems prima di configurare il client della riga di comando UAA.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Creare un'area di lavoro OMS in Azure

È possibile creare l'area di lavoro OMS manualmente o tramite un modello. Caricare le visualizzazioni e gli avvisi OMS preconfigurati al termine della distribuzione del nozzle.

Per creare manualmente l'area lavoro OMS:

1. Nel portale di Azure cercare l'elenco di servizi in Marketplace e quindi selezionare Log Analytics.
2. Selezionare **Crea** e quindi scegliere le opzioni per gli elementi seguenti:

   * **Area di lavoro OMS:** immettere un nome per l'area di lavoro.
   * **Sottoscrizione:** se sono disponibili più sottoscrizioni, scegliere quella con la stessa distribuzione di Cloud Foundry.
   * **Gruppo di risorse:** è possibile creare un nuovo gruppo di risorse o usare quello con la stessa distribuzione di Cloud Foundry.
   * **Località**: immettere la località.
   * **Piano tariffario:** fare clic su **OK** per completare.

Per altre informazioni, vedere [Introduzione a Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

In alternativa, è possibile creare l'area di lavoro OMS tramite il modello OMS. Con questo metodo, il modello carica automaticamente le visualizzazioni e gli avvisi OMS preconfigurati. Per altre informazioni, vedere l'articolo sulla [soluzione Azure OMS Log Analitica per Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Distribuire il nozzle

È possibile distribuire il nozzle in diversi modi: come riquadro di Pivotal Cloud Foundry o come applicazione di Cloud Foundry.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuire il nozzle come parte di Ops manager di Pivotal Cloud Foundry

Se Pivotal Cloud Foundry è stato distribuito tramite Ops Manager, attenersi alla procedura indicata per [installare e configurare il nozzle per Pivotal Cloud Foundry](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Il nozzle viene installato come riquadro in Ops Manager.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>Distribuire il nozzle come un'applicazione Cloud Foundry

Se non si usa Ops Manager di Pivotal Cloud Foundry, distribuire il nozzle come applicazione. Le sezioni seguenti descrivono il processo.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Accedere alla propria distribuzione di Cloud Foundry come amministratore tramite l'interfaccia della riga di comando di Cloud Foundry.

Eseguire il comando seguente:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" è il nome di dominio di Cloud Foundry. È possibile recuperarlo tramite la ricerca di "SYSTEM_DOMAIN" nel file manifesto della distribuzione di Cloud Foundry. 

"CF_User" è il nome di amministratore di Cloud Foundry. È possibile recuperare il nome e la password eseguendo una ricerca nella sezione "scim", cercando nome e "cf_admin_password" nel file manifesto della distribuzione di Cloud Foundry.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Creare un utente Cloud Foundry e concedere i privilegi necessari

Eseguire i comandi seguenti:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" è il nome di dominio di Cloud Foundry. È possibile recuperarlo tramite la ricerca di "SYSTEM_DOMAIN" nel file manifesto della distribuzione di Cloud Foundry.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Scaricare la versione più recente del nozzle di Log Analytics di Azure

Eseguire il comando seguente:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Ora è possibile impostare le variabili di ambiente nel file manifest.yml nella directory corrente. Di seguito viene illustrato il manifesto dell'applicazione per il nozzle. Sostituire i valori con informazioni specifiche dell'area di lavoro OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to OMS Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to OMS Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to OMS Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Eseguire il push dell'applicazione dal computer di sviluppo.

Verificare che sia aperta la cartella oms-log-analytics-firehose-nozzle. Eseguire il comando seguente:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Convalidare l'installazione del nozzle

### <a name="from-apps-manager-for-pcf"></a>Da Apps Manager (per Pivotal Cloud Foundry)

1. Accedere a Ops Manager e verificare che il riquadro sia visualizzato nel dashboard di installazione.
2. Accedere ad Apps Manager e verificare che lo spazio creato per il nozzle sia elencato nel report di utilizzo; confermare che lo stato sia normale.

### <a name="from-your-development-computer"></a>Dal computer di sviluppo

Nella finestra dell'interfaccia della riga di comando di Cloud Foundry digitare:
```
cf apps
```
Verificare che l'applicazione nozzle di OMS sia in esecuzione.

## <a name="view-the-data-in-the-oms-portal"></a>Visualizzare i dati nel portale OMS

### <a name="1-import-the-oms-view"></a>1. Importare la visualizzazione OMS

Dal portale di OMS, passare a **Visualizza finestra di progettazione** > **Importa** > **Sfoglia**e selezionare uno dei file omsview. Selezionare ad esempio *Foundry.omsview Cloud*e salvare la vista. A questo punto viene visualizzato un riquadro **pagina di panoramica** di OMS. Selezionare per visualizzare le metriche visualizzate.

È possibile personalizzare le visualizzazioni o creare nuove visualizzazioni tramite **Visualizza finestra di progettazione**.

*"Cloud Foundry.omsview"* è una versione di anteprima del modello di visualizzazione OMS di Cloud Foundry. Si tratta di un modello predefinito completamente configurato. Inviare eventuali commenti e suggerimenti nella [sezione dedicata](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Creazione di regole di avviso

È possibile [creare avvisi](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) e personalizzare le query e i valori di soglia come necessario. Di seguito è riportato un set di avvisi consigliati.

| Query di ricerca                                                                  | Genera l'avviso in base a | Descrizione                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Numero di risultati < 1   | **bbs.Domain.cf-apps** segnala se il dominio cf-apps è aggiornato. Ciò significa che le richieste di app Cloud Foundry dal controller cloud vengono sincronizzate con bbs.LRPsDesired (Diego-desired AIs) per l'esecuzione. Nessun dato ricevuto significa che il dominio cf-apps non è stato aggiornato nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Numero di risultati > 0   | Per le celle Diego, 0 indica uno stato di integrità e 1 uno stato di non integrità. Impostare l'avviso se più celle Diego non integre vengono rilevate nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Numero di risultati > 0 | 1 indica che il sistema è integro e 0 che il sistema non è integro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Numero di risultati > 0   | Consul genera periodicamente lo stato di integrità. 0 indica che il sistema è integro, mentre 1 indica che l'emettitore di route rileva che Consul è inattivo. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Numero di risultati > 0 | Il numero delta di messaggi eliminato intenzionalmente da Doppler a causa di uno stato di congestione. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Numero di risultati > 0   | Il loggregator invia il messaggio di log **LGR** per indicare problemi con il processo di registrazione. Questo problema si verifica ad esempio quando l'output del messaggio di log ha dimensioni elevate. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Numero di risultati > 0   | Quando il nozzle riceve l'avviso di consumer lento da loggregator, invia la ValueMetric **slowConsumerAlert** a OMS. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Numero di risultati > 0   | Se il numero delta di eventi persi raggiunge una soglia, è possibile che si stia verificando un problema nel nozzle. |

## <a name="scale"></a>Scalabilità

È possibile ridimensionare il nozzle e il loggregator.

### <a name="scale-the-nozzle"></a>Ridimensionare il nozzle

È consigliabile iniziare con almeno due istanze del nozzle. Il firehose distribuisce il carico di lavoro tra tutte le istanze del nozzle.
Per assicurarsi che il nozzle possa rimanere aggiornato con il traffico di dati dal firehose, impostare l'avviso **slowConsumerAlert** (elencato nella sezione precedente, "Creazione di regole di avviso"). Dopo aver ricevuto l'avviso, seguire le indicazioni nel [materiale sussidiario per nozzle lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) per determinare se è necessario un ridimensionamento.
Per aumentare le istanze del nozzle, usare Apps Manager o l'interfaccia della riga di comando di Cloud Foundry per incrementare i numeri di istanze o le risorse di memoria o del disco per il nozzle.

### <a name="scale-the-loggregator"></a>Ridimensionare il loggregator

Il loggregator invia il messaggio di log **LGR** per indicare problemi con il processo di registrazione. È possibile monitorare l'avviso per determinare se è necessario ridimensionare il loggregator.
Per ridimensionare il loggregator, aumentare le dimensioni del buffer Doppler o aggiungere altre istanze del server Doppler nel manifesto di Cloud Foundry. Per altre informazioni vedere [il materiale sussidiario per il ridimensionamento di loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aggiornare

Per aggiornare il nozzle a una versione più recente, scaricare la nuova versione, seguire i passaggi nella sezione precedente "Distribuire il nozzle" ed eseguire di nuovo il push dell'applicazione.

### <a name="remove-the-nozzle-from-ops-manager"></a>Rimuovere il nozzle da Ops Manager

1. Accedere a Ops Manager.
2. Individuare il riquadro relativo al nozzle di **Log Analytics di Microsoft Azure per Pivotal Cloud Foundry**.
3. Fare clic sull'icona a forma di cestino e confermare l'eliminazione.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Rimuovere il nozzle dal computer di sviluppo

Nella finestra dell'interfaccia della riga di comando di Cloud Foundry digitare:
```
cf delete <App Name> -r
```

Se si rimuove il nozzle, i dati nel portale OMS non vengono rimossi automaticamente. I dati scadranno in base alle impostazioni di conservazione di Log Analytics di OMS.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Il nozzle di Log Analytics di Azure è open source. Inviare eventuali domande e commenti e suggerimenti nella [sezione GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Per aprire una richiesta di supporto tecnico per Azure, usare la categoria dei servizi "Macchina virtuale che esegue Cloud Foundry". 

## <a name="next-step"></a>Passaggio successivo

Oltre alle metriche Cloud Foundry analizzate nel nozzle, è possibile usare l'agente OMS per ottenere informazioni dettagliate sui dati operativi a livello di macchina virtuale (syslog, prestazioni, avvisi e inventario); l'agente OMS viene installato come componente aggiuntivo Bosh nelle macchine virtuali di Cloud Foundry.

Per informazioni dettagliate, vedere [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Distribuire l'agente OMS nella distribuzione Cloud Foundry).
