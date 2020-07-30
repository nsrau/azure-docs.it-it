---
title: Usare il firewall di Azure per proteggere le distribuzioni di Azure Kubernetes Service (AKS)
description: Informazioni su come usare il firewall di Azure per proteggere le distribuzioni di Azure Kubernetes Service (AKS)
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/29/2020
ms.author: victorh
ms.openlocfilehash: 602671f1052de2d9446f32946271cea2f9995044
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412950"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Usare il firewall di Azure per proteggere le distribuzioni di Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) offre un cluster Kubernetes gestito in Azure. Riduce la complessità e il sovraccarico operativo della gestione di Kubernetes grazie all'offload di gran parte di tale responsabilità in Azure. AKS gestisce le attività critiche, ad esempio il monitoraggio dell'integrità e la manutenzione per l'utente e fornisce un cluster di livello aziendale e sicuro con governance semplificata.

Kubernetes orchestra i cluster di macchine virtuali e pianifica i contenitori da eseguire su tali macchine virtuali in base alle risorse di calcolo disponibili e ai requisiti di risorse di ogni contenitore. I contenitori sono raggruppati in pod, unità operativa di base per Kubernetes e tali Pod si adattano allo stato desiderato.

A scopi di gestione e operativi, i nodi in un cluster del servizio Azure Kubernetes devono poter accedere a porte e nomi di dominio completi (FQDN) specifici. Queste azioni possono essere la comunicazione con il server API oppure il download e l'installazione dei componenti principali del cluster Kubernetes e degli aggiornamenti della sicurezza dei nodi. Il firewall di Azure consente di bloccare l'ambiente e filtrare il traffico in uscita.

Seguire le linee guida in questo articolo per fornire protezione aggiuntiva per il cluster Azure Kubernetes usando il firewall di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un cluster Kubernetes di Azure distribuito con l'applicazione in esecuzione.

   Per altre informazioni, vedere [esercitazione: distribuire un cluster Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) ed [esercitazione: eseguire applicazioni in Azure KUBERNETES Service (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Protezione di AKS

Il firewall di Azure fornisce un tag FQDN AKS per semplificare la configurazione. Per consentire il traffico della piattaforma AKS in uscita, attenersi alla procedura seguente:

- Quando si usa il firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per indirizzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. 

   L'uso del firewall di Azure con un UDR interrompe la configurazione in ingresso a causa del routing asimmetrico. Il problema si verifica se la subnet AKS ha una route predefinita che passa all'indirizzo IP privato del firewall, ma si sta usando un servizio di bilanciamento del carico pubblico. Ad esempio, il servizio in ingresso o Kubernetes di tipo *LoadBalancer*.

   In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto di ritorno perché il firewall non è a conoscenza del fatto che è stata stabilita una sessione. Per informazioni su come integrare Firewall di Azure con il servizio di bilanciamento del carico in ingresso o del servizio, vedere [Integrare Firewall di Azure con Azure Load Balancer Standard](integrate-lb.md).
- Creare una raccolta di regole dell'applicazione e aggiungere una regola per abilitare il tag FQDN *AzureKubernetesService* . L'intervallo di indirizzi IP di origine è la rete virtuale del pool host, il protocollo è HTTPS e la destinazione è AzureKubernetesService.
- Le porte in uscita/regole di rete seguenti sono obbligatorie per un cluster del servizio Azure Kubernetes:

   - Porta TCP 443
   - TCP [*IPAddrOfYourAPIServer*]: 443 è necessario se si dispone di un'app che deve comunicare con il server API. Questa modifica può essere impostata dopo la creazione del cluster.
   - Porta TCP 9000 e porta UDP 1194 per il Pod anteriore del tunnel per comunicare con l'estremità del tunnel nel server API.

      Per essere più specifici, vedere **. HPC. <location> azmk8s.io* e gli indirizzi nella tabella seguente.
   - Porta UDP 123 per la sincronizzazione dell'ora del protocollo NTP (Network Time Protocol) (nodi Linux).
   - Porta UDP 53 per DNS, necessaria se sono presenti pod che accedono direttamente al server API.

   Per altre informazioni, vedere [controllare il traffico in uscita per i nodi del cluster in Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).
- Configurare i tag del servizio di archiviazione e AzureMonitor. Monitoraggio di Azure riceve i dati di log Analytics.

   È anche possibile consentire l'URL dell'area di lavoro singolarmente `<worksapceguid>.ods.opinsights.azure.com` , ovvero, e `<worksapceguid>.oms.opinsights.azure.com` . È possibile risolvere il problema in uno dei modi seguenti:

    - Consentire l'accesso HTTPS dalla subnet del pool host a `*. ods.opinsights.azure.com` , e `*.oms. opinsights.azure.com` . Questi FQDN con caratteri jolly abilitano l'accesso richiesto, ma sono meno restrittivi.
    - Usare la query di log Analytics seguente per elencare gli FQDN esatti necessari e quindi consentirli in modo esplicito nelle regole dell'applicazione firewall:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul servizio Azure Kubernetes, vedere [concetti di base di Kubernetes per Azure Kubernetes Service (AKS)](../aks/concepts-clusters-workloads.md).
