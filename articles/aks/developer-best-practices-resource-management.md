---
title: Procedure consigliate per lo sviluppatore - Gestione delle risorse nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: b45c5abb66d927f247f62692f12bd9207958a3e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181283"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)

Per lo sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes (AKS) è necessario tenere in considerazione alcune aree chiave. Il modo in cui vengono gestite le distribuzioni delle applicazioni può influire negativamente sull'esperienza dell'utente finale relativamente all'uso dei servizi forniti. Per fare in modo che l'esperienza sia positiva, tenere presenti alcune procedure consigliate che è possibile seguire mentre si sviluppano ed eseguono applicazioni in servizio Azure Kubernetes.

Questo articolo sulle procedure consigliate è incentrato su come eseguire il cluster e i carichi di lavoro dal punto di vista dello sviluppatore di un'applicazione. Per informazioni sulle procedure consigliate di amministrazione, vedere [Procedure consigliate per l'operatore del cluster per l'isolamento e la gestione delle risorse nel servizio Azure Kubernetes (AKS)][operator-best-practices-isolation]. Contenuto dell'articolo:

> [!div class="checklist"]
> * Richieste di risorse e limiti del pod
> * Modalità di sviluppo e distribuzione delle applicazioni con Dev Spaces e Visual Studio Code
> * Come usare lo strumento `kube-advisor` per verificare la presenza di problemi con le distribuzioni

## <a name="define-pod-resource-requests-and-limits"></a>Definire le richieste di risorse e i limiti del pod

**Indicazioni sulle procedure consigliate**. Impostare le richieste e i limiti del pod per tutti i pod nei manifesti YAML. Se il cluster servizio Azure Kubernetes usa *quote di risorse*, la distribuzione può essere rifiutata se non si definiscono questi valori.

Uno dei modi principali per gestire le risorse di calcolo all'interno di un cluster servizio Azure Kubernetes consiste nell'usare le richieste e i limiti del pod. Le richieste e i limiti comunicano all'utilità di pianificazione di Kubernetes le risorse di calcolo da assegnare a un pod.

* Le **richieste del pod** definiscono una determinata quantità di CPU e memoria di cui il pod ha bisogno. Queste richieste devono corrispondere alla quantità di risorse di calcolo richieste dal pod per fornire un livello di prestazioni accettabile.
    * Quando l'utilità di pianificazione di Kubernetes tenta di inserire un pod su un nodo, le richieste del pod vengono usate per determinare quale nodo ha risorse sufficienti disponibili.
    * Monitorare le prestazioni dell'applicazione per regolare queste richieste in modo da essere certi di non definire meno risorse di quelle richieste per mantenere un livello di prestazioni accettabile.
* I **limiti del pod** corrispondono alla quantità massima di CPU e memoria utilizzabile da un pod. Questi limiti sono utili per impedire che alcuni pod con eccessivo tempo di esecuzione richiedano troppa CPU e memoria dal nodo. Questo scenario potrebbe ridurre le prestazioni del nodo e di altri pod in esecuzione su tale nodo.
    * Non impostare un limite del pod superiore a quello che i nodi sono in grado di supportare. Ogni nodo servizio Azure Kubernetes riserva una determinata quantità di CPU e memoria per i componenti principali di Kubernetes. L'applicazione potrebbe tentare di consumare troppe risorse del nodo impedendo la corretta esecuzione di altri pod.
    * Anche in questo caso, monitorare le prestazioni dell'applicazione in momenti diversi durante la giornata o la settimana. Determinare quando si verifica il picco della domanda e allineare i limiti del pod alle risorse richieste per soddisfare le esigenze dell'applicazione.

Nelle specifiche del pod la procedura consigliata prevede di definire le richieste e i limiti. Se non si includono questi valori, l'utilità di pianificazione di Kubernetes non comprende quali sono le risorse richieste. L'utilità di pianificazione può pianificare il pod su un nodo senza risorse sufficienti per fornire prestazioni accettabili per l'applicazione. L'amministratore del cluster può impostare *quote di risorse* in uno spazio dei nomi che richiede di impostare limiti e richieste di risorse. Per altre informazioni, vedere le informazioni sulle [quote di risorse nei cluster servizio Azure Kubernetes][resource-quotas].

Quando si definisce una richiesta o un limite per la CPU, il valore viene misurato in unità di CPU. *1.0* CPU corrisponde a un core CPU virtuale sottostante sul nodo. La stessa misura viene usata per le GPU. È anche possibile definire una richiesta o un limite frazionario, in genere in millicpu. Ad esempio, *100m* corrisponde a *0,1* di un core CPU virtuale sottostante.

