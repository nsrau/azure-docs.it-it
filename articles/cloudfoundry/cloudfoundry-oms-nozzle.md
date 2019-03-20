---
title: Distribuire il nozzle di Log Analytics di Azure per il monitoraggio di Cloud Foundry | Microsoft Docs
description: Istruzioni dettagliate per la distribuzione del nozzle loggregator di Cloud Foundry per Log Analytics di Azure. Usare il nozzle per monitorare le metriche di prestazioni e integrità del sistema di Cloud Foundry.
services: virtual-machines-linux
author: ningk
manager: jeconnoc
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 6220aebdef6970f3d5f7017e4ae48f6f409ae0ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111464"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuire il nozzle di Azure Log Analytics per il monitoraggio del sistema Cloud Foundry

[Monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) è un servizio di Azure. Consente di raccogliere e analizzare i dati generati dagli ambienti cloud e locali.

Il Nozzle di Log Analitica (il Nozzle) è un componente di Cloud Foundry (CF), che inoltra le metriche dal [loggregator di Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose ai log di monitoraggio di Azure. Il nozzle consente di raccogliere, visualizzare e analizzare le metriche delle prestazioni e dell'integrità del sistema Cloud Foundry tra più distribuzioni.

In questo documento descrive come distribuire il Nozzle nell'ambiente cloud Foundry e quindi accedere ai dati dalla console di log di monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

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

Prima di configurare il client della riga di comando UAA, assicurarsi che sia installato RubyGems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Creare un'area di lavoro di Log Analytics in Azure

È possibile creare l'area di lavoro di Log Analytics manualmente o tramite un modello. Il modello distribuirà un programma di installazione di visualizzazioni KPI preconfigurate e gli avvisi per la console di log di monitoraggio di Azure. 

#### <a name="to-create-the-workspace-manually"></a>Per creare manualmente l'area lavoro OMS:

1. Nel portale di Azure, eseguire ricerche nell'elenco dei servizi in Azure Marketplace e quindi selezionare le aree di lavoro di Log Analitica.
2. Selezionare **Crea** e quindi scegliere le opzioni per gli elementi seguenti:

   * **Area di lavoro di Log Analytics**: immettere un nome per l'area di lavoro.
   * **Sottoscrizione** se sono disponibili più sottoscrizioni, scegliere quella con la stessa distribuzione di Cloud Foundry.
   * **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usare quello con la stessa distribuzione di Cloud Foundry.
   * **Località**: immettere la località.
   * **Piano tariffario**: selezionare **OK** per completare.

Per altre informazioni, vedere [Introduzione a monitoraggio di Azure log](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Per creare l'area di lavoro di Log Analytics tramite il modello di monitoraggio da Azure Marketplace:

1. Aprire il portale di Azure.
1. Fare clic sul segno "+" o su "Crea una risorsa" in alto a sinistra.
1. Digitare "Cloud Foundry" nella finestra di ricerca e selezionare "Cloud Foundry Monitoring Solution" (Soluzione di monitoraggio Cloud Foundry).
1. Verrà caricata la prima pagina del modello della soluzione di monitoraggio Cloud Foundry. Fare clic su "Crea" per avviare il pannello del modello.
1. Immettere i parametri richiesti:
    * **Sottoscrizione** selezionare una sottoscrizione di Azure per l'area di lavoro di Log Analytics, in genere la stessa della distribuzione di Cloud Foundry.
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo per l'area di lavoro di Log Analytics.
    * **Località del gruppo di risorse**: selezionare la località del gruppo di risorse.
    * **OMS_Workspace_Name**: immettere un nome dell'area di lavoro. Se l'area di lavoro non esiste, il modello ne creerà una nuova.
    * **OMS_Workspace_Region**: selezionare la località per l'area di lavoro.
    * **OMS_Workspace_Pricing_Tier**: selezionare lo SKU per l'area di lavoro di Log Analytics. Vedere le [indicazioni sui prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) per riferimento.
    * **Note legali**: fare clic su Note legali e quindi fare clic su "Crea" per accettare i termini legali.
1. Dopo aver specificato tutti i parametri, fare clic su "Crea" per distribuire il modello. Quando viene completata la distribuzione, lo stato verrà visualizzato nella scheda delle notifiche.


## <a name="deploy-the-nozzle"></a>Distribuire il nozzle

È possibile distribuire il nozzle in diversi modi: come riquadro di Pivotal Cloud Foundry o come applicazione di Cloud Foundry.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuire il nozzle come parte di Ops manager di Pivotal Cloud Foundry

Seguire la procedura per [installare e configurare il nozzle di Azure Log Analytics per Pivotal Cloud Foundry](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Questo è l'approccio semplificato. Il riquadro Ops manager di Pivotal Cloud Foundry configurerà ed eseguirà il push del nozzle automaticamente. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Distribuire il nozzle manualmente come un'applicazione Cloud Foundry

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

Ora è possibile impostare le variabili di ambiente nel file manifest.yml nella directory corrente. Di seguito viene illustrato il manifesto dell'applicazione per il nozzle. Sostituire i valori con informazioni specifiche dell'area di lavoro di Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
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

## <a name="view-the-data-in-the-azure-portal"></a>Visualizzare i dati nel portale di Azure

Se la soluzione di monitoraggio è stata distribuita tramite il modello del marketplace, passare al portale di Azure e individuarla. È possibile trovare la soluzione nel gruppo di risorse specificato nel modello. Fare clic sulla soluzione, passare alla "log analitica console", sono elencate le viste preconfigurate, con principali indicatori KPI di sistema di Cloud Foundry, i dati dell'applicazione, gli avvisi e metriche sull'integrità della macchina virtuale. 

Se l'area di lavoro di Log Analytics è stata creata manualmente, seguire questa procedura per creare le viste e gli avvisi:

### <a name="1-import-the-oms-view"></a>1. Importare la visualizzazione OMS

Dal portale di OMS, passare a **Visualizza finestra di progettazione** > **Importa** > **Sfoglia**e selezionare uno dei file omsview. Selezionare ad esempio *Foundry.omsview Cloud*e salvare la vista. A questo punto viene visualizzato un riquadro nella pagina **Panoramica**. Selezionare per visualizzare le metriche visualizzate.

È possibile personalizzare le visualizzazioni o creare nuove visualizzazioni tramite **Visualizza finestra di progettazione**.

*"Cloud Foundry.omsview"* è una versione di anteprima del modello di visualizzazione OMS di Cloud Foundry. Si tratta di un modello predefinito completamente configurato. Inviare eventuali commenti e suggerimenti nella [sezione dedicata](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Creazione di regole di avviso

È possibile [creare avvisi](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) e personalizzare le query e i valori di soglia come necessario. Di seguito è riportato un set di avvisi consigliati.

| Query di ricerca                                                                  | Genera l'avviso in base a | DESCRIZIONE                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Numero di risultati < 1   | **bbs.Domain.cf-apps** segnala se il dominio cf-apps è aggiornato. Ciò significa che le richieste di app Cloud Foundry dal controller cloud vengono sincronizzate con bbs.LRPsDesired (Diego-desired AIs) per l'esecuzione. Nessun dato ricevuto significa che il dominio cf-apps non è stato aggiornato nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Numero di risultati > 0   | Per le celle Diego, 0 indica uno stato di integrità e 1 uno stato di non integrità. Impostare l'avviso se più celle Diego non integre vengono rilevate nell'intervallo di tempo specificato. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Numero di risultati > 0 | 1 indica che il sistema è integro e 0 che il sistema non è integro. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Numero di risultati > 0   | Consul genera periodicamente lo stato di integrità. 0 indica che il sistema è integro, mentre 1 indica che l'emettitore di route rileva che Consul è inattivo. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Numero di risultati > 0 | Il numero delta di messaggi eliminato intenzionalmente da Doppler a causa di uno stato di congestione. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Numero di risultati > 0   | Il loggregator invia il messaggio di log **LGR** per indicare problemi con il processo di registrazione. Questo problema si verifica ad esempio quando l'output del messaggio di log ha dimensioni elevate. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Numero di risultati > 0   | Quando il Nozzle riceve l'avviso di consumer lento da loggregator, invia le **slowConsumerAlert** registra ValueMetric a monitoraggio di Azure. |
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

## <a name="update"></a>Aggiornamento

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

Se si rimuove il nozzle, i dati nel portale OMS non vengono rimossi automaticamente. Scadranno in base la conservazione dei log di monitoraggio di Azure l'impostazione.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Il nozzle di Log Analytics di Azure è open source. Inviare eventuali domande e commenti e suggerimenti nella [sezione GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Per aprire una richiesta di supporto tecnico per Azure, usare la categoria dei servizi "Macchina virtuale che esegue Cloud Foundry". 

## <a name="next-step"></a>Passaggio successivo

Da PCF2.0, le metriche delle prestazioni della macchina virtuale sono trasferite al nozzle di Azure Log Analitica dal server d'inoltro le metriche di sistema e integrate nell'area di lavoro di Log Analitica. Non è più necessario l'agente di Log Analytics per le metriche delle prestazioni della macchina virtuale. Tuttavia è ancora possibile usare l'agente di Log Analytics per raccogliere informazioni di Syslog. L'agente di Log Analytics viene installato come componente aggiuntivo Bosh nelle macchine virtuali di Cloud Foundry. 

Per informazioni dettagliate, vedere [Distribuire l'agente di Log Analytics nella distribuzione Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
