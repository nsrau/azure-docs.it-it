---
title: Procedure consigliate per lo sviluppatore - Gestione delle risorse nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: bfce7d77f214762a69857e74f0bb533ad1ce0f1b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107647"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)

Per lo sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes (AKS) è necessario tenere in considerazione alcune aree chiave. Il modo in cui vengono gestite le distribuzioni delle applicazioni può influire negativamente sull'esperienza dell'utente finale relativamente all'uso dei servizi forniti. Per fare in modo che l'esperienza sia positiva, tenere presenti alcune procedure consigliate che è possibile seguire mentre si sviluppano ed eseguono applicazioni in servizio Azure Kubernetes.

Questo articolo sulle procedure consigliate è incentrato su come eseguire il cluster e i carichi di lavoro dal punto di vista dello sviluppatore di un'applicazione. Per informazioni sulle procedure consigliate amministrative, vedere procedure consigliate [per l'operatore cluster per l'isolamento e la gestione delle risorse in Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. Contenuto dell'articolo:

> [!div class="checklist"]
> * Richieste di risorse e limiti del pod
> * Modalità di sviluppo e distribuzione delle applicazioni con Dev Spaces e Visual Studio Code
> * Come usare lo strumento `kube-advisor` per verificare la presenza di problemi con le distribuzioni

## <a name="define-pod-resource-requests-and-limits"></a>Definire le richieste di risorse e i limiti del pod

**Indicazioni sulle procedure consigliate**. Impostare le richieste e i limiti del pod per tutti i pod nei manifesti YAML. Se il cluster servizio Azure Kubernetes usa *quote di risorse*, la distribuzione può essere rifiutata se non si definiscono questi valori.

Uno dei modi principali per gestire le risorse di calcolo all'interno di un cluster servizio Azure Kubernetes consiste nell'usare le richieste e i limiti del pod. Le richieste e i limiti comunicano all'utilità di pianificazione di Kubernetes le risorse di calcolo da assegnare a un pod.

* **Le richieste CPU/memoria Pod** definiscono una quantità di CPU e memoria che il Pod necessita a intervalli regolari.
    * Quando l'utilità di pianificazione Kubernetes tenta di inserire un pod in un nodo, le richieste Pod vengono usate per determinare quale nodo dispone di risorse sufficienti per la pianificazione.
    * Per impostazione predefinita, la richiesta Pod verrà impostata sul limite definito.
    * È molto importante monitorare le prestazioni dell'applicazione per modificare tali richieste. Se vengono effettuate richieste insufficienti, è possibile che l'applicazione riceva una riduzione delle prestazioni a causa della pianificazione di un nodo. Se le richieste vengono sovrastimate, è possibile che l'applicazione abbia una maggiore difficoltà a essere pianificata.
* I **limiti di CPU/memoria Pod** sono la quantità massima di CPU e memoria che può essere usata da un pod. Questi limiti consentono di definire quali Pod devono essere terminati in caso di instabilità del nodo a causa di risorse insufficienti. Senza i limiti appropriati, i pod impostati verranno terminati fino a quando non si solleva la pressione delle risorse.
    * I limiti Pod consentono di definire quando un pod perde il controllo dell'utilizzo delle risorse. Quando viene superato un limite, al Pod viene assegnata la priorità per la gestione dell'integrità dei nodi e per ridurre al minimo l'effetto sui pod che condividono il nodo.
    * Se non si imposta un limite Pod, il valore predefinito è il valore massimo disponibile in un determinato nodo.
    * Non impostare un limite del pod superiore a quello che i nodi sono in grado di supportare. Ogni nodo servizio Azure Kubernetes riserva una determinata quantità di CPU e memoria per i componenti principali di Kubernetes. L'applicazione potrebbe tentare di consumare troppe risorse del nodo impedendo la corretta esecuzione di altri pod.
    * Anche in questo caso, è molto importante monitorare le prestazioni dell'applicazione in momenti diversi durante il giorno o la settimana. Determinare quando la richiesta di picco è e allineare i limiti del Pod alle risorse necessarie per soddisfare le esigenze massime dell'applicazione.

Nelle specifiche del Pod **è consigliabile definire** queste richieste e limiti in base alle informazioni sopra riportate. Se non si includono questi valori, l'utilità di pianificazione Kubernetes non può prendere in considerazione le risorse richieste dalle applicazioni per facilitare la pianificazione delle decisioni.

Se l'utilità di pianificazione inserisce un pod in un nodo con risorse insufficienti, le prestazioni dell'applicazione saranno ridotte. È consigliabile che gli amministratori del cluster impostino le *quote delle risorse* in uno spazio dei nomi che richiede l'impostazione di richieste e limiti di risorse. Per altre informazioni, vedere [quote delle risorse nei cluster AKS][resource-quotas].

