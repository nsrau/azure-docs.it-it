---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: fd3d1c464c6f2d4cbecd715db0689581ca141769
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654071"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di servizio Azure Kubernetes

Quando si creano o gestiscono cluster del servizio Azure Kubernetes (AKS), in alcuni casi potrebbero verificarsi problemi. In questo articolo sono descritti alcuni problemi comuni e i passaggi per risolverli.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

Consultare la [guida ufficiale per la risoluzione dei problemi dei cluster di Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
È disponibile anche una [guida alla risoluzione dei problemi](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) pubblicata da un tecnico Microsoft per la risoluzione dei problemi relativi a pod, nodi, cluster e altre funzionalità.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Durante la creazione o l’aggiornamento, viene visualizzato un errore di tipo “quota superata”. Cosa devo fare? 

È necessario [richiedere i core](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per AKS?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster AKS nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster AKS nell’interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell’interfaccia della riga di comando di Azure. Questa impostazione predefinita può essere modificata usando il flag `–-max-pods` nel comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster AKS con funzionalità di rete avanzate. Cosa devo fare?

Nell'opzione personalizzata di Rete virtuale di Azure per la rete durante la creazione del servizio AKS, l'interfaccia di rete contenitore di Azure (CNI) viene usata per gestire gli indirizzi IP (IPAM). Il numero di nodi in un cluster servizio Azure Kubernetes può essere compreso tra 1 e 100. In base alla sezione precedente, le dimensioni della subnet devono essere maggiori del prodotto tra il numero di nodi e il numero massimo di pod per nodo. La relazione può essere espressa in questo modo: dimensioni della subnet > numero di nodi nel cluster * numero massimo di pod per nodo.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Il pod è bloccato in modalità “CrashLoopBackOff”. Cosa devo fare?

Potrebbero esistere vari motivi per cui il modo rimane bloccato in tale modalità. Potrebbe essere opportuno verificare:

* Il pod stesso con `kubectl describe pod <pod-name>`.
* I log con `kubectl log <pod-name>`.

Per altre informazioni su come risolvere i problemi di pod, vedere [Debug delle applicazioni](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Qual è la procedura per abilitare RBAC in un cluster esistente?

Purtroppo, l'abilitazione del controllo degli accessi in base al ruolo (RBAC) nei cluster esistenti non è al momento supportata. È necessario creare in modo esplicito nuovi cluster. Se si usa l'interfaccia della riga di comando, RBAC è abilitato per impostazione predefinita. Se si usa il portale del servizio Azure Kubernetes, nel flusso di lavoro di creazione è disponibile un pulsante che consente di abilitare RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>È stato creato un cluster con RBAC abilitato tramite l'interfaccia della riga di comando di Azure con le impostazioni predefinite o il portale di Azure e nel dashboard di Kubernetes vengono visualizzati diversi avvisi. Il dashboard non generava avvisi. Cosa devo fare?

Il motivo per cui vengono visualizzati avvisi nel dashboard è che ora il cluster è abilitato con RBAC e l'accesso è stato disabilitato per impostazione predefinita. In generale, questo approccio è considerato una procedura appropriata perché l'esposizione predefinita del dashboard a tutti gli utenti del cluster può comportare rischi per la sicurezza. Se si vuole comunque abilitare il dashboard, seguire le indicazioni riportate in questo [post di blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Non è possibile connettersi al dashboard. Cosa devo fare?

Il modo più semplice per accedere al servizio all'esterno del cluster consiste nell'eseguire `kubectl proxy`, per inoltrare le richieste inviate a localhost sulla porta 8001 al server API Kubernetes. Da qui, il server API può usare un proxy per il servizio: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se non viene visualizzato il dashboard di Kubernetes, controllare se il pod `kube-proxy` è eseguito nello spazio dei nomi `kube-system`. Se non è esecuzione, eliminare il pod in modo che venga riavviato.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Viene visualizzato l’errore “Errore del server: errore durante il contatto con il backend: contattare tcp…” Cosa devo fare?

Assicurarsi che il gruppo di sicurezza di rete predefinito (NSG) non sia stato modificato e che la porta 22 sia aperta per la connessione al server API. Controllare se il pod `tunnelfront` è in esecuzione nello spazio dei nomi `kube-system`. In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>Sto cercando di eseguire l'aggiornamento o il ridimensionamento e ricevo un "messaggio: La modifica della proprietà 'imageReference' non è consentita".  Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster AKS. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Come posso rinnovare il segreto dell'entità servizio nel cluster del servizio Azure Kubernetes?

Per impostazione predefinita, i cluster AKS vengono creati con un'entità servizio che dispone di una data di scadenza di un anno. Se la data di scadenza è prossima, è possibile reimpostare le credenziali per estendere l'entità servizio per un ulteriore periodo di tempo.

L'esempio segue questa procedura:

1. Ottiene l'ID dell'entità servizio del cluster tramite il comando [az aks show](/cli/azure/aks#az-aks-show).
1. Elenca il segreto client dell'entità servizio tramite l'[elenco di credenziali di az ad sp](/cli/azure/ad/sp/credential#az-ad-sp-credential-list).
1. Estende l'entità servizio per un altro anno tramite il comando [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). Il segreto client dell'entità servizio deve rimanere invariato per eseguire correttamente il cluster del servizio Azure Kubernetes.

```azurecli
# Get the service principal ID of your AKS cluster.
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret.
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for one year.
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
