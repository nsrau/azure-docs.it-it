---
title: Usare identità gestite in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 20e255958cbd90aaddf060e42d7627c1e1ebec88
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371461"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare identità gestite in Azure Kubernetes Service

Attualmente, un cluster Azure Kubernetes Service (AKS) (in particolare, il provider di cloud Kubernetes) richiede un'identità per creare risorse aggiuntive, ad esempio i servizi di bilanciamento del carico e i dischi gestiti in Azure. Questa identità può essere un' *identità gestita* o un' *entità servizio*. Se si usa un' [entità servizio](kubernetes-service-principal.md), è necessario specificare una o AKS per crearne una per conto dell'utente. Se si usa l'identità gestita, questa verrà creata automaticamente da AKS. I cluster che usano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per tenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più facile usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio sia per le identità gestite.

Le *identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. La rotazione delle credenziali per MI viene eseguita automaticamente ogni 46 giorni in base Azure Active Directory valore predefinito. AKS USA sia i tipi di identità gestiti assegnati dal sistema che quelli assegnati dall'utente. Queste identità non sono attualmente modificabili. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata la seguente risorsa:

- INTERFACCIA della riga di comando di Azure, versione 2.8.0 o successiva

## <a name="limitations"></a>Limitazioni

* I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster.
* Non è possibile eseguire la migrazione dei cluster AKS esistenti alle identità gestite.
* Durante le operazioni di **aggiornamento** del cluster, l'identità gestita è temporaneamente non disponibile.
* Lo spostamento/migrazione dei tenant di cluster abilitati per identità gestite non è supportato.
* Se il cluster ha `aad-pod-identity` abilitato, i pod di identità gestita del nodo modificano i iptables dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che tutte le richieste effettuate all'endpoint dei metadati vengono intercettate da NMI anche se il Pod non lo usa `aad-pod-identity` . AzurePodIdentityException CRD può essere configurato per informare `aad-pod-identity` che qualsiasi richiesta all'endpoint di metadati originata da un pod che corrisponde alle etichette definite in CRD deve essere inoltrata senza alcuna elaborazione in NMI. I pod di sistema con `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ devono essere esclusi in `aad-pod-identity` tramite la configurazione di AzurePodIdentityException CRD. Per altre informazioni, vedere [disabilitare AAD-Pod-Identity per un pod o un'applicazione specifica](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Per configurare un'eccezione, installare la [YAML di eccezione MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Riepilogo delle identità gestite

AKS usa diverse identità gestite per i servizi e i componenti aggiuntivi predefiniti.

| Identità                       | Name    | Caso d'uso | Autorizzazioni predefinite | Porta la tua identità
|----------------------------|-----------|----------|
| Piano di controllo | non visibile | Usato da AKS per le risorse di rete gestite, inclusi i bilanciamenti del carico in ingresso e gli indirizzi IP pubblici gestiti da AKS | Ruolo Collaboratore per il gruppo di risorse nodo | Anteprima
| Kubelet | Nome del cluster AKS-agentpool | Autenticazione con Container Registry di Azure (ACR) | NA (per kubernetes v 1.15 +) | Attualmente non supportato
| Componente aggiuntivo | AzureNPM | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Monitoraggio della rete AzureCNI | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | azurepolicy (Gatekeeper) | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | azurepolicy | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Calico | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Dashboard | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | HTTPApplicationRouting | Gestisce le risorse di rete necessarie | Ruolo Reader per il gruppo di risorse nodo, ruolo Collaboratore per la zona DNS | No
| Componente aggiuntivo | Gateway applicazione in ingresso | Gestisce le risorse di rete necessarie| Ruolo Collaboratore per il gruppo di risorse nodo | No
| Componente aggiuntivo | omsagent | Usato per inviare metriche AKS a monitoraggio di Azure | Monitoraggio del ruolo server di pubblicazione metrica | No
| Componente aggiuntivo | Virtual-Node (ACIConnector) | Gestisce le risorse di rete necessarie per istanze di contenitore di Azure | Ruolo Collaboratore per il gruppo di risorse nodo | No
| Progetto OSS | AAD-Pod-identità | Consente alle applicazioni di accedere in modo sicuro alle risorse cloud con Azure Active Directory (AAD) | ND | Passaggi per concedere l'autorizzazione in https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Creare un cluster AKS con identità gestite

È ora possibile creare un cluster AKS con identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Creare prima di tutto un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Quindi, creare un cluster AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

La creazione di un cluster con identità gestite contiene le informazioni sul profilo dell'entità servizio seguenti:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Usare il comando seguente per eseguire una query su ObjectID dell'identità gestita del piano di controllo:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Il risultato dovrebbe essere simile al seguente:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Una volta creato il cluster, è possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso come è stato fatto con i cluster AKS basati su entità servizio.

> [!NOTE]
> Per creare e usare il proprio VNet, un indirizzo IP statico o un disco di Azure collegato dove le risorse si trovano all'esterno del gruppo di risorse del nodo di lavoro, usare il PrincipalID dell'identità gestita assegnata dal sistema cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [delegare l'accesso ad altre risorse di Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Le concessioni di autorizzazioni per l'identità gestita del cluster usata dal provider di servizi cloud di Azure possono richiedere fino a 60 minuti.

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Usa il tuo piano di controllo (anteprima)
Un'identità del piano di controllo personalizzato consente di concedere l'accesso all'identità esistente prima della creazione del cluster. Questo consente scenari come l'uso di un VNET personalizzato o outboundType di UDR con un'identità gestita.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Devono essere installate le risorse seguenti:
- INTERFACCIA della riga di comando di Azure, versione 2.9.0 o successiva
- Estensione 0.4.57 AKS-Preview

Limitazioni per il piano di controllo Bring Your Own (anteprima):
* Azure Government attualmente non è supportato.
* Azure Cina 21Vianet attualmente non è supportato.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Se non si ha ancora un'identità gestita, è necessario crearne una, ad esempio usando [AZ Identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Il risultato dovrebbe essere simile al seguente:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se l'identità gestita fa parte della sottoscrizione, è possibile usare il [comando AZ Identity CLI][az-identity-list] per eseguire una query su di essa.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

A questo punto è possibile usare il comando seguente per creare il cluster con l'identità esistente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

La creazione di un cluster con le proprie identità gestite contiene le informazioni sul profilo userAssignedIdentities:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Passaggi successivi
* Usare i [modelli di Azure Resource Manager (ARM) ][aks-arm-template] per creare cluster abilitati per l'identità gestita.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
