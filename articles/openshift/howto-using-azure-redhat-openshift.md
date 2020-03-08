---
title: Creare un cluster Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Creare un cluster con Azure Red Hat OpenShift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899308"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Creare, accedere e gestire un cluster Azure Red Hat OpenShift 4,3

> [!IMPORTANT]
> Azure Red Hat OpenShift (ARO) 4,3 è disponibile in versione di anteprima. Le funzionalità di anteprima sono self-service e vengono fornite così come sono e sono disponibili e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Pertanto, le funzionalità non sono destinate all'uso in produzione.

## <a name="prerequisites"></a>Prerequisiti

Per creare un cluster Azure Red Hat OpenShift 4,3, è necessario quanto segue:

- Versione 2.0.72 o successiva dell'interfaccia della riga di comando di Azure
  
- Estensione ' AZ Aro '

- Una rete virtuale contenente due subnet vuote, ciascuna senza un gruppo di sicurezza di rete collegato.  Il cluster verrà distribuito in queste subnet.

- Un'applicazione AAD del cluster (ID client e segreto) e un'entità servizio o autorizzazioni AAD sufficienti per `az aro create` per creare automaticamente un'applicazione AAD e un'entità servizio.

- L'entità servizio e l'entità servizio del cluster di relying party devono avere il ruolo Collaboratore nella rete virtuale del cluster.  Se si dispone del ruolo "amministratore accesso utenti" nella rete virtuale, `az aro create` configurerà automaticamente le assegnazioni di ruolo.

### <a name="install-the-az-aro-extension"></a>Installare l'estensione ' AZ Aro '
L'estensione `az aro` consente di creare, accedere ed eliminare i cluster OpenShift di Azure Red Hat direttamente dalla riga di comando usando l'interfaccia della riga di comando di Azure.

> [!Note] 
> L'estensione `az aro` è attualmente in anteprima. Potrebbe essere modificato o rimosso in una versione futura.
> Per acconsentire esplicitamente all'anteprima dell'estensione `az aro` è necessario registrare il provider di risorse `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Accedere ad Azure.

   ```console
   az login
   ```

2. Eseguire il comando seguente per installare l'estensione `az aro`:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Verificare che l'estensione ARO sia registrata.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuote

Seguire questa procedura per creare una rete virtuale contenente due subnet vuote.

1. Impostare le variabili seguenti.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Creare un gruppo di risorse per il cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Creare la rete virtuale.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Aggiungere due subnet vuote alla rete virtuale.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Disabilitare i criteri di rete per il servizio di collegamento privato nella rete virtuale e nelle subnet. Questo è un requisito per il servizio ARO per accedere e gestire il cluster.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Creare un cluster

Eseguire il comando seguente per creare un cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> La creazione di un cluster richiede in genere circa 35 minuti.

## <a name="access-the-cluster-console"></a>Accedere alla console del cluster

È possibile trovare l'URL della console del cluster (nel formato `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) nella risorsa cluster Azure Red Hat OpenShift 4,3. Eseguire il comando seguente per visualizzare la risorsa:

```console
az aro list -o table
```

È possibile accedere al cluster tramite l'utente `kubeadmin`.  Eseguire il comando seguente per trovare la password per l'utente `kubeadmin`:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Eliminazione di un cluster

Eseguire il comando seguente per eliminare un cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```