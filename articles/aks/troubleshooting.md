---
title: Risolvere i problemi comuni del servizio Kubernetes di Azure
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Kubernetes di Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 9f082c5f198ebd7123058bd250d3fef55494d553
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287540"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di AKS
Quando si creano o gestiscono cluster AKS, possono occasionalmente verificarsi problemi. Questo articolo illustra alcuni problemi comuni e i passaggi per la risoluzione dei problemi.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

[Qui] (https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) è un collegamento ufficiale per la risoluzione dei problemi dei cluster di Kubernetes.
[Questo](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) è un collegamento a una guida di risoluzione dei problemi pubblicata da un tecnico Microsoft per la risoluzione dei problemi di pod, nodi, cluster e così via.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Come si procede se viene visualizzato un errore di quota superata Cosa devo fare? 

È necessario richiedere i core [qui](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per AKS?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster AKS nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster AKS nell'interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell'interfaccia della riga di comando di Azure. Questa impostazione predefinita può essere modificata usando il flag –max-nodes-per-pod nel comando az aks create.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster AKS con funzionalità di rete avanzate. Cosa devo fare?

Per l'opzione di rete virtuale personalizzata selezionata per la rete durante le operazioni di creazione di AKS, viene usata l'interfaccia di rete dei contenitori di Azure per IPAM. Il numero di nodi in un cluster AKS può essere compreso tra 1 e 100. In base al punto 2) sopra, le dimensioni della subnet devono essere maggiori del prodotto del numero di nodi e del numero massimo di pod per nodo: dimensioni subnet > numero di nodi nel cluster * numero massimo di pod per nodo.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Il pod è bloccato in modalità 'CrashLoopBackOff'. Cosa devo fare?

Potrebbero esistere vari motivi per cui il modo rimane bloccato in tale modalità. È possibile esaminare: 
* Il pod stesso con `kubectl describe pod <pod-name>`
* I log con `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Qual è la procedura per abilitare RBAC in un cluster esistente?

Purtroppo, l'abilitazione di RBAC nei cluster esistenti non è supportata al momento. È necessario creare in modo esplicito nuovi cluster. Se si usa l'interfaccia della riga di comando, RBAC viene abilitato per impostazione predefinita, mentre è disponibile un pulsante interruttore per abilitarlo nella pagina per la creazione del flusso di lavoro del portale AKS.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>È stato creato un cluster tramite l'interfaccia della riga di comando di Azure con le impostazioni predefinite o tramite il portale di Azure con RBAC abilitato e compaiono numerosi avvisi nel dashboard di Kubernetes. Il dashboard in precedenza non generava avvisi. Cosa devo fare?

Il motivo per cui vengono visualizzati avvisi nel dashboard è che ora è abilitato con RBAC e l'accesso al dashboard è stato disabilitato per impostazione predefinita. In generale, questo approccio è considerato una procedura appropriata perché l'esposizione predefinita del dashboard a tutti gli utenti del cluster può comportare rischi per la sicurezza. Se si vuole comunque abilitare il dashboard, seguire le indicazioni in questo [blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) per abilitarlo.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Non è possibile connettersi al dashboard. Cosa devo fare?

Il modo più semplice per accedere al servizio all'esterno del cluster consiste nell'eseguire kubectl proxy, per inoltrare le richieste a localhost sulla porta 8001 al server API Kubernetes. Da qui, apiserver può fungere da proxy per il servizio: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default

Se non viene visualizzato il dashboard di Kubernetes, controllare se il pod kube-proxy è in esecuzione nello spazio dei nomi kube system. Se non è esecuzione, eliminare il pod in modo che venga riavviato.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log tramite kubectl logs oppure non è possibile connettersi al server API e viene visualizzato l'errore "Error from server: error dialing backend: dial tcp…" (Errore dal server: errore durante il contatto con il backend: contattare tcp...). Cosa devo fare?

Assicurarsi che il gruppo di sicurezza di rete predefinito non sia modificato e che la porta 22 sia aperta per la connessione al server API. Controllare se il pod tunnelfront è in esecuzione nello spazio dei nomi kube system. In caso contrario, forzarne l'eliminazione in modo che venga riavviato.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>Durante un tentativo di aggiornamento o ridimensionamento viene visualizzato il messaggio: "La modifica della proprietà 'imageReference' non è consentita." Errore.  Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster AKS. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. Modificare le risorse in MC_* nel cluster AKS è una violazione dell'obiettivo del livello di servizio (SLO).


