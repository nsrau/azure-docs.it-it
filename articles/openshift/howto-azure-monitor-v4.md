---
title: Integrazione di Azure Monitor per Azure Red Hat OpenShift 4.3
description: Informazioni su come abilitare Monitoraggio di Azure nel cluster Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082847"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integrazione di Azure Monitor per Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Si noti che Azure Red Hat OpenShift 4.3 è attualmente disponibile solo in anteprima privata negli Stati Uniti orientali. L'accettazione dell'anteprima privata è solo su invito. Assicurarsi di registrare la sottoscrizione prima di tentare di abilitare questa funzionalità: [Registrazione di Azure Red Hat OpenShift Private Preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Le funzionalità di anteprima sono self-service e vengono fornite come sono e come disponibili e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Pertanto, le funzionalità non sono destinate all'utilizzo in produzione.

Questo articolo descrive come abilitare l'anteprima privata di Monitoraggio di Azure per i contenitori per i cluster OpenShift 4.3 ospitati in base al prem o in qualsiasi ambiente cloud. Le stesse istruzioni si applicano anche per abilitare il monitoraggio per i cluster Azure Red Hat OpenShift (ARO) 4.3.  

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Elmo 3](https://helm.sh/docs/intro/install/)
- Accesso a kubeconfig del cluster kubernetes
- Accesso a una sottoscrizione di Azure
- Accesso al cluster OpenShift 4.3 per installare il grafico Monitoraggio di Azure per contenitori
- Autorizzazione del ruolo RBAC Collaboratore minimo nella sottoscrizione di AzureMinimum Contributor RBAC role permission on the Azure Subscription  
- L'agente di monitoraggio richiede le porte e i domini in uscita seguenti per inviare i dati di monitoraggio al back-end di Monitoraggio di Azure (se bloccato da proxy/firewall):
  - N.ods.opinsights.azure.com 443
  - N.oms.opinsights.azure.com 443
  - N.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Lo script utilizza bash 4 funzioni, quindi assicuratevi che il bash è aggiornato. È possibile controllare la `bash --version`versione corrente con .

### <a name="download-the-onboarding-script"></a>Scarica lo script di onboarding

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Eseguire lo script seguente con azureSubscriptionId, workspace Region, clusterName e context del cluster Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Ad esempio:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati dell'agenteConfigure agent data collection

Per impostazione predefinita, l'agente di monitoraggio raccoglie i log del contenitore  di tutti i contenitori in esecuzione in tutti gli spazi dei nomi, ad eccezione del kube-system.  Se si desidera configurare la raccolta di log del contenitore specifica di uno spazio dei nomi o di uno spazio dei nomi specifico, è possibile fare riferimento alla [configurazione dell'agente di Container Insights](../azure-monitor/insights/container-insights-agent-config.md). Qui è possibile configurare l'agente di monitoraggio con le impostazioni di raccolta dati desiderate utilizzando la mappa di configurazione.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurare la raschiatura delle metriche di Prometheus

Monitoraggio di Azure per i contenitori raschia le metriche prometheus e l'inserimento nel back-end di Monitoraggio di Azure.Azure Monitor for containers scrapes the Prometheus metrics and ingest to the Azure Monitor backend. Fare riferimento alla [configurazione di Container Insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) per istruzioni su come configurare il raschiamento Prometheus.

Dopo aver completato correttamente l'onboarding, passare a [Monitoraggio ibrido](https://aka.ms/azmon-containers-hybrid) e selezionare Ambiente come **"Tutti"** per visualizzare il cluster OpenShift v4 appena integrato.

## <a name="disable-monitoring"></a>Disabilitare il monitoraggio

Se si vuole disabilitare il monitoraggio, è possibile eliminare il grafico Elmo di Monitoraggio di Azure per i contenitori usando il comando seguente per interrompere la raccolta e l'inserimento dei dati di monitoraggio nel back-end di Monitoraggio di Azure per i contenitori.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitoraggio degli aggiornamenti

Eseguire nuovamente lo script di onboarding come descritto nella sezione [Onboarding](#onboarding) con lo stesso parametro per l'aggiornamento al grafico Helm più recente.

## <a name="after-successful-onboarding"></a>Dopo l'onboarding riuscito

Passare a [Monitoraggio ibrido](https://aka.ms/azmon-containers-hybrid)ed è possibile visualizzare il cluster OpenShift/ARO v4 appena abilitato con lo stato di integrità nella scheda **Cluster monitorati.** È possibile ottenere informazioni più approfondite, ad esempio metriche, inventario e log, facendo clic sulla colonna **Cluster.**

## <a name="supported-features"></a>Caratteristiche supportate

Per altre informazioni sulle funzionalità supportate, vedere Panoramica di [Informazioni sul contenitore](../azure-monitor/insights/container-insights-overview.md).

Contattaci askcoin@microsoft.com tramite per feedback e domande.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio, vedere:To learn more about monitoring, see:
- [Panoramica di Panoramica di Informazioni dettagliate sul contenitor](../azure-monitor/insights/container-insights-overview.md)

- [Panoramica della query di log](../azure-monitor/log-query/log-query-overview.md)
