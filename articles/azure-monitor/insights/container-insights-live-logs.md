---
title: Visualizzare i log di Monitoraggio di Azure per i contenitori in tempo reale | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale dei log dei contenitori (stdout o stderr) senza usare kubectl con Monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: c8baa4d2355adf99ce188d632ac50901db29a758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60497664"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Come visualizzare i log dei contenitori in tempo reale con Monitoraggio di Azure per i contenitori (anteprima)
Questa funzionalità, attualmente in anteprima, offre una visualizzazione in tempo reale dei log dei contenitori nel servizio Azure Kubernetes (stdout o stderr) senza bisogno di eseguire comandi kubectl. Quando si seleziona questa opzione, viene visualizzato un nuovo riquadro sotto la tabella dei dati delle prestazioni dei contenitori nella visualizzazione **Contenitori**.  Il riquadro mostra i dati di registrazione in tempo reale generati dal motore dei contenitori per facilitare la risoluzione dei problemi in tempo reale. **Collaboratore** accesso alla risorsa del cluster è necessario per usare questa funzionalità.

I log in tempo reale supportano tre metodi diversi per il controllo dell'accesso ai log:

1. Servizio Azure Kubernetes senza autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata 
2. Servizio Azure Kubernetes con autorizzazione del controllo degli accessi in base al ruolo di Kubernetes abilitata
3. Servizio Azure Kubernetes con Single Sign-On di Azure Active Directory (AD) basato su SAML abilitato 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes con controllo degli accessi in base al ruolo abilitato
 
Se si ha un cluster Kubernetes non configurato con l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes oppure integrato con il Single Sign-On di Azure AD, non è necessario eseguire la procedura seguente. Poiché l'autorizzazione Kubernetes usa kube-api, sono necessarie autorizzazioni di sola lettura.

## <a name="kubernetes-rbac-authorization"></a>Autorizzazione del controllo degli accessi in base al ruolo di Kubernetes
Se è stata abilitata l'autorizzazione del controllo degli accessi in base al ruolo di Kubernetes, è necessario applicare l'associazione di ruolo del cluster. I passaggi di esempio seguenti illustrano come configurare l'associazione di ruolo del cluster da questo modello di configurazione yaml.   

1. Copiare e incollare il file yaml e salvarlo come LogReaderRBAC.yaml.  

   ```
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRole 
   metadata: 
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""] 
        resources: ["pods/log"] 
        verbs: ["get"] 
   --- 
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRoleBinding 
   metadata: 
      name: containerHealth-read-logs-global 
   roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
   subjects: 
      - kind: User 
        name: clusterUser 
        apiGroup: rbac.authorization.k8s.io 
   ```

2. Creare l'associazione di ruolo del cluster eseguendo il comando seguente: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Configurare il servizio Azure Kubernetes con Azure Active Directory
Il servizio Azure Kubernetes può essere configurato in modo da usare Azure Active Directory (AD) per l'autenticazione utente. Se è la prima volta che si esegue questa configurazione, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes](../../aks/azure-ad-integration.md). Durante la procedura per creare l'[applicazione client](../../aks/azure-ad-integration.md#create-client-application) e specificare l'**URI di reindirizzamento**, è necessario aggiungere un altro URI all'elenco `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>La configurazione dell'autenticazione con Azure Active Directory per il Single Sign-On può essere eseguita solo durante la distribuzione iniziale di un nuovo cluster del servizio Azure Kubernetes. Non è possibile configurare l'accesso Single Sign-On per un cluster del servizio Azure Kubernetes già distribuito.  
> 

## <a name="view-live-logs"></a>Visualizzare i log in tempo reale
Quando si consulta la pagina **Contenitori**, si può scegliere **Visualizza log contenitori** oppure **Visualizza log contenitori in tempo reale**.  Selezionando **Visualizza log contenitori in tempo reale**, sotto la tabella di dati delle prestazioni dei contenitori appare un nuovo riquadro che mostra una registrazione dal vivo generata dal motore del contenitore per agevolare ulteriormente la risoluzione dei problemi in tempo reale.  
1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel menu **Microsoft Azure** selezionare **Monitoraggio** e quindi **Contenitori**.  
3. Nella visualizzazione dei **contenitori monitorati** selezionare un contenitore dall'elenco.  
4. Selezionare la visualizzazione **Contenitori** e nel pannello delle proprietà del contenitore selezionato sarà presente il collegamento **Visualizza log contenitori in tempo reale**.  
5. Se il cluster del servizio Azure Kubernetes è configurato con il Single Sign-On di AAD, durante la sessione del browser viene chiesto di eseguire l'autenticazione per il primo utilizzo. Selezionare l'account e completare l'autenticazione con Azure.  

Dopo l'autenticazione, il riquadro dei log in tempo reale verrà visualizzato nella parte inferiore del riquadro centrale. Se l'indicatore dello stato di recupero mostra un segno di spunta verde sul lato destro del riquadro, significa che è possibile recuperare dati.
    
  ![Dati recuperati nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-01.png)  

Nella barra di ricerca è possibile filtrare per parola chiave per evidenziare il testo nel log.   

  ![Esempio di filtro nel riquadro dei log in tempo reale](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Per sospendere lo scorrimento automatico e controllare il comportamento del riquadro in modo da poter scorrere manualmente i nuovi dati di log, fare clic sull'opzione **Scorri**.  Per riabilitare lo scorrimento automatico è sufficiente fare clic nuovamente sull'opzione **Scorri**.  È anche possibile sospendere il recupero dei dati di log facendo clic sull'opzione **Sospendi**. Quando si è pronti per riprendere, fare clic su **Riproduci**.  

![Visualizzazione dei log in tempo reale in pausa](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md).
- Vista [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinito ed esempi per valutare o personalizzare per gli avvisi, la visualizzazione o l'analisi dei cluster.
