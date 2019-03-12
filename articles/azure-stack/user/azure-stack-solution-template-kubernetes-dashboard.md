---
title: Accedere al Dashboard di Kubernetes in Azure Stack | Microsoft Docs
description: Informazioni su come accedere al Dashboard di Kubernetes in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 243fb01a5c8112afaf310f616b93c2667ac6f19d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729862"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Accedere al Dashboard di Kubernetes in Azure Stack 

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack* 
> [!Note]   
> Kubernetes in Azure Stack è disponibile in anteprima. Scenario disconnesso di Azure Stack non è attualmente supportato per l'anteprima. 

Kubernetes include un dashboard web che è possibile usare per le operazioni di gestione di base. Questo dashboard consente di visualizzare lo stato di integrità di base e le metriche per le applicazioni, creare e distribuire servizi e modificare applicazioni esistenti. Questo articolo illustra come configurare il dashboard di Kubernetes in Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Prerequisiti per il Dashboard di Kubernetes

* Cluster Azure Kubernetes Stack

    È necessario avere distribuito un cluster Kubernetes in Azure Stack. Per altre informazioni, vedere [distribuzione Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Client SSH

    È necessario un client SSH alla protezione di connettersi al nodo master nel cluster. Se si usa Windows, è possibile usare [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Sono necessari la chiave privata usata quando è stato distribuito il cluster Kubernetes.

* FTP (PSCP)

    È necessario anche un client FTP che supporti SSH e SSH File Transfer Protocol per trasferire i certificati dal nodo master per il computer di gestione di Azure Stack. È possibile usare [FileZilla](https://filezilla-project.org/download.php?type=client). Sono necessari la chiave privata usata quando è stato distribuito il cluster Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Panoramica dei passaggi per abilitare i dashboard

1.  Esportare i certificati di Kubernetes dal nodo master nel cluster. 
2.  Importare i certificati in Azure Stack il computer di gestione.
2.  Aprire il dashboard web di Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Esportare un certificato dal server master 

È possibile recuperare l'URL per il dashboard dal nodo master nel cluster.

1. Ottenere l'indirizzo IP pubblico e il nome utente per il cluster master di dal dashboard di Azure Stack. Per ottenere queste informazioni:

    - Accedi al [portale di Azure Stack](https://portal.local.azurestack.external/)
    - Selezionare **tutti i servizi** > **tutte le risorse**. Trovare il master nel gruppo di risorse cluster. È denominato master `k8s-master-<sequence-of-numbers>`. 

2. Aprire il nodo principale nel portale. Copia il **indirizzo IP pubblico** indirizzo. Fare clic su **Connect** per ottenere il nome utente nel **account di accesso con account locale della macchina virtuale** casella. Questo è lo stesso nome utente che è impostato quando si crea il cluster. Usare l'indirizzo IP pubblico anziché l'indirizzo IP privato elencato nel pannello della connessione.

3.  Aprire un client SSH per connettersi al master. Se si lavora in Windows, è possibile usare [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) per creare la connessione. Verrà utilizzato l'indirizzo IP pubblico per il nodo master, il nome utente ed aggiungere la chiave privata usata durante la creazione del cluster.

4.  Quando si connette il terminale, digitare `kubectl` per aprire il client della riga di comando di Kubernetes.

5. Eseguire il comando seguente:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Trovare l'URL per il dashboard. Ad esempio: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Estrarre il certificato autofirmato e convertirlo in formato PFX. Eseguire il comando seguente:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Ottiene l'elenco dei segreti durante la **kube system** dello spazio dei nomi. Eseguire il comando seguente:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Assicurarsi di annotare il kubernetes-dashboard-token -\<XXXXX > valore. 

8.  Ottenere il token e salvarlo. Aggiornamento di `kubernetes-dashboard-token-<####>` con il valore del segreto nel passaggio precedente.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importare il certificato

1. Aprire Filezilla e connettersi al nodo master. È necessario il:

    - l'indirizzo IP pubblico a un nodo master
    - il nome utente
    - il segreto privato
    - Usare **SFTP - SSH File Transfer Protocol**

2. Copia `/etc/kubernetes/certs/client.pfx` e `/etc/kubernetes/certs/ca.crt` nel computer di gestione di Azure Stack.

3. Assicurarsi di annotare i percorsi dei file. Aggiornare lo script con i percorsi e quindi aprire PowerShell con privilegi elevati. Eseguire lo script aggiornato:  

    ```PowerShell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Aprire il dashboard di Kubernetes 

1.  Disabilitare il blocco popup nel Web browser.

2.  Il browser all'URL indicato quando è stato eseguito il comando punto `kubectl cluster-info`. Ad esempio: https://azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3.  Selezionare il certificato client.
4.  Immettere il token. 
5. Riconnettersi alla riga di comando bash nel nodo master e concedere le autorizzazioni per `kubernetes-dashboard`. Eseguire il comando seguente:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Lo script offre `kubernetes-dashboard` Cloud i privilegi di amministratore. Per altre informazioni, vedere [cluster abilitato per RBAC](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

È possibile usare il dashboard. Per altre informazioni nel dashboard di Kubernetes, vedere [Dashboard dell'interfaccia utente Web Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Dashboard di Kubernetes di Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi 

[Distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Aggiungere un cluster Kubernetes nel Marketplace (per l'operatore di Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
