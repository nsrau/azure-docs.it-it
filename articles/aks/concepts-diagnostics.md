---
title: Panoramica di diagnostica del servizio Azure Kubernetes (AKS)
description: Informazioni sui cluster di diagnostica automatica nel servizio Azure Kubernetes.
services: container-service
author: yunjchoi
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 69ae6766414dbe533500860b01852ee8d25c3a1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513913"
---
# <a name="azure-kubernetes-service-aks-diagnostics-overview"></a>Panoramica di diagnostica del servizio Azure Kubernetes (AKS)

La risoluzione dei problemi del cluster di Azure Kubernetes Service (AKS) è una parte importante della gestione del cluster, soprattutto se il cluster esegue carichi di lavoro cruciali. Il servizio di diagnostica AKS è un'esperienza intuitiva e autonoma che consente di identificare e risolvere i problemi nel cluster. La diagnostica AKS è nativa del cloud ed è possibile usarla senza costi aggiuntivi per la configurazione o la fatturazione.

## <a name="open-aks-diagnostics"></a>Apri diagnostica AKS

Per accedere alla diagnostica AKS:

- Passare al cluster Kubernetes nel [portale di Azure](https://portal.azure.com).
- Fare clic su **diagnostica e risoluzione dei problemi** nel percorso di spostamento a sinistra per aprire la diagnostica AKS.
- Scegliere una categoria che descriva meglio il problema del cluster usando le parole chiave nel riquadro della Home page oppure digitare una parola chiave che meglio descriva il problema nella barra di ricerca, ad esempio _problemi del nodo del cluster_.

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un report di diagnostica

Dopo aver fatto clic su una categoria, è possibile visualizzare un report di diagnostica specifico per il cluster. Il report di diagnostica chiama in modo intelligente se si verificano problemi nel cluster con icone di stato. È possibile eseguire il drill-down in ogni argomento facendo clic su **altre informazioni** per visualizzare una descrizione dettagliata del problema, le azioni consigliate, i collegamenti a docs utili, le metriche correlate e i dati di registrazione. I report di diagnostica vengono generati in modo intelligente in base allo stato corrente del cluster dopo l'esecuzione di diversi controlli. I report di diagnostica possono essere uno strumento utile per individuare il problema del cluster e individuare i passaggi successivi per risolvere il problema.

![Report di diagnostica](./media/concepts-diagnostics/diagnostic-report.png)

![Report di diagnostica espanso](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Informazioni dettagliate sul cluster

In **cluster Insights**sono disponibili i seguenti controlli di diagnostica.

### <a name="cluster-node-issues"></a>Problemi del nodo del cluster

Problemi del nodo del cluster controlla la presenza di problemi correlati al nodo che potrebbero causare un comportamento imprevisto del cluster.

- Problemi di conformità dei nodi
- Errori del nodo
- Risorse insufficienti
- Configurazione IP mancante nel nodo
- Errori CNI nodo
- Nodo non trovato
- Spegnimento del nodo
- Errore di autenticazione del nodo
- Nodo Kube-proxy non aggiornato

### <a name="create-read-update--delete-operations"></a>Operazioni di creazione, lettura, aggiornamento & eliminazione

Le operazioni CRUD controllano eventuali operazioni CRUD che possono causare problemi nel cluster.

- Errore dell'operazione di eliminazione della subnet in uso
- Errore dell'operazione di eliminazione del gruppo di sicurezza di rete
- Errore dell'operazione di eliminazione della tabella di route in uso
- Errore di provisioning delle risorse a cui si fa riferimento
- Errore dell'operazione di eliminazione dell'indirizzo IP pubblico
- Errore di distribuzione dovuto alla quota di distribuzione
- Errore dell'operazione a causa di criteri dell'organizzazione
- Registrazione della sottoscrizione mancante
- Errore di provisioning dell'estensione VM
- Capacità subnet
- Errore di superamento della quota

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

Gestione identità e sicurezza rileva gli errori di autenticazione e autorizzazione che possono impedire la comunicazione con il cluster.

- Errori di autorizzazione del nodo
- 401 errori
- 403 errori

## <a name="next-steps"></a>Passaggi successivi

Raccogliere i log per facilitare la risoluzione dei problemi del cluster usando il [periscopio AKS](https://aka.ms/aksperiscope).

Pubblicare domande o commenti e suggerimenti in [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) aggiungendo "[diag]" nel titolo.
