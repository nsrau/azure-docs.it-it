---
title: Informazioni sulla gestione dell'archiviazione Kubernetes nel dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Descrive il modo in cui viene eseguita la gestione dell'archiviazione di Kubernetes in un dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: alkohli
ms.openlocfilehash: 34165071238ca3edf78ab9cca43639c23ce5ed2a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448709"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Gestione archiviazione Kubernetes sul dispositivo GPU Pro Azure Stack Edge

Nel dispositivo Azure Stack Edge Pro viene creato un cluster Kubernetes quando si configura il ruolo di calcolo. Una volta creato il cluster Kubernetes, le applicazioni in contenitori possono essere distribuite nel cluster Kubernetes in pod. Esistono diversi modi per fornire spazio di archiviazione ai Pod nel cluster Kubernetes. 

Questo articolo descrive i metodi per eseguire il provisioning dell'archiviazione in un cluster Kubernetes in generale e in particolare nel contesto del dispositivo Azure Stack Edge Pro. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Requisiti di archiviazione per i pod Kubernetes

I pod Kubernetes sono senza stato, ma le applicazioni che eseguono sono in genere con stato. Poiché i pod possono essere di breve durata e vengono riavviati, sottoposti a failover o spostati tra i nodi Kubernetes, è necessario soddisfare i requisiti seguenti per l'archiviazione associata al Pod. 

Lo spazio di archiviazione deve:

- Live all'esterno del Pod.
- Essere indipendente dal ciclo di vita pod.
- Essere accessibile da tutti i nodi Kubernetes.

Per comprendere come viene gestita l'archiviazione per Kubernetes, è necessario comprendere due risorse API: 

- **PersistentVolume (PV)**: si tratta di una parte di archiviazione nel cluster Kubernetes. L'archiviazione Kubernetes può essere sottoposta a provisioning statico come `PersistentVolume` . Può anche essere sottoposta a provisioning dinamico come  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: si tratta di una richiesta di archiviazione da un utente. PVC utilizzano risorse PV. PVC può richiedere modalità di accesso e dimensioni specifiche. 

    Poiché gli utenti hanno bisogno di `PersistentVolumes` proprietà diverse per i diversi problemi, gli amministratori del cluster devono essere in grado di offrire un'ampia gamma di tipi `PersistentVolumes` diversi rispetto alle modalità di accesso e alle dimensioni. Per queste esigenze, è necessaria la `StorageClass` risorsa.

Il provisioning dell'archiviazione può essere statico o dinamico. Ogni tipo di provisioning viene descritto nelle sezioni seguenti.

## <a name="static-provisioning"></a>Provisioning statico

Gli amministratori del cluster Kubernetes possono eseguire il provisioning statico dell'archiviazione. A tale scopo, è possibile utilizzare il back-end di archiviazione basato sui filesystem SMB/NFS oppure utilizzare dischi iSCSI collegati localmente sulla rete in un ambiente locale o persino utilizzare File di Azure o dischi di Azure nel cloud. Questo tipo di archiviazione non viene sottoposta a provisioning per impostazione predefinita e gli amministratori del cluster devono pianificare e gestire il provisioning. 
 
Di seguito è riportato un diagramma che illustra il modo in cui viene usata l'archiviazione con provisioning statico in Kubernetes: 

