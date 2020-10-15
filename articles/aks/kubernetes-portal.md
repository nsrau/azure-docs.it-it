---
title: Accedere alle risorse di Kubernetes dalla portale di Azure (anteprima)
description: Informazioni su come interagire con le risorse di Kubernetes per gestire un cluster di Azure Kubernetes Service (AKS) dal portale di Azure.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 09/21/2020
ms.author: lahugh
ms.openlocfilehash: ce51e76829c19def1c1603b1a88592d1e683ccae
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070640"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Accedere alle risorse di Kubernetes dalla portale di Azure (anteprima)

Il portale di Azure include un visualizzatore di risorse Kubernetes (anteprima) per semplificare l'accesso alle risorse di Kubernetes nel cluster del servizio Kubernetes di Azure (AKS). La visualizzazione delle risorse di Kubernetes dalla portale di Azure riduce il cambio di contesto tra la portale di Azure e lo `kubectl` strumento da riga di comando, semplificando l'esperienza di visualizzazione e modifica delle risorse di Kubernetes. Il Visualizzatore risorse include attualmente più tipi di risorse, ad esempio distribuzioni, Pod e set di repliche.

La visualizzazione risorse Kubernetes dalla portale di Azure sostituisce il [componente aggiuntivo del dashboard AKS][kubernetes-dashboard], che è impostato per la deprecazione.

>[!NOTE]
>Il capabilty non è attualmente supportato nei [cluster di servizi Kubernetes di Azure privati](./private-clusters.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerequisiti

Per visualizzare le risorse di Kubernetes nel portale di Azure, è necessario un cluster AKS. Qualsiasi cluster è supportato, ma se si usa l'integrazione Azure Active Directory (Azure AD), il cluster deve usare l' [integrazione di Azure ad gestite da AKS][aks-managed-aad]. Se il cluster USA Azure AD legacy, è possibile aggiornare il cluster nel portale o con l'interfaccia della riga di comando di [Azure][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Visualizzare le risorse di Kubernetes

Per visualizzare le risorse di Kubernetes, passare al cluster AKS nel portale di Azure. Il riquadro di spostamento a sinistra viene usato per accedere alle risorse. Le risorse includono:

- **Spazi dei nomi** consente di visualizzare gli spazi dei nomi del cluster. Il filtro nella parte superiore dell'elenco degli spazi dei nomi consente di filtrare rapidamente e visualizzare le risorse dello spazio dei nomi.
- **Carichi di lavoro** Mostra informazioni sulle distribuzioni, i pod, i set di repliche e i set di daemon distribuiti nel cluster. La schermata seguente mostra i pod di sistema predefiniti in un cluster AKS di esempio.
- I **Servizi e** le risorse in ingresso mostrano tutte le risorse del servizio del cluster e di ingresso.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Informazioni Pod Kubernetes visualizzate nel portale di Azure." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Distribuire un'applicazione

In questo esempio verrà usato il cluster AKS di esempio per distribuire l'applicazione Azure vote dalla [Guida introduttiva di AKS][portal-quickstart].

1. Selezionare **Aggiungi** da una qualsiasi delle visualizzazioni di risorse (spazio dei nomi, carichi di lavoro o servizi e in ingresso).
1. Incollare il YAML per l'applicazione Azure vote dalla [Guida introduttiva di AKS][portal-quickstart].
1. Per distribuire l'applicazione, selezionare **Aggiungi** nella parte inferiore dell'editor YAML. 

Una volta aggiunto il file YAML, il Visualizzatore risorse Mostra sia i servizi Kubernetes che sono stati creati: il servizio interno (Azure-vote-back) e il servizio esterno (Azure-vote-front) per accedere all'applicazione Azure vote. Il servizio esterno include un indirizzo IP esterno collegato per poter visualizzare facilmente l'applicazione nel browser.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informazioni Pod Kubernetes visualizzate nel portale di Azure." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Monitorare le informazioni dettagliate sulla distribuzione

I cluster AKS con [monitoraggio di Azure per i contenitori][enable-monitor] abilitati possono visualizzare rapidamente informazioni dettagliate sulla distribuzione. Dalla visualizzazione risorse di Kubernetes, gli utenti possono visualizzare lo stato attivo delle singole distribuzioni, tra cui l'utilizzo della CPU e della memoria, nonché la transizione a monitoraggio di Azure per ottenere informazioni più dettagliate. Di seguito è riportato un esempio di informazioni dettagliate sulla distribuzione da un cluster AKS di esempio:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Informazioni Pod Kubernetes visualizzate nel portale di Azure." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Modifica YAML

La visualizzazione risorse Kubernetes include anche un editor YAML. Un editor YAML incorporato significa che è possibile aggiornare o creare servizi e distribuzioni dall'interno del portale e applicare immediatamente le modifiche.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Informazioni Pod Kubernetes visualizzate nel portale di Azure.":::

Dopo aver modificato YAML, le modifiche vengono applicate selezionando **Verifica e Salva**, confermando le modifiche e quindi salvando di nuovo.

>[!WARNING]
> L'esecuzione di modifiche dirette alla produzione tramite interfaccia utente o CLI non è consigliata. è consigliabile usare le [procedure consigliate per l'integrazione continua (ci) e la distribuzione continua (CD)](kubernetes-action.md). Le funzionalità di gestione di Kubernetes del portale di Azure e l'editor YAML sono compilate per l'apprendimento e il volo di nuove distribuzioni in un'impostazione di sviluppo e test.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione illustra i problemi comuni e i passaggi per la risoluzione dei problemi.

### <a name="unauthorized-access"></a>Accesso non autorizzato

Per accedere alle risorse di Kubernetes, è necessario avere accesso al cluster AKS, all'API Kubernetes e agli oggetti Kubernetes. Assicurarsi di essere un amministratore del cluster o un utente con le autorizzazioni appropriate per accedere al cluster AKS. Per altre informazioni sulla sicurezza del cluster, vedere [Opzioni di accesso e identità per AKS][concepts-identity].

>[!NOTE]
> La visualizzazione risorse kubernetes nel portale di Azure è supportata solo da [cluster abilitati per AAD gestiti](managed-aad.md) o da cluster non abilitati per AAD. Se si usa un cluster abilitato per la funzionalità AAD gestito, l'utente o l'identità di AAD deve avere le rispettive associazioni Role/Role per accedere all'API kubernetes, oltre all'autorizzazione per eseguire il pull dell' [utente `kubeconfig` ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Abilita visualizzazione risorse

Per i cluster esistenti, potrebbe essere necessario abilitare la visualizzazione delle risorse Kubernetes. Per abilitare la visualizzazione risorse, seguire le istruzioni nel portale per il cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Informazioni Pod Kubernetes visualizzate nel portale di Azure." lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> È possibile aggiungere la funzionalità AKS per gli [**intervalli IP autorizzati del server API**](api-server-authorized-ip-ranges.md) per limitare l'accesso al server API solo all'endpoint pubblico del firewall. Un'altra opzione per questi cluster è `--api-server-authorized-ip-ranges` l'aggiornamento per includere l'accesso per un computer client locale o un intervallo di indirizzi IP (da cui viene visualizzato il portale). Per consentire l'accesso, è necessario l'indirizzo IPv4 pubblico del computer. È possibile trovare questo indirizzo con il comando seguente o eseguendo una ricerca di "Qual è l'indirizzo IP" in un browser Internet.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come accedere alle risorse di Kubernetes per il cluster AKS. Vedere [distribuzioni e manifesti YAML][deployments] per una conoscenza più approfondita delle risorse cluster e dei file YAML a cui si accede con il Visualizzatore di risorse Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md