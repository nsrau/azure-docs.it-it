---
title: Metriche di distribuzione & HPA con monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive le metriche relative alla distribuzione & la metrica di scalabilità automatica di Pod orizzontali con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570530"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Metriche di distribuzione & HPA con monitoraggio di Azure per i contenitori

A partire dalla versione dell'agente *ciprod08072020*, monitoraggio di Azure per i contenitori-Integrated Agent ora raccoglie le metriche per le distribuzioni & attributi.

## <a name="deployment-metrics"></a>Metriche di distribuzione

Monitoraggio di Azure per i contenitori avvia automaticamente le distribuzioni di monitoraggio, raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviarle nella tabella **InsightMetrics** :

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Numero totale di Pod pronti assegnati a questa distribuzione (status. readyReplicas). Di seguito sono riportate le dimensioni di questa metrica. <ul> <li> distribuzione: nome della distribuzione </li> <li> k8sNamespace-spazio dei nomi Kubernetes per la distribuzione </li> <li> deploymentStrategy-strategia di distribuzione da usare per sostituire i pod con quelli nuovi (spec. Strategy. Type)</li><li> creationTime-timestamp di creazione della distribuzione </li> <li> spec_replicas: numero di Pod desiderati (spec. repliche) </li> <li>status_replicas_available: numero totale di pod disponibili (pronti per almeno minReadySeconds) di destinazione di questa distribuzione (status. availableReplicas)</li><li>status_replicas_updated: numero totale di Pod non terminati assegnati da questa distribuzione con la specifica del modello desiderata (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>Metriche HPA

Monitoraggio di Azure per i contenitori avvia automaticamente il monitoraggio di attributi, raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviando questi ultimi nella tabella **InsightMetrics** :

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, TargetName | Numero corrente di repliche di Pod gestiti da questo ridimensionatore automatico (status. currentReplicas). Di seguito sono riportate le dimensioni di questa metrica. <ul> <li> hPa: nome del HPA </li> <li> k8sNamespace-spazio dei nomi Kubernetes per HPA </li> <li> lastScaleTime-ultima volta in cui l'HPA ha ridimensionato il numero di Pod (status. lastScaleTime)</li><li> timestamp di creazione creationTime-HPA </li> <li> spec_max_replicas-limite massimo per il numero di pod che possono essere impostati dal ridimensionamento automatico (spec. maxReplicas) </li> <li> spec_min_replicas-limite inferiore per il numero di repliche a cui è possibile ridurre il ridimensionamento automatico (spec. minReplicas) </li><li>status_desired_replicas il numero di repliche di Pod gestito da questo ridimensionatore automatico (status. desiredReplicas)</li><li>targetKind: tipo di destinazione di HPA (spec. scaleTargetRef. Kind) </li><li>TargetName: nome della destinazione di HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Grafici di distribuzione & HPA 

Il monitoraggio di Azure per i contenitori include grafici preconfigurati per le metriche elencate in precedenza nella tabella come cartella di lavoro per ogni cluster. È possibile trovare le distribuzioni & le distribuzioni di cartelle di lavoro HPA **& hPa** direttamente da un cluster AKS selezionando **cartelle di lavoro** nel riquadro a sinistra e nell'elenco a discesa **Visualizza cartelle di lavoro** in informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le [metriche di stato Kube in Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) per altre informazioni sulle metriche di stato Kube.