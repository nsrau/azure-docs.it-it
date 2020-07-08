---
title: Usare identità gestite in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/30/2020
ms.author: mlearned
ms.openlocfilehash: 30d1290f9eb7b2750f09e5e256d4dd212c7e4607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610286"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare identità gestite in Azure Kubernetes Service

Attualmente, un cluster Azure Kubernetes Service (AKS) (in particolare, il provider di cloud Kubernetes) richiede un'identità per creare risorse aggiuntive, ad esempio i servizi di bilanciamento del carico e i dischi gestiti in Azure. Questa identità può essere un' *identità gestita* o un' *entità servizio*. Se si usa un' [entità servizio](kubernetes-service-principal.md), è necessario specificare una o AKS per crearne una per conto dell'utente. Se si usa l'identità gestita, questa verrà creata automaticamente da AKS. I cluster che usano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per tenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più facile usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio sia per le identità gestite.

Le *identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. La rotazione delle credenziali per MI viene eseguita automaticamente ogni 46 giorni in base Azure Active Directory valore predefinito. AKS USA sia i tipi di identità gestiti assegnati dal sistema che quelli assegnati dall'utente. Queste identità non sono attualmente modificabili. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata la seguente risorsa:

- Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva

## <a name="limitations"></a>Limitazioni

* L'uso delle identità gestite non è attualmente supportato.
* I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster.
* Non è possibile aggiornare o aggiornare i cluster AKS esistenti per abilitare le identità gestite.
* Durante le operazioni di **aggiornamento** del cluster, l'identità gestita è temporaneamente non disponibile.

## <a name="summary-of-managed-identities"></a>Riepilogo delle identità gestite

AKS usa diverse identità gestite per i servizi e i componenti aggiuntivi predefiniti.

| Identità                       | Nome    | Caso d'uso | Autorizzazioni predefinite | Porta la tua identità
|----------------------------|-----------|----------|
| Piano di controllo | non visibile | Usato da AKS per gestire le risorse di rete, ad esempio creare un servizio di bilanciamento del carico per il traffico in ingresso, l'indirizzo IP pubblico e così via.| Ruolo Collaboratore per il gruppo di risorse nodo | Attualmente non supportato
| Kubelet | Nome del cluster AKS-agentpool | Autenticazione con Container Registry di Azure (ACR) | Ruolo lettore per il gruppo di risorse nodo | Attualmente non supportato
| Componente aggiuntivo | AzureNPM | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Monitoraggio della rete AzureCNI | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | azurepolicy (Gatekeeper) | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | azurepolicy | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Calico | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | Dashboard | Non è richiesta alcuna identità | ND | No
| Componente aggiuntivo | HTTPApplicationRouting | Gestisce le risorse di rete necessarie | Ruolo Reader per il gruppo di risorse nodo, ruolo Collaboratore per la zona DNS | No
| Componente aggiuntivo | Gateway applicazione in ingresso | Gestisce le risorse di rete necessarie| Ruolo Collaboratore per il gruppo di risorse nodo | No
| Componente aggiuntivo | omsagent | Usato per inviare metriche AKS a monitoraggio di Azure | Monitoraggio del ruolo server di pubblicazione metrica | No
| Componente aggiuntivo | Nodo virtuale (ACIConnector) | Gestisce le risorse di rete necessarie per istanze di contenitore di Azure | Ruolo Collaboratore per il gruppo di risorse nodo | No


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

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Usare il comando seguente per eseguire una query su ObjectID dell'identità gestita del piano di controllo:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Il risultato dovrebbe essere simile al seguente:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Per creare e usare il proprio VNet, un indirizzo IP statico o un disco di Azure collegato dove le risorse si trovano all'esterno del gruppo di risorse del nodo di lavoro, usare il PrincipalID dell'identità gestita assegnata dal sistema cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [delegare l'accesso ad altre risorse di Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Le concessioni di autorizzazioni per l'identità gestita del cluster usata dal provider di servizi cloud di Azure possono richiedere fino a 60 minuti.

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Il cluster verrà creato in pochi minuti. È quindi possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso esattamente come è stato fatto con i cluster AKS basati su entità servizio.

## <a name="next-steps"></a>Passaggi successivi
* Usare i [modelli di Azure Resource Manager (ARM)][aks-arm-template] per creare cluster abilitati per l'identità gestita.

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters
