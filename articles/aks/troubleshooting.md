---
title: Risolvere i problemi comuni del servizio Azure Kubernetes
description: Informazioni su come individuare e risolvere i problemi comuni quando si usa il servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 6ff273236f9f8465de9ec0cda89ed3ff8996ecec
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932656"
---
# <a name="aks-troubleshooting"></a>Risoluzione dei problemi di servizio Azure Kubernetes

Quando si creano o gestiscono cluster del servizio Azure Kubernetes (AKS), in alcuni casi potrebbero verificarsi problemi. In questo articolo sono descritti alcuni problemi comuni e i passaggi per risolverli.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In generale, dove è possibile trovare informazioni sul debug di problemi di Kubernetes?

Consultare la [guida ufficiale per la risoluzione dei problemi dei cluster di Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
È disponibile anche una [guida alla risoluzione dei problemi](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) pubblicata da un tecnico Microsoft per la risoluzione dei problemi relativi a pod, nodi, cluster e altre funzionalità.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Durante la creazione o l’aggiornamento, viene visualizzato un errore di tipo “quota superata”. Cosa devo fare? 

È necessario [richiedere i core](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual è l'impostazione del numero massimo di pod per nodo per il servizio Azure Kubernetes?

Il numero massimo di pod per nodo è 30 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nel portale di Azure.
Il numero massimo di pod per nodo è 110 per impostazione predefinita se si distribuisce un cluster del servizio Azure Kubernetes nell’interfaccia della riga di comando di Azure. Assicurarsi di usare la versione più recente dell’interfaccia della riga di comando di Azure. Questa impostazione predefinita può essere modificata usando il flag `–-max-pods` nel comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Viene visualizzato l'errore "insufficientSubnetSize" durante la distribuzione di un cluster del servizio Azure Kubernetes con funzionalità di rete avanzate. Cosa devo fare?

Se si usa Azure CNI (funzionalità di rete avanzate), il servizio Azure Kubernetes prealloca gli indirizzi IP in base al valore "max-pods" per nodo configurato. Il numero di nodi in un cluster del servizio Azure Kubernetes può essere compreso tra 1 e 110. In base al numero massimo di pod per nodo configurato, le dimensioni della subnet devono essere maggiori del prodotto tra il numero di nodi e il numero massimo di pod per nodo. L'equazione di base seguente descrive questo requisito:

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

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Non è possibile ottenere i log usando i log di kubectl o non è possibile connettersi al server API. Ricevo un messaggio di errore dal server: errore durante la connessione del back-end: Dial TCP... ". Cosa devo fare?

Verificare che il gruppo di sicurezza di rete predefinito non sia stato modificato e che sia la porta 22 che la 9000 siano aperte per la connessione al server API. Controllare se il `tunnelfront` Pod è in esecuzione nello spazio dei nomi *Kube-System* usando il `kubectl get pods --namespace kube-system` comando. In caso contrario, forzare l’eliminazione del pod per riavviarlo.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Sto cercando di eseguire l'aggiornamento o il ridimensionamento e ricevo un "messaggio: La modifica della proprietà 'imageReference' non è consentita". Come si risolve il problema?

È possibile che questo errore venga visualizzato in seguito alla modifica di tag nei nodi dell'agente all'interno del cluster del servizio Azure Kubernetes. La modifica e l'eliminazione di tag e altre proprietà delle risorse nel gruppo di risorse MC_* può causare risultati imprevisti. La modifica delle risorse nel gruppo MC_* del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Si ricevono errori che il cluster è in stato di errore e l'aggiornamento o il ridimensionamento non funziona finché non viene risolto

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-cluster-failed*

Questo errore si verifica quando i cluster entrano in uno stato di errore per diversi motivi. Attenersi alla procedura seguente per risolvere lo stato di errore del cluster prima di riprovare a eseguire l'operazione precedentemente non riuscita:

1. Fino a quando il cluster non `failed` è in `upgrade` stato `scale` di stato e le operazioni non riusciranno. I problemi e le soluzioni principali comuni includono:
    * Ridimensionamento con **quota di calcolo (CRP) insufficiente**. Per risolvere il cluster, ridimensionare prima di tutto il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi questa [procedura per richiedere un aumento della quota di calcolo](../azure-supportability/resource-manager-core-quotas-request.md) prima di riprovare a eseguire la scalabilità verticale oltre i limiti di quota iniziali.
    * Ridimensionamento di un cluster con rete avanzata e **risorse insufficienti per la subnet (rete)** . Per risolvere il cluster, ridimensionare prima di tutto il cluster fino a uno stato di obiettivo stabile entro la quota. Seguire quindi [questa procedura per richiedere un aumento della quota di risorse](../azure-resource-manager/resource-manager-quota-errors.md#solution) prima di provare a eseguire la scalabilità verticale oltre i limiti iniziali della quota.
2. Una volta risolta la cause sottostante dell'errore di aggiornamento, il cluster deve essere in uno stato di esito positivo. Una volta verificato uno stato di esito positivo, ripetere l'operazione originale.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Si sono verificati errori durante il tentativo di aggiornare o ridimensionare lo stato in corso di aggiornamento del cluster o l'aggiornamento non è riuscito

*Questa assistenza per la risoluzione dei problemi è diretta da https://aka.ms/aks-pending-upgrade*

Le operazioni di aggiornamento e ridimensionamento in un cluster con un pool a nodo singolo o un cluster con [più pool di nodi](use-multiple-node-pools.md) si escludono a vicenda. Non è possibile avere un cluster o un pool di nodi simultaneamente per l'aggiornamento e la scalabilità. Al contrario, ogni tipo di operazione deve essere completato sulla risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Di conseguenza, le operazioni sono limitate quando le operazioni di aggiornamento o ridimensionamento attive vengono eseguite o tentate e successivamente non riuscite. 

Per facilitare la diagnosi dell'esecuzione `az aks show -g myResourceGroup -n myAKSCluster -o table` del problema per recuperare lo stato dettagliato del cluster. In base al risultato:

* Se il cluster sta aggiornando attivamente, attendere che l'operazione venga terminata. In caso di esito positivo, ripetere l'operazione precedentemente non riuscita.
* Se il cluster non è stato aggiornato, seguire i passaggi descritti nella sezione precedente.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>È possibile spostare il cluster in una sottoscrizione o in una sottoscrizione diversa con il cluster in un nuovo tenant?

Se il cluster AKS è stato spostato in una sottoscrizione diversa o nella sottoscrizione proprietaria del cluster a un nuovo tenant, il cluster perderà la funzionalità a causa della perdita di assegnazioni di ruolo e dei diritti di entità servizio. AKS non supporta lo stato di **trasferimento dei cluster tra sottoscrizioni o tenant** a causa del vincolo this.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ricevo errori durante il tentativo di usare le funzionalità che richiedono set di scalabilità di macchine virtuali

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-vmss-enablement*

È possibile che vengano visualizzati errori che indicano che il cluster AKS non si trova in un set di scalabilità di macchine virtuali, come nell'esempio seguente:

**AgentPool ' AgentPool ' ha impostato la scalabilità automatica come abilitato ma non nei set di scalabilità di macchine virtuali**

Per usare funzionalità come il ridimensionamento automatico del cluster o più pool di nodi, è necessario creare cluster AKS che usano i set di scalabilità di macchine virtuali. Gli errori vengono restituiti se si prova a usare funzionalità che dipendono dai set di scalabilità di macchine virtuali e si fa riferimento a un cluster AKS normale di un set di scalabilità di macchine virtuali. Il supporto per i set di scalabilità di macchine virtuali è attualmente disponibile in anteprima in AKS.

Seguire i passaggi *prima di iniziare* nel documento appropriato per registrarsi correttamente per la funzionalità Anteprima del set di scalabilità di macchine virtuali e creare un cluster AKS:

* [Usare il ridimensionamento automatico del cluster](cluster-autoscaler.md)
* [Creare e usare più pool di nodi](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quali restrizioni di denominazione sono applicate per le risorse e i parametri AKS?

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-naming-rules*

Le restrizioni di denominazione sono implementate sia dalla piattaforma Azure che da AKS. Se un nome di risorsa o un parametro interrompe una di queste restrizioni, viene restituito un errore in cui viene chiesto di fornire un input diverso. Si applicano le linee guida di denominazione comuni seguenti:

* Il nome del gruppo di risorse *MC_* AKS combina il nome del gruppo di risorse e il nome della risorsa. La sintassi generata automaticamente di `MC_resourceGroupName_resourceName_AzureRegion` non deve essere maggiore di 80 caratteri. Se necessario, ridurre la lunghezza del nome del gruppo di risorse o del cluster AKS.
* Il *dnsPrefix* deve iniziare e terminare con valori alfanumerici. I caratteri validi includono valori alfanumerici e trattini (-). Il *dnsPrefix* non può includere caratteri speciali, ad esempio un punto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Si ricevono errori durante il tentativo di creare, aggiornare, ridimensionare, eliminare o aggiornare il cluster. tale operazione non è consentita perché è in corso un'altra operazione.

*Questa assistenza per la risoluzione dei problemi è diretta da aka.ms/aks-pending-operation*

Le operazioni del cluster sono limitate quando è ancora in corso un'operazione precedente. Per recuperare uno stato dettagliato del cluster, usare il `az aks show -g myResourceGroup -n myAKSCluster -o table` comando. Usare il proprio gruppo di risorse e il nome del cluster AKS in base alle esigenze.

In base all'output dello stato del cluster:

* Se il cluster si trova in uno stato di provisioning diverso da *succeeded* o *failed*, attendere che venga terminata l'operazione (*aggiornamento/aggiornamento/creazione/ridimensionamento/eliminazione/migrazione*). Al termine dell'operazione precedente, riprovare a eseguire l'operazione più recente del cluster.

* Se si verifica un errore di aggiornamento del cluster, attenersi alla procedura descritta [per la ricezione di errori nel caso in cui il cluster si trova in stato di errore e l'aggiornamento o il ridimensionamento non funzionerà fino a quando non sarà stato risolto](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Si ricevono errori che non sono stati trovati dall'entità servizio quando si tenta di creare un nuovo cluster senza passarne uno esistente.

Quando si crea un cluster AKS, è necessario che un'entità servizio crei risorse per conto dell'utente. AKS offre la possibilità di crearne uno nuovo in fase di creazione del cluster, ma ciò richiede che Azure Active Directory propaghi completamente la nuova entità servizio in un tempo ragionevole per consentire la creazione del cluster. Quando questa propagazione richiede troppo tempo, la convalida del cluster non riuscirà a creare perché non è possibile trovare un'entità servizio disponibile a tale scopo. 

Usare le soluzioni alternative seguenti:
1. Usare un'entità servizio esistente che è già stata propagata tra le aree ed esiste per passare ad AKS al momento della creazione del cluster.
2. Se si usano gli script di automazione, aggiungere i ritardi di tempo tra la creazione dell'entità servizio e la creazione del cluster AKS.
3. Se si usa portale di Azure, tornare alle impostazioni del cluster durante la creazione e ripetere la pagina di convalida dopo alcuni minuti.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Si ricevono errori dopo la limitazione del traffico in uscita

Quando si limita il traffico in uscita da un cluster AKS, sono disponibili le porte in uscita e le regole di rete [consigliate e facoltative](limit-egress-traffic.md) e le regole di dominio/FQDN/applicazione per AKS. Se le impostazioni sono in conflitto con una di queste regole, potrebbe non essere possibile eseguire determinati `kubectl` comandi. È anche possibile che vengano visualizzati errori durante la creazione di un cluster AKS.

Verificare che le impostazioni non siano in conflitto con le porte in uscita, le regole di rete e le regole di dominio FQDN/applicazione consigliate o facoltative.
