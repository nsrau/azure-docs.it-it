---
title: Informazioni sul controllo degli accessi in base al ruolo Kubernetes nel dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto il modo in cui il controllo degli accessi in base al ruolo Kubernetes viene eseguito in un dispositivo Pro Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 0880ae64520997fc6b41ba4a7e8508d927235a8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320813"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Controllo degli accessi in base al ruolo Kubernetes sul dispositivo GPU Pro Azure Stack Edge


Nel dispositivo Azure Stack Edge Pro, quando si configura il ruolo di calcolo, viene creato un cluster Kubernetes. È possibile usare il controllo degli accessi in base al ruolo Kubernetes (RBAC) per limitare l'accesso alle risorse del cluster sul dispositivo.

Questo articolo fornisce una panoramica del sistema RBAC fornito da Kubernetes e in che modo il controllo degli accessi in base al ruolo Kubernetes è implementato nel dispositivo Azure Stack Edge Pro. 

## <a name="rbac-for-kubernetes"></a>RBAC per Kubernetes

Kubernetes RBAC consente di assegnare a utenti o gruppi di utenti le autorizzazioni necessarie per eseguire operazioni quali la creazione o la modifica di risorse o la visualizzazione di log da carichi di lavoro di applicazioni in esecuzione. Queste autorizzazioni possono essere limitate a un singolo spazio dei nomi o concesse nell'intero cluster. 

Quando si configura il cluster Kubernetes, viene creato un singolo utente corrispondente a questo cluster e viene chiamato utente amministratore del cluster.  Un `kubeconfig` file è associato all'utente amministratore del cluster. Il `kubeconfig` file è un file di testo che contiene tutte le informazioni di configurazione necessarie per la connessione al cluster per autenticare l'utente.

## <a name="namespaces-types"></a>Tipi di spazi dei nomi

Le risorse Kubernetes, ad esempio pod e distribuzioni, sono raggruppate logicamente in uno spazio dei nomi. Questi raggruppamenti consentono di dividere logicamente un cluster Kubernetes e di limitare l'accesso per creare, visualizzare o gestire le risorse. Gli utenti possono interagire solo con le risorse all'interno degli spazi dei nomi assegnati.

Gli spazi dei nomi sono destinati all'uso in ambienti con molti utenti distribuiti in più team o progetti. Per altre informazioni, vedere [Spazi dei nomi di Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Il dispositivo Azure Stack Edge Pro ha gli spazi dei nomi seguenti:

- **Spazio dei nomi System** : questo spazio dei nomi è il punto in cui si trovano le risorse principali, ad esempio le funzionalità di rete come DNS e proxy o il dashboard Kubernetes. In genere non si distribuiscono le proprie applicazioni in questo spazio dei nomi. Usare questo spazio dei nomi per eseguire il debug di eventuali problemi del cluster Kubernetes. 

    Nel dispositivo sono presenti più spazi dei nomi di sistema e i nomi corrispondenti a questi spazi dei nomi di sistema sono riservati. Di seguito è riportato un elenco degli spazi dei nomi di sistema riservati: 
    - Kube-sistema
    - metallb-sistema
    - DBE-spazio dei nomi
    - default
    - kubernetes-dashboard
    - Kube-node-lease
    - Kube-pubblico


    Assicurarsi di non usare nomi riservati per gli spazi dei nomi utente creati. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Spazio dei nomi utente** : si tratta degli spazi dei nomi che è possibile creare tramite **kubectl** o tramite l'interfaccia di PowerShell del dispositivo per distribuire le applicazioni in locale.
 
- **IOT Edge spazio dei nomi** : è possibile connettersi a questo `iotedge` spazio dei nomi per gestire le applicazioni distribuite tramite IOT Edge.

- **Spazio dei nomi di Azure Arc** : è possibile connettersi a questo `azure-arc` spazio dei nomi per gestire le applicazioni distribuite tramite Azure Arc. Con Azure Arc è inoltre possibile distribuire applicazioni in altri spazi dei nomi utente. 

## <a name="namespaces-and-users"></a>Spazi dei nomi e utenti

Nel mondo reale, è importante suddividere il cluster in più spazi dei nomi. 

- **Più utenti**: se sono presenti più utenti, più spazi dei nomi consentiranno a tali utenti di distribuire le applicazioni e i servizi negli spazi dei nomi specifici in modo isolato l'uno dall'altro. 
- **Singolo utente**: anche se è presente un singolo utente, più spazi dei nomi consentiranno all'utente di eseguire più versioni delle applicazioni nello stesso cluster Kubernetes.

### <a name="roles-and-rolebindings"></a>Roles e RoleBindings