Nell'esempio di base seguente per un singolo pod NGINX il pod richiede *100m* di tempo CPU e *128Mi* di memoria. I limiti delle risorse per il pod sono impostati su *250m* di CPU e *256Mi* di memoria:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Per altre informazioni sulle misure e le assegnazioni delle risorse, vedere [Managing compute resources for containers][k8s-resource-limits] (Gestione delle risorse di calcolo per i contenitori).

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Sviluppare ed eseguire il debug di applicazioni in un cluster servizio Azure Kubernetes

**Indicazioni sulle procedure consigliate**. I team di sviluppo devono eseguire la distribuzione e il debug in un cluster servizio Azure Kubernetes usando Dev Spaces. Questo modello di sviluppo garantisce che le esigenze a livello di archiviazione, rete o controlli degli accessi in base al ruolo vengano implementate prima della distribuzione dell'app nell'ambiente di produzione.

Con Azure Dev Spaces, le applicazioni vengono sviluppate e sottoposte a debug e test direttamente in un cluster servizio Azure Kubernetes. Gli sviluppatori all'interno di un team collaborano per compilare ed eseguire test durante il ciclo di vita dell'applicazione. È possibile continuare a usare gli strumenti esistenti come Visual Studio o Visual Studio Code. Viene installata un'estensione per Dev Spaces che offre un'opzione per eseguire l'applicazione e sottoporla a debug in un cluster servizio Azure Kubernetes:

![Eseguire il debug delle applicazioni in un cluster servizio Azure Kubernetes con Dev Spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Questo processo di sviluppo e test integrato con Dev Spaces riduce la necessità di ambienti di test locali, come [minikube][minikube]. In alternativa, è possibile sviluppare e testare un'applicazione in un cluster servizio Azure Kubernetes. Questo cluster può essere protetto e isolato come indicato nella sezione precedente sull'uso degli spazi dei nomi per isolare un cluster in modo logico. Quando le app sono pronte per la distribuzione nell'ambiente di produzione, è possibile distribuirle in tutta sicurezza perché l'intera fase di sviluppo è già stata eseguita in un cluster servizio Azure Kubernetes reale.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Usare l'estensione di Visual Studio Code per Kubernetes

**Indicazioni sulle procedure consigliate**. Installare e usare l'estensione di VS Code per Kubernetes quando si scrivono manifesti YAML. È anche possibile usare l'estensione per una soluzione di distribuzione integrata, particolarmente utile per i proprietari delle applicazioni che raramente interagiscono con il cluster servizio Azure Kubernetes.

L'[estensione di Visual Studio Code per Kubernetes][vscode-kubernetes] consente di sviluppare e distribuire applicazioni in servizio Azure Kubernetes. L'estensione fornisce IntelliSense per le risorse di Kubernetes e grafici e modelli Helm. È anche possibile esplorare, distribuire e modificare le risorse di Kubernetes da VS Code. L'estensione offre inoltre un controllo IntelliSense per le richieste di risorse o i limiti impostati nelle specifiche dei pod:

![Estensione di VS Code per Kubernetes con un avviso sull'assenza di limiti di memoria](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificare regolarmente la presenza di problemi dell'applicazione con kube-advisor

**Procedure consigliate** -regolarmente eseguono la versione più recente di `kube-advisor` strumento open source per rilevare i problemi nel cluster. Se si applicano le quote di risorse in un cluster servizio Azure Kubernetes esistente, eseguire per prima cosa `kube-advisor` per trovare i pod che non hanno richieste di risorse e limiti definiti.

Il [kube-advisor] [ kube-advisor] lo strumento è un progetto open source AKS associato che esegue l'analisi di un cluster Kubernetes e segnala i problemi rilevati. Un controllo utile consiste nell'identificare i pod che non hanno richieste di risorse e limiti applicati.

In un cluster servizio Azure Kubernetes che ospita molti team di sviluppo e applicazioni può essere difficile tenere traccia dei pod senza questi limiti e richieste di risorse impostati. Come procedura consigliata, eseguire regolarmente `kube-advisor` nei cluster servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sulle procedure consigliate ha illustrato come eseguire il cluster e i carichi di lavoro dal punto di vista dell'operatore di un cluster. Per informazioni sulle procedure consigliate di amministrazione, vedere [Procedure consigliate per l'operatore del cluster per l'isolamento e la gestione delle risorse nel servizio Azure Kubernetes (AKS)][operator-best-practices-isolation].

Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Sviluppare con Dev Spaces][dev-spaces]
* [Verificare la presenza di problemi con kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