![Provisioning statico tramite PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Si verificano i passaggi seguenti: 

1. **Provisioning dell'archiviazione**: l'amministratore del cluster esegue il provisioning dell'archiviazione In questo esempio, l'amministratore del cluster crea una o più condivisioni SMB che creano automaticamente oggetti volume permanenti nel cluster Kubernetes corrispondente a queste condivisioni. 

1. **Archiviazione delle attestazioni**: si invia una distribuzione in PVC che richiede l'archiviazione. Questa attestazione per l'archiviazione è PersistentVolumeClaim (PVC). Se le dimensioni e la modalità di accesso del fotovoltaico corrispondono a quelle del PVC, il PVC è associato al PV. Il PVC e la mappa FV uno-a-uno.

1. **Montare il PVC nel contenitore**: quando il PVC è associato al PV, è possibile montare questo PVC in un percorso nel contenitore. Quando la logica dell'applicazione nel contenitore legge/scrive da/in questo percorso, i dati vengono scritti nell'archiviazione SMB.
 

## <a name="dynamic-provisioning"></a>Provisioning dinamico

Di seguito è riportato un diagramma che illustra il modo in cui viene usata l'archiviazione con provisioning statico in Kubernetes: 

![Provisioning dinamico tramite StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Si verificano i passaggi seguenti: 

1. **Definire la classe di archiviazione**: l'amministratore del cluster definisce una classe di archiviazione a seconda dell'ambiente operativo per il cluster Kubernetes. L'amministratore del cluster distribuisce anche un provisioner, che è ancora un altro pod o un'applicazione distribuita nel cluster Kubernetes. Il provisioning include tutti i dettagli per eseguire il provisioning delle condivisioni in modo dinamico.  

1. **Archiviazione delle attestazioni**: si invia un'applicazione che potrebbe richiedere lo spazio di archiviazione. Una volta creato un PVC con questo riferimento alla classe di archiviazione, viene richiamato il provisioning. 

1. Provisioning **dinamico dell'archiviazione**: il provisioning crea dinamicamente la condivisione associata all'archiviazione su disco locale. Una volta creata la condivisione, viene creato anche un oggetto PV che corrisponde automaticamente a questa condivisione.

1. **Montare il PVC nel contenitore**: quando il PVC è associato al PV, è possibile montare il PVC sul contenitore in un percorso allo stesso modo del provisioning statico e leggere o scrivere nella condivisione.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Provisioning dell'archiviazione su Azure Stack Edge Pro

Sul dispositivo Azure Stack Edge Pro, il provisioning statico `PersistentVolumes` viene creato usando le funzionalità di archiviazione del dispositivo. Quando si esegue il provisioning di una condivisione e si **Usa l'opzione Condividi con calcolo Edge** è abilitata, questa azione crea automaticamente una risorsa PV nel cluster Kubernetes.

![Creazione di una condivisione locale in portale di Azure per il provisioning statico](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Per usare la suddivisione in livelli nel cloud, è possibile creare una condivisione cloud perimetrale con l'opzione Usa la condivisione con calcolo Edge abilitata. Viene di nuovo creata automaticamente una PV per questa condivisione. Tutti i dati dell'applicazione scritti nella condivisione perimetrale vengono suddivisi in livelli nel cloud. 

![Creazione della condivisione cloud in portale di Azure per il provisioning statico](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

È possibile creare condivisioni SMB e NFS per eseguire il provisioning statico di PVs sul dispositivo Azure Stack Edge Pro. Una volta effettuato il provisioning del PV, si invierà un PVC per richiedere questa archiviazione. Di seguito è riportato un esempio di una distribuzione in PVC `yaml` che attesta l'archiviazione e usa le condivisioni di cui è stato effettuato il provisioning.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Per altre informazioni, vedere [distribuire un'applicazione con stato tramite il provisioning statico sul Azure stack Edge Pro tramite kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Per accedere alla stessa risorsa di archiviazione con provisioning statico, le opzioni di montaggio del volume corrispondenti per le associazioni di archiviazione sono le seguenti. `/home/input`È il percorso in cui il volume è accessibile all'interno del contenitore.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro dispone anche di un builtin `StorageClass` denominato `ase-node-local` che usa un archivio di dischi dati collegato al nodo Kubernetes. Questo `StorageClass` supporta il provisioning dinamico. È possibile creare un `StorageClass` riferimento nelle applicazioni pod e un PV viene creato automaticamente. Per ulteriori informazioni, vedere il [dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) per eseguire una query per `ase-node-local StorageClass` .

![Classe di archiviazione predefinita nel dashboard di Kubernetes](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Per altre informazioni, vedere [distribuire un'applicazione con stato tramite il provisioning dinamico in Azure stack Edge Pro tramite kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Scegliere il tipo di archiviazione

Potrebbe essere necessario scegliere il tipo di archiviazione in base al carico di lavoro che si sta distribuendo. 

- Se si desidera la `ReadWriteMany` modalità di accesso per la in `PersistentVolumes` cui i volumi vengono montati in lettura/scrittura da molti nodi distribuiti, utilizzare il provisioning statico per le condivisioni SMB/NFS.

- Se le applicazioni che si stanno distribuendo hanno un requisito di conformità POSIX, ad esempio applicazioni come MongoDB, PostgreSQL, MySQL o Prometeo, usare la StorageClass incorporata. Le modalità di accesso sono `ReadWriteOnce` o il volume è montato come lettura/scrittura da un singolo nodo. 


Per altre informazioni sulle modalità di accesso, vedere [Kubernetes Volumes Access Mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Passaggi successivi

Per comprendere come è possibile eseguire il provisioning statico di un `PersistentVolume` , vedere:

- [Distribuire un'applicazione con stato tramite il provisioning statico sul Azure stack Edge Pro tramite kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Per informazioni su come è possibile eseguire il provisioning dinamico di un `StorageClass` , vedere:

- [Distribuire un'applicazione con stato tramite il provisioning dinamico sul Azure stack Edge Pro tramite kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
