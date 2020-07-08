---
title: Creazione del volume del server NFS Ubuntu Linux
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare manualmente un volume del server NFS Ubuntu Linux per l'uso con i pod in Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 7db3f806df88e5b23012e97ba5c2f14ca65b2508
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80803467"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Creare e usare manualmente un volume del server Linux NFS (Network File System) con il servizio Azure Kubernetes (AKS)
La condivisione dei dati tra contenitori è spesso un componente necessario di applicazioni e servizi basati su contenitori. In genere sono disponibili vari pod che necessitano dell'accesso alle stesse informazioni in un volume permanente esterno.    
Sebbene i file di Azure siano un'opzione, la creazione di un server NFS in una macchina virtuale di Azure è un'altra forma di archiviazione condivisa permanente. 

In questo articolo viene illustrato come creare un server NFS in una macchina virtuale Ubuntu. Inoltre, concedere ai contenitori AKS l'accesso a questa file system condivisa.

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

Il cluster AKS deve risiedere nelle stesse reti virtuali o nelle reti virtuali con peering del server NFS. Il cluster deve essere creato in un VNET esistente, che può essere lo stesso VNET della macchina virtuale.

I passaggi per la configurazione con un VNET esistente sono descritti nella documentazione relativa alla [creazione di un cluster AKS in VNET esistenti][aks-virtual-network] e alla [connessione di reti virtuali con peering VNET][peer-virtual-networks]

Si presuppone anche che sia stata creata una macchina virtuale Ubuntu Linux (ad esempio, 18,04 LTS). Le impostazioni e le dimensioni possono essere di propria gradimento e possono essere distribuite tramite Azure. Per la Guida introduttiva a Linux, vedere [gestione delle VM Linux][linux-create].

Se si distribuisce il cluster AKS per la prima volta, Azure popola automaticamente il campo rete virtuale quando distribuisce il computer Ubuntu, rendendoli attivi all'interno della stessa VNET. Se invece si desidera lavorare con reti con peering, consultare la documentazione riportata sopra.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuzione del server NFS in una macchina virtuale
Ecco lo script per configurare un server NFS nella macchina virtuale Ubuntu:
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
Il server viene riavviato (a causa dello script) ed è possibile montare il server NFS in AKS.

>[!IMPORTANT]  
>Assicurarsi di sostituire il **AKS_SUBNET** con quello corretto dal cluster. in caso contrario, "*" aprirà il server NFS per tutte le porte e le connessioni.

Dopo aver creato la macchina virtuale, copiare lo script precedente in un file. Quindi, è possibile spostarlo dal computer locale o dovunque lo script sia nella VM usando: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Quando lo script si trova nella macchina virtuale, è possibile connettersi tramite SSH alla macchina virtuale ed eseguirlo tramite il comando:
```console
sudo ./nfs-server-setup.sh
```
Se l'esecuzione ha esito negativo a causa di un errore di autorizzazione negata, impostare l'autorizzazione di esecuzione tramite il comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Connessione del cluster AKS al server NFS
È possibile connettere il server NFS al cluster effettuando il provisioning di un volume permanente e di un'attestazione di volume permanente che specifichi come accedere al volume.

È necessario connettere i due servizi nella stessa rete virtuale o in reti virtuali con peering. Le istruzioni per la configurazione del cluster nella stessa VNET sono disponibili qui: [creazione del cluster AKS nel VNET esistente][aks-virtual-network]

Una volta che si trovano nella stessa rete virtuale (o con peering), è necessario effettuare il provisioning di un volume permanente e di un'attestazione di volume permanente nel cluster AKS. I contenitori possono quindi montare l'unità NFS nella directory locale.

Di seguito è riportato un esempio di definizione Kubernetes per il volume permanente (questa definizione presuppone che il cluster e la macchina virtuale si trovino nella stessa VNET):

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

È necessario anche un file di attestazione del volume permanente. Di seguito è riportato un esempio di cosa includere:

>[!IMPORTANT]  
>**"storageClassName"** deve rimanere una stringa vuota o l'attestazione non funzionerà.

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
Se non è possibile connettersi al server da un cluster, un problema potrebbe essere la directory esportata o il relativo padre, non dispone di autorizzazioni sufficienti per accedere al server.

Verificare che la directory di esportazione e la relativa directory padre dispongano di 777 autorizzazioni.

È possibile controllare le autorizzazioni eseguendo il comando seguente e le directory devono disporre delle autorizzazioni *' drwxrwxrwx '* :
```console
ls -l
```

## <a name="more-information"></a>Altre informazioni
Per ottenere una procedura dettagliata completa o per eseguire il debug del programma di installazione del server NFS, ecco un'esercitazione approfondita:
  - [Esercitazione su NFS][nfs-tutorial]

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

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
