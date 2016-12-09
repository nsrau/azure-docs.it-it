---
title: Distribuire un cluster del servizio contenitore di Azure con Kubernetes | Documentazione Microsoft
description: Distribuire un cluster del servizio contenitore di Azure con Kubernetes
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Motore del servizio contenitore di Microsoft Azure - Procedura dettagliata per Kubernetes

## <a name="deployment"></a>Distribuzione

Ecco la procedura per distribuire un semplice cluster Kubernetes:

1. [Generare la chiave SSH](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [Generare l'entità servizio](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. Fare clic sul pulsante [Distribuisci in Azure nel file README](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) e compilare tutti i campi

## <a name="walkthrough"></a>Procedura dettagliata

Dopo la creazione del cluster Kubernetes, sarà disponibile un gruppo di risorse col contenuto seguente:

1. 1 master accessibile da SSH sulla porta 22 o kubectl sulla porta 443.

2. Set di nodi in un set di disponibilità.  È possibile accedere ai nodi tramite un master.  Per un esempio specifico, vedere [inoltro agente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant).

L'immagine seguente illustra l'architettura di un cluster del servizio contenitore con 1 master e 2 agenti:

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

L'immagine precedente è costituita da queste parti:

1. **Componenti master**: il master esegue l'utilità di pianificazione di Kubernetes, il server API e lo strumento di gestione del controller.  La porta 443 viene esposta per la gestione remota con l'interfaccia della riga di comando di kubectl.
2. **Nodi**: i nodi Kubernetes vengono eseguiti in un set di disponibilità.  I servizi di bilanciamento del carico di Azure vengono aggiunti in modo dinamico al cluster in base ai servizi esposti. 
3. **Componenti comuni**: tutte le macchine virtuali eseguono kubelet, Docker e un proxy.
4. **Rete**: a tutte le macchine virtuali viene assegnato un indirizzo IP nella rete 10.240.0.0/16.  A ogni macchina virtuale viene assegnata una subnet /24 per il rispettivo CIDR di pod, abilitando un indirizzo IP per ogni pod.  Il proxy in esecuzione in ogni macchina virtuale implementa la rete del servizio 10.0.0.0/16.

Tutte le macchine virtuali si trovano nella stessa rete privata virtuale e sono completamente accessibili le une alle altre.

## <a name="create-your-first-kubernetes-service"></a>Creare il primo servizio Kubernetes

La procedura dettagliata illustra come eseguire queste operazioni:
 * Accedere a un cluster Kubernetes tramite SSH
 * Distribuire una semplice applicazione Docker ed esporla a tutti gli utenti
 * Posizione del file di configurazione Kube e informazioni su come accedere in modalità remota al cluster Kubernetes
 * Usare `kubectl exec` per eseguire comandi in un contenitore 
 * Accedere al dashboard Kubernetes

1. Dopo avere distribuito il modello, annotare i nomi di dominio completi del master.
   1. Se si usa Powershell o l'interfaccia della riga di comando, il parametro di output si trova nella sezione OutputsString denominata 'masterFQDN'
   2. Se si usa il portale, passare al pannello Panoramica della risorsa ContainerService per copiare il valore "masterFQDN":
     
   ![Immagine della scalabilità di Docker](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. SSH per il nome di dominio completo master ottenuto nel Passaggio 1.

3. Esplorare i nodi e i pod in esecuzione:
  1. Per visualizzare un elenco dei nodi digitare `kubectl get nodes`.  Se si vogliono visualizzare i dettagli completi sui nodi, aggiungere `-o yaml` per ottenere `kubectl get nodes -o yaml`.
  2. Per visualizzare un elenco dei pod in esecuzione, digitare `kubectl get pods --all-namespaces`.

4. Avviare la prima immagine Docker digitando `kubectl run nginx --image nginx`.  Il contenitore Docker nginx verrà avviato in un pod in uno dei nodi.

5. Digitare `kubectl get pods -o yaml` per visualizzare i dettagli completi della distribuzione nginx. È possibile visualizzare l'IP dell'host e l'IP del pod.  L'IP del pod viene assegnato dal CIDR di pod sull'host.  Eseguire curl sull'IP del pod per visualizzare l'output di nginx, ad esempio: `curl 10.244.1.4`

  ![Immagine di curl sull'IP del pod](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. Il passaggio successivo consiste nell'esporre la distribuzione di nginx come servizio Kubernetes nella rete del servizio privata 10.0.0.0/16:
  1. Esporre il servizio con il comando `kubectl expose deployment nginx --port=80`.
  2. Ottenere l'IP del servizio `kubectl get service`
  3. Eseguire curl sull'IP, ad esempio: `curl 10.0.105.199`

  ![Immagine di curl sull'IP del servizio](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. Il passaggio finale consiste nell'esporre il servizio a tutti gli utenti.  Questa operazione viene eseguita cambiando il tipo di servizio da `ClusterIP` a `LoadBalancer`:
  1. Modificare il servizio: `kubectl edit svc/nginx`
  2. Cambiare `type` da `ClusterIP` a `LoadBalancer` e salvarlo.  Kubernetes creerà quindi un servizio Azure Load Balancer con un IP pubblico.
  3. La modifica richiederà circa 2-3 minuti.  Per verificare il passaggio del servizio da "in sospeso" a un IP esterno, digitare `watch 'kubectl get svc'`

  ![Immagine della verifica della transizione da in sospeso a IP esterno](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. Dopo la visualizzazione dell'IP esterno, è possibile passare all'IP nel browser:

  ![Immagine del passaggio a nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. Il passaggio successivo della procedura dettagliata consiste nell'illustrare come gestire il cluster Kubernetes in modalità remota.  Scaricare prima di tutto Kubectl nel computer e inserirlo nel percorso specifico:
  * [Windows Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [OSX Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. Il master di Kubernetes include il file di configurazione kube per l'accesso remoto nella home directory ~/.kube/config.  Scaricare questo file nel computer, configurare la variabile di ambiente KUBECONFIG ed eseguire kubectl per verificare che sia possibile connettersi al cluster:
  * Windows Per usare pscp da [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Assicurarsi che il certificato sia esposto tramite [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X o Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. Il passaggio successivo consiste nell'illustrare come eseguire in modalità remota i comandi in un contenitore Docker remoto:
  1. Eseguire `kubectl get pods` per visualizzare il nome del pod nginx
  2. Usando il nome del pod, è possibile eseguire un comando remoto sul pod.  Ad esempio: `kubectl exec nginx-701339712-retbj date`
  3. Provare a eseguire una sessione bash remota. Ad esempio: `kubectl exec nginx-701339712-retbj -it bash`.  Lo screenshot seguente illustra questi comandi:

  ![Immagine di curl sull'IP del pod](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. Il passaggio finale di questa esercitazione consiste nell'illustrare il dashboard:
  1. Eseguire `kubectl proxy` per connettersi direttamente al proxy
  2. Nel browser passare al [dashboard](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all)
  3. Esplorare i pod e i servizi.
  ![Immagine del dashboard Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>Altre informazioni

Ecco i collegamenti consigliati per ottenere altre informazioni su Kubernetes:

1. [Documentazione su Azure Kubernetes](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Documentazione della community di Kubernetes

1. [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html): illustra come distribuire, ridimensionare ed eseguire il debug di applicazione inserite in contenitori.
2. [Manuale dell'utente di Kubernetes](http://kubernetes.io/docs/user-guide/): fornisce informazioni sull'esecuzione di programmi in un cluster Kubernetes esistente.
3. [Esempi di Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): fornisce alcuni esempi relativi all'esecuzione di applicazioni effettive con Kubernetes.



<!--HONumber=Nov16_HO3-->