Kubernetes ha il concetto di associazione ruolo e ruolo che consente di concedere autorizzazioni a un utente o a una risorsa a livello di spazio dei nomi e a livello di cluster. 

- **Ruoli**: è possibile definire le autorizzazioni per gli utenti come **ruolo** e quindi usare i **ruoli** per concedere le autorizzazioni all'interno di uno spazio dei nomi. 
- **RoleBindings**: dopo aver definito i ruoli, è possibile usare **RoleBindings** per assegnare i ruoli per un determinato spazio dei nomi. 

Questo approccio consente di separare logicamente un singolo cluster Kubernetes, con la possibilità per gli utenti di accedere solo alle risorse dell'applicazione nello spazio dei nomi assegnato. 

## <a name="rbac-on-azure-stack-edge-pro"></a>RBAC in Azure Stack Edge Pro

Nell'implementazione corrente di RBAC, Azure Stack Edge Pro consente di eseguire le azioni seguenti da un spazio di PowerShell con restrizioni:

- Creare gli spazi dei nomi.  
- Creare utenti aggiuntivi.
- Concedere all'amministratore l'accesso agli spazi dei nomi creati. Tenere presente che non sarà possibile accedere al ruolo di amministratore del cluster o a una visualizzazione delle risorse a livello di cluster.
- Ottenere `kubeconfig` il file con le informazioni per accedere al cluster Kubernetes.


Il dispositivo Azure Stack Edge Pro dispone di più spazi dei nomi di sistema ed è possibile creare spazi dei nomi utente con `kubeconfig` file per accedere a tali spazi dei nomi. Gli utenti hanno il controllo completo su questi spazi dei nomi e possono creare o modificare gli utenti o concedere agli utenti l'accesso. Solo l'amministratore del cluster ha accesso completo agli spazi dei nomi di sistema e alle risorse a livello di cluster. Un oggetto `aseuser` dispone di accesso in sola lettura agli spazi dei nomi di sistema.

Di seguito è riportato un diagramma che illustra l'implementazione di RBAC sul dispositivo Azure Stack Edge Pro.

![RBAC sul dispositivo Azure Stack Edge Pro](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

In questo diagramma Alice, Bob e Chuck hanno accesso solo agli spazi dei nomi utente assegnati, che in questo caso sono `ns1` rispettivamente, `ns2` e `ns3` . All'interno di questi spazi dei nomi, l'accesso è amministratore. L'amministratore del cluster d'altra parte dispone di accesso amministrativo agli spazi dei nomi di sistema e alle risorse a livello di cluster.

In qualità di utente, è possibile creare spazi dei nomi e utenti, assegnare utenti a spazi dei nomi o scaricare `kubeconfig` file. Per istruzioni dettagliate, vedere [accedere al cluster Kubernetes tramite kuebctl in Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Quando si lavora con gli spazi dei nomi e gli utenti nei dispositivi Azure Stack Edge Pro, si applicano le avvertenze seguenti:

- Non è consentito eseguire alcuna operazione, ad esempio creare utenti, concedere o revocare l'accesso allo spazio dei nomi all'utente per uno qualsiasi degli spazi dei nomi di sistema. Esempi di spazi dei nomi di sistema includono `kube-system` ,, `metallb-system` `kubernetes-dashboard` , `default` , `kube-node-lease` , `kube-public` . Gli spazi dei nomi System includono anche gli spazi dei nomi riservati per i tipi di distribuzione, ad esempio `iotedge` (IOT Edge spazio dei nomi) e `azure-arc` (spazio dei nomi di Azure Arc).
- È possibile creare spazi dei nomi utente e all'interno di tali spazi dei nomi creare utenti aggiuntivi e concedere o revocare l'accesso allo spazio dei nomi a tali utenti.
- Non è possibile creare spazi dei nomi con nomi identici a quelli per qualsiasi spazio dei nomi di sistema. I nomi degli spazi dei nomi di sistema sono riservati.  
- Non è possibile creare spazi dei nomi utente con nomi già utilizzati da altri spazi dei nomi utente. Se, ad esempio, si dispone di un oggetto `test-ns` creato, non è possibile creare un altro `test-ns` spazio dei nomi.
- Non è consentito creare utenti con nomi già riservati. Ad esempio, `aseuser` è un utente riservato e non può essere utilizzato.


## <a name="next-steps"></a>Passaggi successivi

Per comprendere come è possibile creare un utente, creare uno spazio dei nomi e concedere agli utenti l'accesso allo spazio dei nomi, vedere [accedere a un cluster Kubernetes tramite kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

