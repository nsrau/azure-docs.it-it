---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 5902ba86b51ca1998364e393ac02bbb0d0a23a28
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432635"
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

Se si usa Azure CNI (funzionalità di rete avanzate), il servizio Azure Kubernetes prealloca gli indirizzi IP in base al valore "max-pods" per nodo configurato. Il numero di nodi in un cluster AKS può essere compreso tra 1 e 110. In base al numero massimo di pod per nodo configurato, le dimensioni della subnet devono essere maggiori del prodotto tra il numero di nodi e il numero massimo di pod per nodo. L'equazione di base seguente descrive questo requisito:

Dimensioni della subnet > numero di nodi del cluster (prendendo in considerazione i requisiti di scalabilità futuri) * numero massimo di pod per nodo.

Per altre informazioni, vedere [Pianificare l'indirizzamento IP per il cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

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

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Viene visualizzato "errore dal server: errore nella composizione back-end: comporre tcp...". Cosa devo fare?

Assicurarsi che il gruppo di sicurezza di rete predefinito non è stato modificato e che la porta 22 sia aperta per la connessione al server API. Controllare se il `tunnelfront` pod in esecuzione nel *kube system* dello spazio dei nomi usando il `kubectl get pods --namespace kube-system` comando. In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Sto cercando di eseguire l'aggiornamento o il ridimensionamento e ricevo un "messaggio: La modifica della proprietà 'imageReference' non è consentita". Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster AKS. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Si verificano errori che il cluster non è in stato di errore e l'aggiornamento o ridimensionamento non funzioneranno finché non si è risolto

*Questo tipo di servizio di risoluzione dei problemi viene indirizzato da https://aka.ms/aks-cluster-failed*

Questo errore si verifica quando i cluster di entrano in uno stato non riuscito per diversi motivi. Attenersi alla procedura seguente per risolvere lo stato del cluster non riuscito prima di ritentare l'operazione non riuscita in precedenza:

1. Fino a quando il cluster si trova fuori `failed` stato `upgrade` e `scale` operazioni non riescono. Le risoluzioni e i problemi radice comuni includono:
    * Scalandola **quota di calcolo sufficienti (CRP)**. Per risolvere, prima di tutto ridimensionare il cluster tornato a uno stato stabile obiettivo entro la quota. Quindi seguire queste [aumenta la seguente procedura per richiedere una quota di calcolo](../azure-supportability/resource-manager-core-quotas-request.md) prima di provare ad aumentare le prestazioni oltre nuovamente i limiti di quota iniziale.
    * Ridimensionamento di un cluster con una rete avanzata e **le risorse della subnet insufficiente (rete)**. Per risolvere, prima di tutto ridimensionare il cluster tornato a uno stato stabile obiettivo entro la quota. Segui [aumenta la procedura seguente per richiedere una quota di risorse](../azure-resource-manager/resource-manager-quota-errors.md#solution) prima di provare ad aumentare le prestazioni oltre nuovamente i limiti di quota iniziale.
2. Dopo avere risolta la causa principale dell'errore di aggiornamento, il cluster deve essere nello stato riuscito. Una volta verificato uno stato completato, ripetere l'operazione originale.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Si verificano errori durante il tentativo di eseguire l'aggiornamento o la scala che indicano il cluster è in corso attualmente in corso aggiornato o dispone di aggiornamento non riuscito

*Questo tipo di servizio di risoluzione dei problemi viene indirizzato da https://aka.ms/aks-pending-upgrade*

Le operazioni del cluster sono limitate quando si verificano operazioni di aggiornamento attive o è stato tentato un aggiornamento, ma successivamente non è riuscito. Per diagnosticare il problema eseguire `az aks show -g myResourceGroup -n myAKSCluster -o table` per recuperare informazioni dettagliate sullo stato nel cluster. In base al risultato:

* Se è attiva l'aggiornamento di cluster, attendere finché non termina l'operazione. Se la connessione riesce, ripetere l'operazione non riuscita in precedenza.
* Se cluster ha esito negativo di aggiornamento, seguire i passaggi descritti in precedenza
