---
title: Distribuzione di Kubernetes in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire Kubernetes in Azure Stack.
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
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 4aa74aebfd9e0ef6c71913c76181a07045aa3ad7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231220"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Distribuzione di Kubernetes in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

L'articolo seguente esamina usando un modello di soluzione di Azure Resource Manager per la distribuzione e provisioning delle risorse di Kubernetes in un'unica operazione coordinata. Verrà necessario per raccogliere le informazioni necessarie relative all'installazione di Azure Stack, generare il modello e quindi distribuire il cloud. Si noti il modello non sono lo stesso servizio AKS gestito disponibile in Azure globale.

## <a name="kubernetes-and-containers"></a>Contenitori e Kubernetes

È possibile installare Kubernetes usando i modelli di Azure Resource Manager generati dal motore del servizio contenitore di AZURE in Azure Stack. [Kubernetes](https://kubernetes.io) è un sistema open source per automatizzare la distribuzione, ridimensionamento e la gestione delle applicazioni nei contenitori. Oggetto [contenitore](https://www.docker.com/what-container) è contenuta in un'immagine, simile a una macchina virtuale. A differenza di una macchina virtuale, l'immagine del contenitore include solo le risorse che necessarie per eseguire un'applicazione, ad esempio il codice, eseguire il codice, librerie specifiche e le impostazioni di runtime.

È possibile usare Kubernetes per:

- Sviluppare applicazioni con architettura altamente scalabile, aggiornabile, che possono essere distribuite in pochi secondi. 
- Semplificare la progettazione dell'applicazione e migliorare l'affidabilità da applicazioni diverse di Helm. [Helm](https://github.com/kubernetes/helm) è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes.
- Facilmente monitorare e diagnosticare l'integrità delle applicazioni con scalabilità e funzionalità di aggiornamento.

Ti verrà addebitata solo per l'utilizzo di calcolo necessario per i nodi che supportano il cluster. Per altre informazioni, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="prerequisites"></a>Prerequisiti 

Per iniziare, assicurarsi che si abbia le autorizzazioni appropriate e che Azure Stack è pronto.

1. Verificare che è possibile creare applicazioni nel tenant di Azure Active Directory (Azure AD). Autorizzazioni necessarie per la distribuzione di Kubernetes.

    Per istruzioni sul controllo delle autorizzazioni, vedere [autorizzazioni di controllo Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Generare una pubblica e privata coppia di chiavi SSH per accedere alla VM Linux in Azure Stack. La chiave pubblica è necessario quando si crea il cluster.

    Per istruzioni su come generare una chiave, vedere [generazione di chiavi SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Verificare che si abbia una sottoscrizione valida nel portale tenant di Azure Stack e di avere sufficiente IP pubblico indirizzi disponibili per aggiungere nuove applicazioni.

    Il cluster non può essere distribuito in Azure Stack **amministratore** sottoscrizione. È necessario usare una **utente** sottoscrizione. 

1. Se non è un Kubernetes Cluster nel marketplace, rivolgersi all'amministratore di Azure Stack.

## <a name="create-a-service-principal-in-azure-ad"></a>Creare un'entità servizio in Azure AD

1. Accedi a globale [portale di Azure](http://portal.azure.com).

1. Controllo che eseguito l'accesso con il tenant di Azure AD associato all'istanza di Azure Stack. È possibile passare un accesso aggiuntivo facendo clic sull'icona del filtro sulla barra degli strumenti di Azure.

    ![Selezionare il tenant di AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Creare un'applicazione Azure AD.

    a. Selezionare **Azure Active Directory** > **+ registrazioni per l'App** > **registrazione nuova applicazione**.

    b. Immettere un **nome** dell'applicazione.

    c. Selezionare **app Web / API**.

    d. Immettere `http://localhost` per il **Sign-on di URL**.

    c. Fare clic su **Create**(Crea).

1. Annotare l'**ID applicazione**. È necessario l'ID quando si crea il cluster. L'ID viene fatto riferimento come **Client ID dell'entità servizio**.

1. Selezionare **le impostazioni** > **chiavi**.

    a. Immettere il **descrizione**.

    b. Selezionare **non scade mai** per **Expires**.

    c. Selezionare **Salva**. Prendere nota di stringa della chiave. La stringa della chiave sarà necessario durante la creazione del cluster. La chiave viene fatto riferimento come le **segreto Client entità servizio**.


## <a name="give-the-service-principal-access"></a>Concedere l'accesso dell'entità servizio

Assegnare l'entità servizio l'accesso alla sottoscrizione in modo che l'entità di creare le risorse.

1.  Accedi per il [portale di Azure Stack](https://portal.local.azurestack.external/).

1. Selezionare **tutti i servizi** > **sottoscrizioni**.

1. Selezionare la sottoscrizione creata dall'operatore di per l'uso del Kubernetes Cluster.

1. Selezionare **controllo di accesso (IAM)** > selezionare **+ Aggiungi**.

1. Selezionare il **collaboratore** ruolo.

1. Selezionare il nome dell'applicazione creato per il servizio dell'entità. È possibile digitare il nome nella casella di ricerca.

1. Fare clic su **Save**.

## <a name="deploy-a-kubernetes"></a>Distribuzione di Kubernetes

1. Aprire il [portale di Azure Stack](https://portal.local.azurestack.external).

1. Selezionare **+ crea una risorsa** > **calcolo** > **Kubernetes Cluster**. Fare clic su **Create**(Crea).

    ![Distribuisci modello di soluzione](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Nozioni di base

1. Selezionare **nozioni di base** nel creare un Cluster Kubernetes.

    ![Distribuisci modello di soluzione](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Selezionare i **sottoscrizione** ID.

1. Immettere il nome del nuovo gruppo di risorse o selezionare un gruppo di risorse esistente. Il nome della risorsa deve essere alfanumerico e lettere minuscole.

1. Selezionare il **posizione** del gruppo di risorse. Questa è l'area che scelta per l'installazione di Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Impostazioni del Cluster Kubernetes

1. Selezionare **le impostazioni del Cluster Kubernetes** nel creare un Cluster Kubernetes.

    ![Distribuisci modello di soluzione](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Immettere il **nome utente dell'amministratore della macchina virtuale Linux**. Nome utente per le macchine virtuali di Linux che fanno parte del cluster Kubernetes e DVM.

1. Immettere il **chiave pubblica SSH** utilizzati per l'autorizzazione a tutti i computer Linux creato come parte del cluster Kubernetes e DVM.

1. Immettere il **Master del prefisso DNS del profilo** che è univoco nell'area. Deve essere un nome univoco con area, ad esempio `k8s-12345`. Provare a scelta lo stesso del gruppo di risorse nome ritiene più pratica.

    > [!Note]  
    > Per ogni cluster, usare un prefisso DNS del profilo master nuovi ed esclusivi.

1. Selezionare il **Kubernetes Master Pool profilo conteggio**. Il conteggio contiene il numero di nodi nel pool master. Può esistere da 1 a 7. Questo valore deve essere un numero dispari.

1. Selezionare **VMSize la VM master Kubernetes**.

1. Selezionare il **numero profilo del Pool di nodi Kubernetes**. Il conteggio contiene il numero di agenti nel cluster. 

1. Selezionare il **profilo di archiviazione**. È possibile scegliere **Blob di disco** oppure **disco gestito**. Specifica le macchine virtuali del nodo dimensioni di macchina virtuale di Kubernetes. 

1. Immettere il **entità servizio ClientId** viene utilizzato dal provider di cloud di Azure in Kubernetes. L'ID Client identificato come l'ID dell'applicazione durante la creazione dell'entità servizio.

1. Immettere il **segreto Client entità servizio** creato al momento della creazione dell'entità servizio.

1. Immettere il **versione del Provider Cloud di Azure in Kubernetes**. Si tratta della versione per il provider di Azure in Kubernetes. Azure Stack rilascia una compilazione Kubernetes personalizzata per ogni versione di Azure Stack.

### <a name="3-summary"></a>3. Summary

1. Selezionare riepilogo. Il pannello visualizza un messaggio di convalida per le impostazioni delle configurazioni di Kubernetes Cluster.

    ![Distribuisci modello di soluzione](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Verificare le impostazioni.

3. Selezionare **OK** per distribuire il cluster.

> [!TIP]  
>  Se hai domande sulla distribuzione, è possibile pubblicare una domanda o vedere se un utente ha già risposto alla domanda nel [Forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="connect-to-your-cluster"></a>Connetti al cluster

A questo punto si è pronti per connettersi al cluster. Il master è reperibile nel gruppo di risorse cluster e viene denominato `k8s-master-<sequence-of-numbers>`. Usare un client SSH per connettersi al master. Sul master, è possibile usare **kubectl**, il client della riga di comando di Kubernetes per gestire il cluster. Per istruzioni, vedere [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

È anche possibile trovare il **Helm** utili per l'installazione e distribuzione di App nel cluster di gestione pacchetti. Per istruzioni sull'installazione e usare Helm con il cluster, vedere [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un Kubernetes nel Marketplace (per l'operatore di Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
