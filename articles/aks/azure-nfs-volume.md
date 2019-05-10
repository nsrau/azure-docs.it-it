---
title: Creare un Ubuntu Server NFS (Network File System) per l'uso da POD di Azure Kubernetes Service (AKS)
description: Informazioni su come creare manualmente un volume NFS Ubuntu Linux Server per l'uso con POD in Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468500"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Creare manualmente e usare un volume del Linux Server NFS (Network File System) con Azure Kubernetes Service (AKS)
Condivisione dei dati tra i contenitori è spesso un componente necessario di applicazioni e servizi basati su contenitori. In genere si dispone di vari POD che richiedono l'accesso alle stesse informazioni in un volume permanente esterno.    
Anche se i file di Azure sono un'opzione, la creazione di un Server NFS in una macchina virtuale di Azure è un altro formato di archiviazione condivisa permanente. 

Questo articolo illustrerà come creare un Server NFS in una macchina virtuale Ubuntu. E anche fornire l'accesso di contenitori del servizio contenitore di AZURE per questo file system condiviso.

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone che si dispone di un Cluster servizio contenitore di AZURE esistente. Se è necessario un Cluster del servizio contenitore di AZURE, vedere la Guida introduttiva AKS [tramite la CLI di Azure] [ aks-quickstart-cli] oppure [usando il portale di Azure][aks-quickstart-portal].

Durata (TTL) nelle reti virtuali con peering o stesso come il Server NFS, sarà necessario il Cluster AKS. Il cluster deve essere creato in una rete virtuale esistente, che può essere nella stessa rete virtuale della macchina virtuale.

Nella documentazione sono descritti i passaggi per la configurazione con una rete virtuale esistente: [creazione di Cluster AKS nella rete virtuale esistente] [ aks-virtual-network] e [connessione di reti virtuali con peering reti virtuali][peer-virtual-networks]

Si presuppone inoltre che aver creato una macchina virtuale di Linux Ubuntu (ad esempio, 18.04 LTS). Le impostazioni e le dimensioni possono essere in base alle esigenze e può essere distribuiti tramite Azure. Per la Guida introduttiva di Linux, vedere [gestione delle macchine Virtuali linux][linux-create].

Se si distribuisce il Cluster AKS prima di tutto, Azure automaticamente popolerà il campo di rete virtuale durante la distribuzione di computer Ubuntu, rendendoli in tempo reale all'interno della stessa rete virtuale. Ma se si desidera lavorare invece con le reti con peering, consultare la documentazione precedente.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuzione del Server NFS a una macchina virtuale
Ecco lo script per configurare un Server NFS all'interno della macchina virtuale Ubuntu:
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
Il server verrà riavviato (a causa di uno script) ed è possibile montare il Server NFS al servizio contenitore di AZURE

>[!IMPORTANT]  
>Assicurarsi di sostituire il **AKS_SUBNET** con quello corretto dal cluster oppure "*" verrà aperto il Server NFS per tutte le porte e le connessioni.

Dopo aver creato la macchina virtuale, copiare lo script precedente in un file. Quindi, è possibile spostarlo dal computer locale, oppure ogni volta che la macchina virtuale con lo script è: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Al termine dello script nella macchina virtuale, è possibile usare ssh alla macchina virtuale ed eseguirlo tramite il comando:
```console
sudo ./nfs-server-setup.sh
```
Se l'esecuzione ha esito negativo a causa di un errore di autorizzazione negata, impostare le autorizzazioni di esecuzione tramite il comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Cluster servizio contenitore di AZURE che si connette al Server NFS
È possibile connettere il Server NFS per il cluster di effettuando il provisioning di un volume permanente e attestazione di volume permanente che specifica la modalità di accesso al volume.  
Ci si connette i due servizi nello stesse o con peering reti virtuali è necessario. Le istruzioni per configurare il cluster nella stessa rete virtuale sono qui: [creazione Cluster di AKS nella rete virtuale esistente][aks-virtual-network]

Dopo che sono nella stessa rete virtuale o eseguire il peering, è necessario eseguire il provisioning di un volume permanente e un volume permanente di attestazione nel Cluster AKS. I contenitori possono quindi montare l'unità NFS alla propria directory locale.

Ecco un esempio di definizione di kubernetes per il volume permanente (questa definizione si presuppone che il cluster e macchine Virtuali sono nella stessa rete virtuale):

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
Sostituire **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** con informazioni sul Server NFS.

È necessario anche un file di attestazione di volume permanente. Di seguito è riportato un esempio di cosa includere:

>[!IMPORTANT]  
>**"storageClassName"** deve rimanere vuoto stringa o l'attestazione non funzionerà.

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

## <a name="troubleshooting"></a>risoluzione dei problemi
Se non è possibile connettersi al server da un cluster, un problema potrebbe essere la directory di esportazione o il relativo elemento padre, non ha autorizzazioni sufficienti per accedere al server.

Verificare che sia la directory di esportazione e la relativa directory padre stato 777 autorizzazioni.

È possibile controllare le autorizzazioni eseguendo il comando seguente e le directory devono avere *'drwxrwxrwx'* autorizzazioni:
```console
ls -l
```

## <a name="more-information"></a>Altre informazioni
Per ottenere una procedura dettagliata completa o che consentono di eseguire il debug del programma di installazione di Server NFS, ecco un'esercitazione dettagliata:
  - [NFS Tutorial][nfs-tutorial]

## <a name="next-steps"></a>Passaggi successivi

Per procedure consigliate associati, vedere [procedure consigliate per i backup nel servizio contenitore di AZURE e archiviazione][operator-best-practices-storage].

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
