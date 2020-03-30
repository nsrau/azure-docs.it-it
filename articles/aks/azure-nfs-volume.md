---
title: Creare un server Ubuntu NFS (Network File System) per l'utilizzo da parte dei pod del servizio Azure Kubernetes (AKS)
description: Informazioni su come creare manualmente un volume di NFS Ubuntu Linux Server da usare con i pod nel servizio Azure Kubernetes (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596624"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Creare e usare manualmente un volume di server Linux NFS (Network File System) con Azure Kubernetes Service (AKS)
La condivisione dei dati tra i contenitori è spesso un componente necessario delle applicazioni e dei servizi basati su contenitori. Di solito si dispone di vari pod che devono accedere alle stesse informazioni su un volume persistente esterno.    
Mentre i file di Azure sono un'opzione, la creazione di un server NFS in una macchina virtuale di Azure è un'altra forma di archiviazione condivisa persistente. 

Questo articolo illustra come creare un server NFS in una macchina virtuale Ubuntu. E anche dare ai contenitori AKS l'accesso a questo file system condiviso.

## <a name="before-you-begin"></a>Prima di iniziare
In questo articolo si presuppone che si dispone di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la guida introduttiva di AKS [usando l'interfaccia della riga di comando][aks-quickstart-cli] di Azure o il portale di [Azure.][aks-quickstart-portal]

Il cluster AKS dovrà trovarsi nelle stesse reti virtuali o con peering del server NFS. Il cluster deve essere creato in una rete virtuale esistente, che può essere la stessa VNET della macchina virtuale.

I passaggi per la configurazione con una rete virtuale esistente sono descritti nella documentazione: creazione di [un cluster AKS nella rete virtuale esistente][aks-virtual-network] e connessione di reti virtuali con [peering VNET][peer-virtual-networks]

Si presuppone inoltre che sia stata creata una macchina virtuale Ubuntu Linux (ad esempio, 18.04 LTS). Le impostazioni e le dimensioni possono essere di gradimento e possono essere distribuite tramite Azure.Settings and size can be to your mtimento ti piace and can be deployed through Azure. Per informazioni introduttive su Linux, vedere [Gestione di macchine virtuali Linux.For Linux][linux-create]quickstart, see Linux VM management .

Se si distribuisce prima il cluster AKS, Azure popolerà automaticamente il campo della rete virtuale durante la distribuzione del computer Ubuntu, rendendolo attivo all'interno della stessa rete virtuale. Ma se si desidera lavorare con le reti di peered, consultare la documentazione precedente.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuzione del server NFS in una macchina virtuale
Ecco lo script per configurare un server NFS all'interno della macchina virtuale Ubuntu:Here is the script to set up an NFS Server within your Ubuntu virtual machine:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Il server verrà riavviato (a causa dello script) ed è possibile montare il server NFS in AKS.

>[!IMPORTANT]  
>Assicurarsi di sostituire il **AKS_SUBNET** con quello corretto dal cluster, altrimenti il server NFS verrà aperto a tutte le porte e le connessioni.

Dopo aver creato la macchina virtuale, copiare lo script precedente in un file. È quindi possibile spostarlo dal computer locale o ovunque si trovi lo script nella macchina virtuale usando:Then, you can move it from your local machine, or where the script is, into the VM using: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Dopo aver fatto parte dello script nella macchina virtuale, è possibile eseguire lo ssh nella macchina virtuale ed eseguirlo tramite il comando:Once your script is in your VM, you can ssh into the VM and execute it via the command:
```console
sudo ./nfs-server-setup.sh
```
Se l'esecuzione non riesce a causa di un errore di autorizzazione negata, impostare l'autorizzazione di esecuzione tramite il comando:If its execution fails because of a permission denied error, set execution permission via the command:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Connessione del cluster AKS al server NFS
È possibile connettere il server NFS al cluster eseguendo il provisioning di un volume persistente e di un'attestazione di volume persistente che specifica come accedere al volume.

È necessaria la connessione dei due servizi nella stessa rete virtuale o in reti virtuali con peered. Le istruzioni per la configurazione del cluster nella stessa rete virtuale sono qui: [Creazione di cluster AKS nella rete virtuale esistente][aks-virtual-network]

Una volta che si trovano nella stessa rete virtuale (o peered), è necessario eseguire il provisioning di un volume persistente e un'attestazione di volume persistente nel cluster AKS. I contenitori possono quindi montare l'unità NFS nella directory locale.

Ecco un esempio di definizione di Kubernetes per il volume persistente (questa definizione presuppone che il cluster e la macchina virtuale si trovino nella stessa rete virtuale):Here is an example Kubernetes definition for the persistent volume (This definition assumes your cluster and VM are in the same VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Sostituire **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** con le informazioni sul server NFS.

Avrai anche bisogno di un file di attestazione di volume persistente. Ecco un esempio di cosa includere:

>[!IMPORTANT]  
>**"storageClassName"** deve rimanere una stringa vuota altrimenti l'attestazione non funzionerà.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se non è possibile connettersi al server da un cluster, un problema potrebbe essere la directory esportata o il relativo padre non dispone di autorizzazioni sufficienti per accedere al server.

Verificare che la directory di esportazione e la relativa directory padre dispongano di autorizzazioni 777.

È possibile controllare le autorizzazioni eseguendo il comando seguente e le directory devono avere autorizzazioni *'drwxrwxrwx':*
```console
ls -l
```

## <a name="more-information"></a>Ulteriori informazioni
Per ottenere una procedura dettagliata completa o per facilitare il debug dell'installazione del server NFS, ecco un'esercitazione approfondita:To get a full walkthrough or to help you debug your NFS Server setup, here is an in-depth tutorial:
  - [Esercitazione su NFS][nfs-tutorial]

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per l'archiviazione e i backup in AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
