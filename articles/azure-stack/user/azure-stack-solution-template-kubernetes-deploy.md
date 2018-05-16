---
title: Distribuire un Cluster Kubernetes allo Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire un Kubernetes Cluster allo Stack di Azure.
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 7cf865f0ce75d8308d6d42306e8e05852f763cae
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Distribuire un cluster Kubernetes allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

> [!Note]  
> Il Kubernetes del contenitore Servizi di Azure (ACS) nello Stack di Azure è disponibile in anteprima privata. L'operatore di Stack Azure dovrà richiedere l'accesso per l'elemento del Kubernetes Marketplace necessaria per eseguire le istruzioni riportate in questo articolo.

Il seguente articolo vengono esaminati utilizzando un modello di soluzione di gestione risorse di Azure per distribuire ed eseguire il provisioning di risorse per Kubernetes in un'operazione singola, coordinata. Verrà necessario per raccogliere le informazioni necessarie sull'installazione dello Stack di Azure, generare il modello e quindi distribuire al cloud di.

## <a name="kubernetes-and-containers"></a>Kubernetes e contenitori

È possibile installare Kubernetes del contenitore Servizi di Azure (ACS) nello Stack di Azure. [Kubernetes](https://kubernetes.io) è un sistema open source per automatizzare la distribuzione, la scalabilità e la gestione delle applicazioni nei contenitori. Un [contenitore](https://www.docker.com/what-container) è contenuto in un'immagine, simile a una macchina virtuale. A differenza di una macchina virtuale è solo l'immagine contenitore include le risorse che necessarie per eseguire un'applicazione, ad esempio il codice, runtime per eseguire il codice, librerie specifiche e le impostazioni.

È possibile utilizzare Kubernetes per:

- Sviluppare applicazioni altamente scalabile, aggiornabile, che possono essere distribuite in secondi. 
- Semplificare la progettazione dell'applicazione e per migliorare l'affidabilità da diverse applicazioni Helm. [Helm](https://github.com/kubernetes/helm) è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes.
- Facilmente monitorare e diagnosticare l'integrità delle applicazioni con scalabilità e funzionalità di aggiornamento.

## <a name="prerequisites"></a>Prerequisiti 

Per iniziare, verificare che si dispone delle autorizzazioni appropriate e che lo Stack di Azure è pronto.

1. Verificare che è possibile creare applicazioni nel tenant di Azure Active Directory (Azure AD). Queste autorizzazioni necessarie per la distribuzione Kubernetes.

    Per istruzioni sul controllo delle autorizzazioni, vedere [le autorizzazioni di controllo Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Generare una pubblica e privata coppia di chiavi SSH per accedere alla VM Linux nello Stack di Azure. Quando si crea il cluster, occorrerà la chiave pubblica.

    Per istruzioni sulla generazione di una chiave, vedere [generazione delle chiavi SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Verificare che il portale tenant di Azure Stack si ha una sottoscrizione valida e di avere sufficiente IP pubblico indirizzi disponibili per l'aggiunta di nuove applicazioni.

## <a name="create-a-service-principal-in-azure-ad"></a>Creare un'entità servizio in Azure AD

1. Accedi a globale [portale di Azure](http://www.poartal.azure.com).
2. Verificare di aver effettuato usando il tenant di Azure AD associato con lo Stack di Azure.
3. Creare un'applicazione Azure AD.

    a. Selezionare **Azure Active Directory** > **+ App registrazioni** > **nuova registrazione applicazione**.

    b. Immettere un **nome** dell'applicazione.

    c. Selezionare **app Web / API**

    d. Immettere `http://localhost` per il **Sign-on URL**.

    c. Fare clic su **Crea**

4. Annotare il **ID applicazione**. Quando si crea il cluster, sarà necessario l'ID. L'ID viene fatto riferimento come **Client ID dell'entità servizio**.

5. Selezionare **impostazioni** > **chiavi**.

    a. Immettere il **descrizione**.

    b. Selezionare **non scade mai** per **Expires**.

    c. Selezionare **Salva**. Prendere nota di stringa della chiave. La stringa chiave sarà necessario quando si crea il cluster. La chiave è references come le **privata del Client dell'entità servizio**.



## <a name="give-the-service-principal-access"></a>Concedere l'accesso dell'entità servizio

Dare l'accesso dell'entità servizio per la sottoscrizione in modo che l'entità di creare le risorse.

1.  Accedi ai [portale di amministrazione](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **sottoscrizioni utente** > **+ Aggiungi**.

3. Selezionare la sottoscrizione creata.

4. Selezionare **Access control (IAM)** > selezionare **+ Aggiungi**.

5. Selezionare il **proprietario** ruolo.

6. Selezionare il nome dell'applicazione creato per il servizio dell'entità. È possibile digitare il nome nella casella di ricerca.

7. Fare clic su **Save**.

## <a name="deploy-a-kubernetes-cluster"></a>Distribuire un Cluster Kubernetes

1. Aprire la [portale Azure Stack](https://portal.local.azurestack.external).

2. Selezionare **+ nuova** > **calcolo** > **Kubernetes Cluster**.

    ![Distribuisci modello di soluzione](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Selezionare **parametri** in di distribuire il modello di soluzione.

    ![Distribuisci modello di soluzione](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Immettere il **nome utente amministrativo Linux**. Nome utente per le macchine virtuali Linux che fanno parte del cluster Kubernetes e DVM.

3. Immettere il **la chiave pubblica SSH** utilizzato per l'autorizzazione a tutti i computer Linux creato come parte del cluster Kubernetes e DVM.

4. Immettere il **tenant endpoint**. Si tratta dell'endpoint di gestione risorse di Azure per la connessione per creare il gruppo di risorse per il cluster Kubernetes. È necessario ottenere l'endpoint dall'operatore di Stack di Azure per un sistema integrato. Per il Azure Stack Development Kit (ASDK), è possibile utilizzare `https://management.local.azurestack.external`.

5. Immettere il **ID tenant** per il tenant. Se è necessario trovare questo valore, vedere [ottenere l'ID tenant](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Immettere il **prefisso DNS profilo master** che è univoco per l'area. Deve essere un nome univoco con area geografica, ad esempio `k8s-12345`. Provare a scelto uguale al gruppo di risorse denominare le procedure consigliate.

    > [!Note]  
    > Per ogni cluster, usare un prefisso DNS nuovi ed esclusivi profilo master.

7. Immettere il numero di agenti nel cluster. Questo valore è noto come il **numero profilo del Pool di agenti**. Può esistere compreso tra 1 e 32

8. Immettere il **ID applicazione dell'entità servizio** viene utilizzato dal provider di cloud di Kubernetes Azure.

9. Immettere il **privata del client dell'entità servizio** creato al momento della creazione dell'applicazione dell'entità servizio.

10. Immettere il **versione del Provider Cloud Azure Kubernetes**. Si tratta della versione per il provider di Kubernetes Azure. Stack Azure rilascia una compilazione Kubernetes personalizzata per ogni versione di Azure Stack.

12. Selezionare **OK**.

### <a name="specify-the-solution-values"></a>Specificare i valori della soluzione

1. Selezionare il **sottoscrizione**.

2. Immettere il nome del nuovo gruppo di risorse o selezionare un gruppo di risorse esistente. Il nome della risorsa deve essere alfanumerico e lettere minuscole.

3. Immettere il percorso del gruppo di risorse, ad esempio **locale**.

4. Selezionare **creare.**

## <a name="connect-to-your-cluster"></a>Connetti al cluster

A questo punto si è pronti per la connessione al cluster. È necessario il **kubectl**, il client della riga di comando Kubernetes. È possibile trovare istruzioni su come connettersi e gestire il cluster nella documentazione di servizi di contenitore di Azure.   

Per istruzioni, vedere [Connetti al cluster](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un Kubernetes Cluster nel Marketplace (per l'operatore di Stack di Azure)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)