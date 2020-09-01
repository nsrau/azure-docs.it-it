---
title: Informazioni sul controllo degli accessi in base al ruolo Kubernetes sul dispositivo Azure Stack Edge | Microsoft Docs
description: Viene descritto come viene eseguito il controllo degli accessi in base al ruolo Kubernetes in un dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 310fde15a850214aa1741c9cb587c0edcf570a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085385"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Controllo degli accessi in base al ruolo Kubernetes sul dispositivo Azure Stack Edge


Nel dispositivo Azure Stack Edge, quando si configura il ruolo di calcolo, viene creato un cluster Kubernetes. È possibile usare il controllo degli accessi in base al ruolo Kubernetes (RBAC) per limitare l'accesso alle risorse del cluster sul dispositivo.

Questo articolo fornisce una panoramica del sistema RBAC fornito da Kubernetes e in che modo il controllo degli accessi in base al ruolo Kubernetes è implementato nel dispositivo Azure Stack Edge. 

## <a name="rbac-for-kubernetes"></a>RBAC per Kubernetes

Kubernetes RBAC consente di assegnare a utenti o gruppi di utenti le autorizzazioni necessarie per eseguire operazioni quali la creazione o la modifica di risorse o la visualizzazione di log da carichi di lavoro di applicazioni in esecuzione. Queste autorizzazioni possono essere limitate a un singolo spazio dei nomi o concesse nell'intero cluster. 

Quando si configura il cluster Kubernetes, viene creato un singolo utente corrispondente a questo cluster e viene chiamato utente amministratore del cluster.  Un `kubeconfig` file è associato all'utente amministratore del cluster. Il `kubeconfig` file è un file di testo che contiene tutte le informazioni di configurazione necessarie per la connessione al cluster per autenticare l'utente. 

### <a name="namespaces-and-users"></a>Spazi dei nomi e utenti

Nel mondo reale, è importante suddividere il cluster in più spazi dei nomi. 

- **Più utenti**: se sono presenti più utenti, più spazi dei nomi consentiranno a tali utenti di distribuire le applicazioni e i servizi negli spazi dei nomi specifici in modo isolato l'uno dall'altro. 
- **Singolo utente**: anche se è presente un singolo utente, più spazi dei nomi consentiranno all'utente di eseguire più versioni delle applicazioni nello stesso cluster Kubernetes.

### <a name="roles-and-rolebindings"></a>Roles e RoleBindings

Kubernetes ha il concetto di associazione ruolo e ruolo che consente di concedere autorizzazioni a un utente o a una risorsa a livello di spazio dei nomi e a livello di cluster. 

- **Ruoli**: è possibile definire le autorizzazioni per gli utenti come **ruolo** e quindi usare i **ruoli** per concedere le autorizzazioni all'interno di uno spazio dei nomi. 
- **RoleBindings**: dopo aver definito i ruoli, è possibile usare **RoleBindings** per assegnare i ruoli per un determinato spazio dei nomi. 

Questo approccio consente di separare logicamente un singolo cluster Kubernetes, con la possibilità per gli utenti di accedere solo alle risorse dell'applicazione nello spazio dei nomi assegnato. 


## <a name="rbac-on-azure-stack-edge"></a>RBAC su Azure Stack Edge

Nell'implementazione corrente di RBAC, Azure Stack Edge consente di eseguire le azioni seguenti da un spazio di PowerShell con restrizioni:

- Creare gli spazi dei nomi.  
- Creare utenti aggiuntivi.
- Concedere all'amministratore l'accesso agli spazi dei nomi creati. Tenere presente che non sarà possibile accedere al ruolo di amministratore del cluster o a una visualizzazione delle risorse a livello di cluster.
- Ottenere `kubeconfig` il file con le informazioni per accedere al cluster Kubernetes.


