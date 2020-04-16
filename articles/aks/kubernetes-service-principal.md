---
title: Entità servizio per servizio Azure Kubernetes
description: Creare e gestire un'entità servizio di Azure Active Directory per un cluster nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 2c792eb4dc060e3f5d7fa2d8f2176bdd51538c43
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392731"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entità servizio con il servizio Azure Kubernetes

Per interagire con le API di Azure, un cluster AKS richiede [un'entità servizio di Azure Active Directory (AD)][aad-service-principal] o un'identità [gestita.](use-managed-identity.md) Un'entità servizio o un'identità gestita è necessaria per creare e gestire dinamicamente altre risorse di Azure, ad esempio un servizio di bilanciamento del carico di Azure o un registro del contenitore (ACR).

Questo articolo illustra come creare e usare un'entità servizio per i cluster servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Per creare un'entità servizio di Azure AD, sono necessarie le autorizzazioni per registrare un'applicazione con il tenant di Azure AD e per assegnare l'applicazione a un ruolo nella sottoscrizione. In mancanza di queste autorizzazioni, potrebbe essere necessario chiedere all'amministratore di Azure AD o della sottoscrizione di assegnarle oppure creare in anticipo un'entità servizio da usare con il cluster servizio Azure Kubernetes.

Se si usa un'entità servizio da un tenant di Azure AD diverso, esistono considerazioni aggiuntive sulle autorizzazioni disponibili quando si distribuisce il cluster. È possibile che non si disponga delle autorizzazioni appropriate per leggere e scrivere informazioni sulla directory. Per altre informazioni, vedere [Quali sono le autorizzazioni utente predefinite in Azure Active Directory?][azure-ad-permissions]

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Creare e usare un'entità servizio automaticamente

Quando si crea un cluster servizio Azure Kubernetes nel portale di Azure o tramite il comando [az servizio Azure Kubernetes create][az-aks-create], Azure può generare automaticamente un'entità servizio.

Nell'esempio seguente di interfaccia della riga di comando di Azure non è specificata un'entità servizio. In questo scenario l'interfaccia della riga di comando di Azure crea un'entità servizio per il cluster servizio Azure Kubernetes. Per completare correttamente questa operazione, l'account di Azure deve avere i diritti appropriati per la creazione di un'entità servizio.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Creare manualmente un'entità servizio

Per creare manualmente un'entità servizio con l'interfaccia della riga di comando di Azure, usare il comando [az ad sp create-for-rbac][az-ad-sp-create]. Nell'esempio seguente il parametro `--skip-assignment` impedisce il completamento di qualsiasi assegnazione predefinita aggiuntiva:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

L'output è simile all'esempio seguente: Prendere nota dei propri valori `appId` e `password`. Questi valori serviranno per creare il cluster servizio Azure Kubernetes nella sezione successiva.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Specificare un'entità servizio per un cluster servizio Azure Kubernetes

Per usare un'entità servizio esistente quando si crea un cluster servizio Azure Kubernetes con il comando [az servizio Azure Kubernetes create][az-aks-create], utilizzare i parametri `--service-principal` e `--client-secret` per specificare `appId` e `password` dall'output del comando [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Se si usa un'entità servizio esistente con segreto personalizzato, assicurarsi che il segreto non sia più lungo di 190 byte.

Se si distribuisce un cluster servizio Azure Kubernetes mediante il portale di Azure, nella pagina *Authentication* (Autenticazione) della finestra di dialogo **Create Kubernetes cluster** (Crea cluster Kubernetes) scegliere **Configure service principal** (Configura entità servizio). Selezionare **Use existing** (Usa esistente) e specificare i valori seguenti:

- **ID client dell'entità servizio** è l'*appId*
- **Service principal client secret** (Segreto client dell'entità servizio) è il valore *password*

![Immagine del passaggio ad Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegare l'accesso ad altre risorse di Azure

È possibile usare l'entità servizio per il cluster servizio Azure Kubernetes per accedere ad altre risorse. Ad esempio, se si vuole distribuire il cluster AKS in una subnet di rete virtuale di Azure esistente o connettersi al Registro di sistema del contenitore di Azure, è necessario delegare l'accesso a tali risorse all'entità servizio.

Per delegare le autorizzazioni, creare un'assegnazione di ruolo utilizzando il comando [az role assignment create.][az-role-assignment-create] Assegnare `appId` l'oggetto a un ambito specifico, ad esempio un gruppo di risorse o una risorsa di rete virtuale. Un ruolo definisce quindi le autorizzazioni che l'entità servizio possiede nella risorsa, come illustrato nell'esempio seguente:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

L'oggetto `--scope` per una risorsa deve essere un ID risorsa completo, ad esempio */subscriptions/\<guid\>/resourceGroups/myResourceGroup* o */subscriptions/\<guid \>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

Le sezioni seguenti illustrano le deleghe comuni che potrebbe essere necessario creare.

### <a name="azure-container-registry"></a>Registro Azure Container

Se si usa Azure Container Registry (ACR) come archivio immagini del contenitore, è necessario concedere autorizzazioni all'entità servizio per il cluster AKS per leggere ed eseguire il pull delle immagini. Attualmente, la configurazione consigliata consiste nell'utilizzare il comando [az aks create][az-aks-create] o [az aks update][az-aks-update] per l'integrazione con un Registro di sistema e assegnare il ruolo appropriato per l'entità servizio. Per la procedura dettagliata, vedere [Authenticate with Azure Container Registry from Azure Kubernetes Service][aks-to-acr].

### <a name="networking"></a>Rete

È possibile usare le funzionalità di rete avanzate in cui la rete virtuale e la subnet o gli indirizzi IP pubblici sono in un altro gruppo di risorse. Assegnare uno dei seguenti set di autorizzazioni del ruolo:

- Creare un [ruolo personalizzato][rbac-custom-role] e definire le autorizzazioni del ruolo seguenti:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- In alternativa, assegnare il ruolo predefinito [Collaboratore rete][rbac-network-contributor] nella subnet all'interno della rete virtuale

### <a name="storage"></a>Archiviazione

Potrebbe essere necessario accedere alle risorse esistenti del disco in un altro gruppo di risorse. Assegnare uno dei seguenti set di autorizzazioni del ruolo:

- Creare un [ruolo personalizzato][rbac-custom-role] e definire le autorizzazioni del ruolo seguenti:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- In alternativa, assegnare il ruolo predefinito [Collaboratore account di archiviazione][rbac-storage-contributor] nel gruppo di risorse

### <a name="azure-container-instances"></a>Istanze di Azure Container

Se si usa Virtual Kubelet per l’integrazione con il servizio Azure Kubernetes e si sceglie di eseguire Istanze di Azure Container nel gruppo di risorse separato dal cluster del servizio Azure Kubernetes, è necessario concedere all'entità servizio Azure Kubernetes le autorizzazioni da *collaboratore* per il gruppo di risorse di Istanze di Azure Container.

## <a name="additional-considerations"></a>Altre considerazioni

Quando si usano entità di servizio Azure Kubernetes e di Azure AD, ricordare le considerazioni seguenti.

- L'entità servizio per Kubernetes fa parte della configurazione del cluster. Non usare tuttavia l'identità per distribuire il cluster.
- Per impostazione predefinita, le credenziali dell'entità servizio sono valide per un anno. È possibile [aggiornare o ruotare le credenziali dell'entità servizio][update-credentials] in qualsiasi momento.
- Ogni entità servizio è associata a un'applicazione Azure AD. L'entità servizio per un cluster Kubernetes può essere associata *https://www.contoso.org/example*a qualsiasi nome di applicazione Azure AD valido, ad esempio: L'URL per l'applicazione non deve essere necessariamente un endpoint reale.
- Quando si specifica l'**ID client** dell'entità servizio, usare il valore di `appId`.
- Nelle macchine virtuali del nodo agente nel cluster Kubernetes, le credenziali dell'entità servizio vengono archiviate nel file`/etc/kubernetes/azure.json`
- Quando si usa il comando [az servizio Azure Kubernetes create][az-aks-create] per generare automaticamente l'entità servizio, le credenziali dell'entità servizio vengono scritte nel file `~/.azure/aksServicePrincipal.json` nel computer utilizzato per eseguire il comando.
- Se non si passa in modo specifico un'entità servizio in `~/.azure/aksServicePrincipal.json` comandi dell'interfaccia della riga di comando AKS aggiuntivi, viene usata l'entità servizio predefinita in cui si trova.  
- Facoltativamente, è anche possibile rimuovere il file aksServicePrincipal.json e AKS creerà una nuova entità servizio.
- Quando si elimina un cluster servizio Azure Kubernetes creato da [az servizio Azure Kubernetes create][az-aks-create], l'entità servizio creata automaticamente non viene eliminata.
    - Per eliminare l'entità servizio, eseguire una query per il cluster *servicePrincipalProfile.clientId* e quindi eliminare con [az ad app delete][az-ad-app-delete]. Sostituire il gruppo di risorse e i nomi di cluster seguenti con i propri valori:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Risolvere problemi

The service principal credentials for an AKS cluster are cached by the Azure CLI. Se queste credenziali sono scadute, si verificano errori durante la distribuzione dei cluster AKS. Il seguente messaggio di errore quando si esegue [az aks creare][az-aks-create] può indicare un problema con le credenziali dell'entità servizio memorizzati nella cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Controllare l'età del file delle credenziali utilizzando il comando seguente:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

L'ora di scadenza predefinita per le credenziali dell'entità servizio è di un anno. Se il file *aksServicePrincipal.json* è più vecchio di un anno, eliminare il file e provare a distribuire nuovamente un cluster AKS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle entità servizio di Azure Active Directory, vedere [Oggetti applicazione e entità servizio.][service-principal]

Per informazioni su come aggiornare le credenziali, vedere [Aggiornare o ruotare le credenziali per un'entità servizio in AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
