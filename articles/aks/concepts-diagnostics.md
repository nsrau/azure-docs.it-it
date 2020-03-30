---
title: Azure Kubernetes Service (AKS) Diagnostics Overview
description: Informazioni sui cluster auto-diagnosi nel servizio Azure Kubernetes.Learn about self-diagnosing clusters in Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126601"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Panoramica di Diagnostica servizio Di Azure Kubernetes (anteprima)Azure Kubernetes Service Diagnostics (preview) overview

Risoluzione dei problemi del cluster del servizio Kubernetes di Azure è una parte importante della gestione del cluster, soprattutto se il cluster esegue carichi di lavoro mission-critical. Diagnostica AKS è un'esperienza intelligente e autodiagnostica che consente di identificare e risolvere i problemi nel cluster. AKS Diagnostics è nativa cloud ed è possibile usarla senza costi di fatturazione o configurazione aggiuntivi.

Questa funzione è ora in anteprima pubblica.

## <a name="open-aks-diagnostics"></a>Aprire la diagnostica AKSOpen AKS Diagnostics

Per accedere a Diagnostica AKS:

- Passare al cluster Kubernetes nel portale di [Azure.](https://portal.azure.com)
- Fare clic su **Diagnostica e risolvi i problemi** nel riquadro di spostamento sinistro, che apre Diagnostica AKS.
- Scegliere una categoria che descriva meglio il problema del cluster utilizzando le parole chiave nel riquadro della home page oppure digitare una parola chiave che descriva meglio il problema nella barra di ricerca, ad esempio Problemi relativi ai nodi del _cluster._

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un rapporto di diagnostica

Dopo aver fatto clic su una categoria, è possibile visualizzare un report di diagnostica specifico per il cluster. Il rapporto di diagnostica indica in modo intelligente se si verifica un problema nel cluster con le icone di stato. È possibile eseguire il drill-down su ogni argomento facendo clic su **Ulteriori informazioni** per visualizzare la descrizione dettagliata del problema, le azioni consigliate, i collegamenti a documenti utili, le metriche correlate e la registrazione dei dati. I report di diagnostica vengono generati in modo intelligente in base allo stato corrente del cluster dopo l'esecuzione di una varietà di controlli. I report diagnostici possono essere uno strumento utile per individuare il problema del cluster e trovare i passaggi successivi per risolvere il problema.

![Rapporto di diagnostica](./media/concepts-diagnostics/diagnostic-report.png)

![Rapporto di diagnostica espanso](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Approfondimenti sui cluster

In **Cluster Insights**sono disponibili i controlli diagnostici seguenti.

### <a name="cluster-node-issues"></a>Problemi relativi ai nodi del cluster

Problemi nodo cluster controlla i problemi relativi ai nodi che possono causare il verificarsi imprevisto del cluster.

- Problemi di preparazione dei nodiNode readiness issues
- Errori dei nodi
- Risorse insufficienti
- Nodo mancante configurazione IP
- Errori CNI del nodoNode CNI failures
- Nodo non trovato
- Spegnimento del nodo
- Errore di autenticazione del nodoNode authentication failure
- Nodo kube-proxy non aggiornatoNode kube-proxy stale

### <a name="create-read-update--delete-operations"></a>Creare, leggere, aggiornare & eliminare le operazioni

Operations CRUD controlla le operazioni CRUD che possono causare problemi nel cluster.

- Errore di operazione di eliminazione della subnet in usoIn-use subnet delete operation error
- Errore dell'operazione di eliminazione del gruppo di sicurezza di reteNetwork security group delete operation error
- Errore di operazione di eliminazione tabella ciclo di lavorazione in uso
- Errore di provisioning delle risorse di riferimento
- Errore dell'operazione di eliminazione dell'indirizzo IP pubblico
- Errore di distribuzione dovuto alla quota di distribuzione
- Errore di funzionamento dovuto ai criteri dell'organizzazione
- Registrazione dell'abbonamento mancante
- Errore di provisioning dell'estensione VM
- Capacità della subnet
- Errore di quota superata

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

Identity and Security Management rileva gli errori di autenticazione e autorizzazione che potrebbero impedire la comunicazione con il cluster.

- Errori di autorizzazione dei nodiNode authorization failures
- 401 errori
- Errori 403

## <a name="next-steps"></a>Passaggi successivi

Raccogliere i registri per facilitare la risoluzione ulteriore dei problemi del cluster utilizzando [AKS Periscope](https://aka.ms/aksperiscope).

Pubblicare le domande o il feedback su [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) aggiungendo "[Diag]" nel titolo.