Quando si definisce una richiesta o un limite per la CPU, il valore viene misurato in unità di CPU. 
* *1.0* CPU corrisponde a un core CPU virtuale sottostante sul nodo. 
* La stessa misura viene usata per le GPU.
* È possibile definire frazioni misurate in millicore. Ad esempio, *100m* è *0,1* di un core vCPU sottostante.

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

Per altre informazioni sulle misurazioni delle risorse e sulle assegnazioni, vedere [gestione delle risorse di calcolo per i contenitori][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Sviluppare ed eseguire il debug di applicazioni in un cluster servizio Azure Kubernetes

**Indicazioni sulle procedure consigliate**. I team di sviluppo devono eseguire la distribuzione e il debug in un cluster servizio Azure Kubernetes usando Dev Spaces. Questo modello di sviluppo garantisce che le esigenze a livello di archiviazione, rete o controlli degli accessi in base al ruolo vengano implementate prima della distribuzione dell'app nell'ambiente di produzione.

Con Azure Dev Spaces, le applicazioni vengono sviluppate e sottoposte a debug e test direttamente in un cluster servizio Azure Kubernetes. Gli sviluppatori all'interno di un team collaborano per compilare ed eseguire test durante il ciclo di vita dell'applicazione. È possibile continuare a usare gli strumenti esistenti come Visual Studio o Visual Studio Code. Viene installata un'estensione per Dev Spaces che offre un'opzione per eseguire l'applicazione e sottoporla a debug in un cluster servizio Azure Kubernetes:

![Eseguire il debug delle applicazioni in un cluster servizio Azure Kubernetes con Dev Spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Questo processo di sviluppo e test integrato con spazi di sviluppo riduce la necessità di ambienti di test locali, ad esempio [minikube][minikube]. In alternativa, è possibile sviluppare e testare un'applicazione in un cluster servizio Azure Kubernetes. Questo cluster può essere protetto e isolato come indicato nella sezione precedente sull'uso degli spazi dei nomi per isolare un cluster in modo logico. Quando le app sono pronte per la distribuzione nell'ambiente di produzione, è possibile distribuirle in tutta sicurezza perché l'intera fase di sviluppo è già stata eseguita in un cluster servizio Azure Kubernetes reale.

Azure Dev Spaces è progettato per l'uso con applicazioni eseguite su Pod e nodi Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Usare l'estensione di Visual Studio Code per Kubernetes

**Indicazioni sulle procedure consigliate**. Installare e usare l'estensione di VS Code per Kubernetes quando si scrivono manifesti YAML. È anche possibile usare l'estensione per una soluzione di distribuzione integrata, particolarmente utile per i proprietari delle applicazioni che raramente interagiscono con il cluster servizio Azure Kubernetes.

L' [estensione Visual Studio Code per Kubernetes][vscode-kubernetes] consente di sviluppare e distribuire applicazioni in AKS. L'estensione fornisce IntelliSense per le risorse di Kubernetes e grafici e modelli Helm. È anche possibile esplorare, distribuire e modificare le risorse di Kubernetes da VS Code. L'estensione offre inoltre un controllo IntelliSense per le richieste di risorse o i limiti impostati nelle specifiche dei pod:

![Estensione di VS Code per Kubernetes con un avviso sull'assenza di limiti di memoria](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificare regolarmente la presenza di problemi dell'applicazione con kube-advisor

**Procedure consigliate** : eseguire regolarmente la versione più recente di `kube-advisor` strumento open source per rilevare i problemi del cluster. Se si applicano le quote di risorse in un cluster servizio Azure Kubernetes esistente, eseguire per prima cosa `kube-advisor` per trovare i pod che non hanno richieste di risorse e limiti definiti.

Lo strumento [Kube-Advisor][kube-advisor] è un progetto open source AKS associato che analizza un cluster Kubernetes e segnala i problemi riscontrati. Un controllo utile consiste nell'identificare i pod che non hanno richieste di risorse e limiti applicati.

Lo strumento Kube-Advisor può creare report sulle richieste di risorse e sui limiti mancanti in PodSpecs per le applicazioni Windows e sulle applicazioni Linux, ma lo strumento Kube-Advisor stesso deve essere pianificato in un pod Linux. È possibile pianificare l'esecuzione di un pod in un pool di nodi con un sistema operativo specifico usando un [selettore di nodo][k8s-node-selector] nella configurazione del Pod.

In un cluster servizio Azure Kubernetes che ospita molti team di sviluppo e applicazioni può essere difficile tenere traccia dei pod senza questi limiti e richieste di risorse impostati. Come procedura consigliata, eseguire regolarmente `kube-advisor` nei cluster servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sulle procedure consigliate ha illustrato come eseguire il cluster e i carichi di lavoro dal punto di vista dell'operatore di un cluster. Per informazioni sulle procedure consigliate amministrative, vedere procedure consigliate [per l'operatore cluster per l'isolamento e la gestione delle risorse in Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Sviluppare con Dev Spaces][dev-spaces]
* [Verificare la presenza di problemi con Kube-Advisor][aks-kubeadvisor]

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
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