Il dispositivo Azure Stack Edge dispone di più spazi dei nomi di sistema ed è possibile creare spazi dei nomi utente con `kubeconfig` file per accedere a tali spazi dei nomi. Gli utenti hanno il controllo completo su questi spazi dei nomi e possono creare o modificare gli utenti o concedere agli utenti l'accesso. Solo l'amministratore del cluster ha accesso completo agli spazi dei nomi di sistema e alle risorse a livello di cluster. Un oggetto `aseuser` dispone di accesso in sola lettura agli spazi dei nomi di sistema.

Di seguito è riportato un diagramma che illustra l'implementazione di RBAC sul dispositivo Azure Stack Edge.

![RBAC sul dispositivo Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

In questo diagramma Alice, Bob e Chuck hanno accesso solo agli spazi dei nomi utente assegnati, che in questo caso sono `ns1` rispettivamente, `ns2` e `ns3` . All'interno di questi spazi dei nomi, l'accesso è amministratore. L'amministratore del cluster d'altra parte dispone di accesso amministrativo agli spazi dei nomi di sistema e alle risorse a livello di cluster.

È possibile utilizzare `kubectl` i comandi per creare spazi dei nomi, assegnare utenti, assegnare utenti o `kubeconfig` scaricare file. Di seguito è riportato un flusso di lavoro di alto livello:

1. Creare uno spazio dei nomi e un utente.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Creare un utente.  

    `New-HcsKubernetesUser -UserName`  

3. Associare lo spazio dei nomi all'utente creato.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Salvare la configurazione utente in `C:\Users\<username>\.kube` .  

5. Installare `kubectl` e avviare la distribuzione di applicazioni in `kubectl` . 

Per istruzioni dettagliate, vedere [accedere al cluster Kubernetes tramite kuebctl sul Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Quando si lavora con gli spazi dei nomi e gli utenti nei dispositivi Azure Stack Edge, si applicano le avvertenze seguenti:

- Non è consentito eseguire alcuna operazione, ad esempio creare utenti, concedere o revocare l'accesso allo spazio dei nomi all'utente per uno qualsiasi degli spazi dei nomi di sistema. Esempi di spazi dei nomi di sistema includono `kube-system` ,, `metallb-system` `kubernetes-dashboard` , `default` , `kube-node-lease` , `kube-public` . Gli spazi dei nomi System includono anche gli spazi dei nomi riservati per i tipi di distribuzione, ad esempio `iotedge` (IOT Edge spazio dei nomi) e `azure-arc` (spazio dei nomi di Azure Arc).
- È possibile creare spazi dei nomi utente e all'interno di tali spazi dei nomi creare utenti aggiuntivi e concedere o revocare l'accesso allo spazio dei nomi a tali utenti.
- Non è possibile creare spazi dei nomi con nomi identici a quelli per qualsiasi spazio dei nomi di sistema. I nomi degli spazi dei nomi di sistema sono riservati.  
- Non è possibile creare spazi dei nomi utente con nomi già utilizzati da altri spazi dei nomi utente. Se, ad esempio, si dispone di un oggetto `test-ns` creato, non è possibile creare un altro `test-ns` spazio dei nomi.
- Non è consentito creare utenti con nomi già riservati. Ad esempio, `aseuser` è un amministratore del cluster riservato e non può essere usato.

Per ulteriori informazioni sugli spazi dei nomi Azure Stack Edge, vedere [tipi di spazio dei nomi](azure-stack-edge-gpu-kubernetes-workload-management.md#namespaces-types).


<!--To deploy applications on an Azure Stack Edge device, use the following :
 
- First, you will use the PowerShell runspace to create a user, create a namespace, and grant user access to that namespace.
- Next, you will use the Azure Stack Edge resource in the Azure portal to create persistent volumes using either static or dynamic provisioning for the stateful applications that you will deploy.
- Finally, you will use the services to expose applications externally and within the Kubernetes cluster.-->

## <a name="next-steps"></a>Passaggi successivi

Per comprendere come è possibile creare un utente, creare uno spazio dei nomi e concedere agli utenti l'accesso allo spazio dei nomi, vedere [accedere a un cluster Kubernetes tramite kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

