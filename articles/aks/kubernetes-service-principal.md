---
title: Entità servizio per Azure Kubernetes Service (AKS)
description: Creare e gestire un'entità servizio di Azure Active Directory per un cluster in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394591"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entità servizio con Azure Kubernetes Service (AKS)

Per l'interazione con le API di Azure, un cluster AKS richiede un'[entità servizio di Azure Active Directory (AD)][aad-service-principal]. L'entità servizio è necessaria per creare e gestire in modo dinamico altre risorse quali il bilanciamento del carico o un registro contenitori di Azure.

Questo articolo illustra come creare e usare un'entità servizio per i cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Per creare un'entità servizio di Azure AD, sono necessarie le autorizzazioni per registrare un'applicazione con il tenant di Azure AD e per assegnare l'applicazione a un ruolo nella sottoscrizione. In mancanza di queste autorizzazioni, potrebbe essere necessario chiedere all'amministratore di Azure AD o della sottoscrizione di assegnarle oppure creare in anticipo un'entità servizio da usare con il cluster AKS.

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Creare e usare un'entità servizio automaticamente

Quando si crea un cluster AKS nel portale di Azure o tramite il comando [az aks create][az-aks-create], Azure può generare automaticamente un'entità servizio.

Nell'esempio seguente di interfaccia della riga di comando di Azure non è specificata un'entità servizio. In questo scenario l'interfaccia della riga di comando di Azure crea un'entità servizio per il cluster AKS. Per completare correttamente questa operazione, l'account di Azure deve avere i diritti appropriati per la creazione di un'entità servizio.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Creare manualmente un'entità servizio

Per creare manualmente un'entità servizio con l'interfaccia della riga di comando di Azure, usare il comando [az ad sp create-for-rbac][az-ad-sp-create]. Nell'esempio seguente il parametro `--skip-assignment` impedisce il completamento di qualsiasi assegnazione predefinita aggiuntiva:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output è simile all'esempio seguente: Prendere nota dei propri valori `appId` e `password`. Questi valori serviranno per creare il cluster AKS nella sezione successiva.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Specificare un'entità servizio per un cluster AKS

Per usare un'entità servizio esistente quando si crea un cluster AKS con il comando [az aks create][az-aks-create], utilizzare i parametri `--service-principal` e `--client-secret` per specificare `appId` e `password` dall'output del comando [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Se si distribuisce un cluster AKS mediante il portale di Azure, nella pagina *Authentication* (Autenticazione) della finestra di dialogo **Create Kubernetes cluster** (Crea cluster Kubernetes) scegliere **Configure service principal** (Configura entità servizio). Selezionare **Use existing** (Usa esistente) e specificare i valori seguenti:

- **ID client dell'entità servizio** è l'*appId*
- **Service principal client secret** (Segreto client dell'entità servizio) è il valore *password*

![Immagine del passaggio ad Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>Ulteriori considerazioni

Quando si usano entità di AKS e di Azure AD, ricordare le considerazioni seguenti.

- L'entità servizio per Kubernetes fa parte della configurazione del cluster. Non usare tuttavia l'identità per distribuire il cluster.
- Ogni entità servizio è associata a un'applicazione Azure AD. L'entità servizio per un cluster Kubernetes può essere associata a qualsiasi nome di applicazione Azure AD valido, ad esempio *https://www.contoso.org/example*. L'URL per l'applicazione non deve essere necessariamente un endpoint reale.
- Quando si specifica l'**ID client** dell'entità servizio, usare il valore di `appId`.
- Nelle macchine virtuali master e dei nodi nel cluster Kubernetes le credenziali dell'entità servizio sono archiviate nel file `/etc/kubernetes/azure.json`
- Quando si usa il comando [az aks create][az-aks-create] per generare automaticamente l'entità servizio, le credenziali dell'entità servizio vengono scritte nel file `~/.azure/aksServicePrincipal.json` nel computer utilizzato per eseguire il comando.
- Quando si elimina un cluster AKS creato da [az aks create][az-aks-create], l'entità servizio creata automaticamente non viene eliminata.
    - Per eliminare l'entità di servizio, ottenere prima l'ID dell'entità servizio con [az ad app list][az-ad-app-list]. Nell'esempio seguente viene eseguita una query per il cluster denominato *myAKSCluster*, quindi viene eliminato l'ID dell'app con [az ad app delete][az-ad-app-delete]. Sostituire questi nomi con i valori personalizzati:

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle entità servizio Azure Active Directory, vedere [Oggetti applicazione e oggetti entità servizio][service-principal]

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
