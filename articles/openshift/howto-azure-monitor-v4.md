---
title: Integrazione di monitoraggio di Azure per Azure Red Hat OpenShift 4,3
description: Informazioni su come abilitare monitoraggio di Azure nel cluster Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082847"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integrazione di monitoraggio di Azure per Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Si noti che Azure Red Hat OpenShift 4,3 è attualmente disponibile solo in anteprima privata negli Stati Uniti orientali. L'accettazione dell'anteprima privata è riservata a un invito. Assicurarsi di registrare la sottoscrizione prima di provare ad abilitare questa funzionalità: registrazione dell' [Anteprima privata di Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Le funzionalità di anteprima sono self-service e vengono fornite così come sono e sono disponibili e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Pertanto, le funzionalità non sono destinate all'uso in produzione.

Questo articolo descrive come abilitare l'anteprima privata di monitoraggio di Azure per i contenitori per i cluster OpenShift 4,3 ospitati in locale o in qualsiasi ambiente cloud. Le stesse istruzioni si applicano anche per abilitare il monitoraggio per i cluster Azure Red Hat OpenShift (ARO) 4,3.  

## <a name="prerequisites"></a>Prerequisiti

- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Accesso a kubeconfig del cluster kubernetes
- Accesso a una sottoscrizione di Azure
- Accesso al cluster OpenShift 4,3 per installare il grafico Helm di monitoraggio di Azure per contenitori
- Autorizzazione ruolo RBAC minimo collaboratore per la sottoscrizione di Azure  
- L'agente di monitoraggio richiede i seguenti domini e porte in uscita per inviare i dati di monitoraggio al back-end di monitoraggio di Azure (se bloccato da proxy/firewall):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Lo script usa le funzionalità di bash 4, quindi assicurarsi che bash sia aggiornato. È possibile controllare la versione corrente con `bash --version`.

### <a name="download-the-onboarding-script"></a>Scaricare lo script di onboarding

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Eseguire lo script seguente con azureSubscriptionId, l'area dell'area di lavoro, il clustername e il contesto del cluster Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Ad esempio:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati agente

Per impostazione predefinita, l'agente di monitoraggio raccoglie i log del contenitore {stdout; stderr} di tutti i contenitori in esecuzione in tutti gli spazi dei nomi ad eccezione di Kube-System.  Per configurare la raccolta di log del contenitore specifica per determinati spazi dei nomi o spazi dei nomi, è possibile fare riferimento alla [configurazione dell'agente di container Insights](../azure-monitor/insights/container-insights-agent-config.md). Qui è possibile configurare l'agente di monitoraggio con le impostazioni di raccolta dati desiderate usando la mappa di configurazione.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurare la frammentazione delle metriche Prometheus

Il monitoraggio di Azure per i contenitori consente di rimuovere le metriche Prometeo e di inserire il back-end di monitoraggio di Azure. Per istruzioni su come configurare la raschiatura di Prometeo, vedere la pagina relativa alla [configurazione di Prometheus Insights](../azure-monitor/insights/container-insights-prometheus-integration.md) .

Al termine dell'onboarding, passare a [monitoraggio ibrido](https://aka.ms/azmon-containers-hybrid) e selezionare ambiente come **"tutti"** per visualizzare il cluster OpenShift V4 appena caricato.

## <a name="disable-monitoring"></a>Disabilitare il monitoraggio

Se si vuole disabilitare il monitoraggio, è possibile eliminare il grafico Helm di monitoraggio di Azure per i contenitori usando il comando seguente per arrestare la raccolta e l'inserimento dei dati di monitoraggio in monitoraggio di Azure per i contenitori back-end.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitoraggio degli aggiornamenti

Eseguire nuovamente lo script di onboarding come descritto nella sezione [onboarding](#onboarding) con lo stesso parametro per eseguire l'aggiornamento al grafico Helm più recente.

## <a name="after-successful-onboarding"></a>Dopo il completamento dell'onboarding

Passare a [monitoraggio ibrido](https://aka.ms/azmon-containers-hybrid)ed è possibile visualizzare il cluster OPENSHIFT/Aro V4 appena abilitato con lo stato di integrità nella scheda **cluster monitorati** . Qui è possibile ottenere informazioni più dettagliate, ad esempio metriche, inventario e log, facendo clic sulla colonna **cluster** .

## <a name="supported-features"></a>Caratteristiche supportate

Per altre informazioni sulle funzionalità e sulle funzionalità supportate, vedere [Panoramica di container Insights](../azure-monitor/insights/container-insights-overview.md).

Per commenti e askcoin@microsoft.com domande, contattaci via.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul monitoraggio, vedere:
- [Panoramica di container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Panoramica delle query di log](../azure-monitor/log-query/log-query-overview.md)
