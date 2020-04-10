---
title: Creare un cluster OpenShift 4.3 di Azure Red Hat Documenti Microsoft
description: Creare un cluster con Azure Red Hat OpenShift 4.3Create a cluster with Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, cappello rosso, cli
ms.openlocfilehash: 9488ef593cf4ec8600dcb42ea4a2cefa4fcb1446
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998806"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Creare, accedere e gestire un cluster Azure Red Hat OpenShift 4.3Create, access, and manage an Azure Red Hat OpenShift 4.3 Cluster

> [!IMPORTANT]
> Si noti che Azure Red Hat OpenShift 4.3 è attualmente disponibile solo in anteprima privata negli Stati Uniti orientali e negli Stati Uniti orientali 2. L'accettazione dell'anteprima privata è solo su invito. Assicurarsi di registrare la sottoscrizione prima di tentare di abilitare questa funzionalità: [Registrazione di Azure Red Hat OpenShift Private Preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Le funzionalità di anteprima sono self-service e vengono fornite come sono e come disponibili e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Pertanto, le funzionalità non sono destinate all'utilizzo in produzione.

## <a name="prerequisites"></a>Prerequisiti

Per creare un cluster Azure Red Hat OpenShift 4.3, è necessario quanto segue:You'll need the following to create an Azure Red Hat OpenShift 4.3 cluster:

- Interfaccia della riga di comando di Azure versione 2.0.72 o successivaAzure CLI version 2.0.72 or greater
  
- L'estensione "az aro"

- Una rete virtuale contenente due subnet vuote, ognuna senza gruppo di sicurezza di rete collegato.  Il cluster verrà distribuito in queste subnet.

- Un'applicazione AAD del cluster (ID client e segreto) `az aro create` e un'entità servizio o autorizzazioni AAD sufficienti per creare automaticamente un'applicazione AAD e un'entità servizio.

- L'entità servizio componente e l'entità servizio cluster devono disporre del ruolo Collaboratore nella rete virtuale del cluster.  Se nella rete virtuale è impostato il ruolo `az aro create` "Amministratore accesso utenti", le assegnazioni di ruolo verranno configurate automaticamente.

### <a name="install-the-az-aro-extension"></a>Installare l'estensione 'az aro'
L'estensione `az aro` consente di creare, accedere ed eliminare cluster Azure Red Hat OpenShift direttamente dalla riga di comando usando l'interfaccia della riga di comando di Azure.The extension allows you to create, access, and delete Azure Red Hat OpenShift clusters directly from the command line using the Azure CLI.

> [!Note] 
> L'estensione `az aro` è corrente nell'anteprima. Può essere modificato o rimosso in una versione futura.
> Per acconsentire `az aro` esplicitamente all'anteprima `Microsoft.RedHatOpenShift` dell'estensione, è necessario registrare il provider di risorse.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Accedere ad Azure.

   ```console
   az login
   ```

2. Eseguire il comando seguente `az aro` per installare l'estensione:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Verificare che l'estensione ARO sia registrata.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuoteCreate a virtual network containing two empty subnets

Seguire questi passaggi per creare una rete virtuale contenente due subnet vuote.

1. Impostare le variabili seguenti.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > Il segreto pull facoltativo consente al cluster di accedere ai registri dei contenitori Red Hat insieme a contenuto aggiuntivo.
   >
   > Accedere al segreto di https://cloud.redhat.com/openshift/install/azure/installer-provisioned pull passando a e facendo clic su *Copia segreto pull*.
   >
   > Dovrai accedere al tuo account Red Hat o creare un nuovo account Red Hat con la tua email aziendale e accettare i termini e le condizioni.
 

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

5. Disabilitare i criteri di rete per il servizio di collegamento privato nella rete virtuale e nelle subnet. Si tratta di un requisito per il servizio ARO per accedere e gestire il cluster.

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
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> In genere sono necessari circa 35 minuti per creare un cluster.

## <a name="access-the-cluster-console"></a>Accedere alla console del cluster

L'URL della console del cluster `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`(nel modulo ) è riportato nella risorsa cluster Azure Red Hat OpenShift 4.3. Eseguire il comando seguente per visualizzare la risorsa:

```console
az aro list -o table
```

È possibile accedere al `kubeadmin` cluster utilizzando l'utente.  Eseguire il comando seguente per `kubeadmin` trovare la password per l'utente:

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
