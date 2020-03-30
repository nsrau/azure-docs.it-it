---
title: Visualizzare i dati da Azure Data Explorer tramite Kibana
description: In questo articolo viene illustrato come configurare Azure Data Explorer come origine dati per Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065606"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualizzare i dati da Azure Data Explorer in Kibana con il connettore open source K2Bridge

K2Bridge (Kibana-Kusto Bridge) enables you to use Azure Data Explorer as the data source and to visualize that data in Kibana. K2Bridge è un'applicazione containerizzata open source che funge da proxy tra un'istanza di Kibana e un cluster Azure Data Explorer.K2Bridge is an [open-source](https://github.com/microsoft/K2Bridge) containerized application which acts as a proxy between a Kibana instance and an Azure Data Explorer cluster. In questo articolo viene descritto come utilizzare K2Bridge per creare tale connessione.

K2Bridge converte le query di Kibana in Kusto Query Language (KQL) e invia i risultati di Azure Data Explorer a Kibana. 

   ![grafico](media/k2bridge/k2bridge-chart.png)

K2Bridge supporta la scheda Discover di Kibana, dove puoi:
* Cercare ed esplorare i dati
* Filtrare i risultati
* Aggiungere o rimuovere campi nella griglia dei risultati
* Visualizzare il contenuto dei record
* Salvare e condividere ricerche

L'immagine seguente mostra un'istanza di Kibana associata ad Azure Data Explorer da K2Bridge.The image below shows a Kibana instance bound to Azure Data Explorer by K2Bridge. L'esperienza utente in Kibana è invariata.

   [![Pagina di Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prerequisiti

Prima di poter visualizzare i dati da Azure Data Explorer in Kibana, è necessario utilizzare le opzioni seguenti:Before you can visualize data from Azure Data Explorer in Kibana, have the following ready:

* [Helm V3](https://github.com/helm/helm#install), il gestore di pacchetti Kubernetes
* Cluster di Azure Kubernetes Service (AKS) o qualsiasi altro cluster Kubernetes (versione 1.14 alla versione 1.16 sono stati testati e verificati). Se è necessario un cluster AKS, vedere Distribuire un cluster AKS [usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) di Azure o il portale di AzureIf you need an AKS cluster, see Deploy an AKS cluster using the Azure CLI or using the Azure [portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Un [cluster di Azure Data Explorer,](create-cluster-database-portal.md)tra cui:An Azure Data Explorer cluster, including:
    * URL del cluster Azure Data ExplorerThe Azure Data Explorer cluster's URL 
    * Nome del database
    
* Un'entità servizio di Azure AD autorizzata a visualizzare i dati in Azure Data Explorer, tra cui:An Azure AD service principal authorized to view data in Azure Data Explorer, including:
    * L'ID client 
    * Il segreto client

    È consigliata un'entità servizio con autorizzazione 'Viewer'. È sconsigliato utilizzare autorizzazioni più elevate.

    * [Impostare le autorizzazioni di visualizzazione del cluster per l'entità servizio](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)di Azure AD.

    Per altre informazioni sull'entità servizio di Azure AD, vedere [Creare un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)di Azure AD.

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Eseguire K2Bridge nel servizio Azure Kubernetes (AKS)Run K2Bridge on Azure Kubernetes Service (AKS)

Per impostazione predefinita, il grafico Helm di K2Bridges fa riferimento a un'immagine disponibile pubblicamente disponibile nel Registro contenitori (MCR) di Microsoft. MCR non richiede credenziali e funziona out-of-the-box.

1. Scaricare i grafici Helm richiesti.

1. Aggiungere la dipendenza Elasticsearch a Helm. 
    Il motivo per la dipendenza elastico è che K2Bridge utilizza un'istanza elastica di piccole dimensioni interna per gestire le richieste correlate ai metadati (ad esempio modelli di indice e query salvate). Nessun dato aziendale viene salvato in questa istanza interna e può essere considerato come un dettaglio di implementazione. 

    1. Per aggiungere la dipendenza Elasticsearch a Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Per ottenere il grafico K2Bridge dalla directory charts del repository GitHub:
        1. Clonare il repository da [GitHub](https://github.com/microsoft/K2Bridge).
        1. Passare alla directory del repository radice K2Bridges.
        1. Eseguire:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Distribuisci K2Bridge:

    1. Impostare le variabili con i valori corretti per l'ambiente:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Facoltativo) Abilitare i dati di telemetria di Azure Application Insights.Enable Azure Application Insights telemetry. 
        Se è la prima volta che si usa Azure Application Insights, è innanzitutto necessario [creare una risorsa application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). È necessario [copiare la chiave](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) di strumentazione in una variabile:You will need to copy the instrumentation key to a variable: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installare il grafico K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        In [Configurazione](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) è possibile trovare il set completo di opzioni di configurazione.

    1. L'output del comando suggerirà il prossimo comando Helm da eseguire per distribuire Kibana. Facoltativamente, eseguire:Optionally, run:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Utilizzare port forwarding per accedere a Kibana su localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Connettersi a Kibana http://127.0.0.1:5601sfogliando a .

    1. Esporre Kibana agli utenti finali. Esistono diversi metodi per eseguire questa operazione. Il metodo utilizzato dipende in gran parte dal caso d'uso.

        Ad esempio:

        Esporre il servizio come servizio LoadBalancer.Expose the service as a LoadBalancer service. A tale scopo, `--set service.type=LoadBalancer` aggiungere il parametro al comando di installazione K2Bridge Helm ([sopra](#install-k2bridge-chart)).        
    
        Eseguire quindi:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        L'output sarà simile al seguente: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        È quindi possibile utilizzare l'INDIRIZZO IP ESTERNO generato visualizzato e utilizzarlo `<EXTERNAL-IP>:5601`per accedere a Kibana aprendo un browser a .

1. Configurare i modelli di indice per accedere ai dati:Configure index patterns to access your data:  
In una nuova istanza di Kibana:
     1. Apri Kibana.
     1. Passare a Gestione.Navigate to Management.
     1. Selezionare **Modelli indice**. 
     1. Creare un modello di indice.
Il nome dell'indice deve corrispondere esattamente al nome della tabella o della funzione, senza asterisco. È possibile copiare la riga pertinente dall'elenco.

> [!Note]
> Per eseguire su altri provider Kubernetes, modificare `values.yaml` elasticsearch storageClassName in modo che si adatti a quello suggerito dal provider.

## <a name="visualize-data"></a>Visualizzare i dati

Quando Azure Data Explorer è configurato come origine dati per Kibana, è possibile usare Kibana per esplorare i dati. 

1. In Kibana, nel menu a sinistra, seleziona la scheda **Scopri.**

1. Nell'elenco a discesa a sinistra selezionare un modello di indice (in questo caso, una tabella di Azure Data Explorer), che definisce l'origine dati da esplorare.
    
   ![Selezionare un modello di indice](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Se i dati hanno un campo filtro temporale, è possibile specificare l'intervallo di tempo. In alto a destra nella pagina, impostare un filtro temporale. Per impostazione predefinita, Discover mostra i dati degli ultimi 15 minuti.

   ![Filtro temporale](media/k2bridge/k2bridge-time-filter.png)
    
1. La tabella dei risultati mostra i primi 500 record. È possibile espandere un documento per esaminarne i dati dei campi in formato JSON o di tabella.

   ![Espandere un record](media/k2bridge/k2bridge-expand-record.png)

1. Per impostazione predefinita, la tabella dei risultati include le colonne per il _source del documento e il campo del tempo (se presente). Puoi scegliere colonne specifiche da aggiungere alla tabella dei risultati selezionando **Aggiungi** accanto al nome del campo nella barra laterale sinistra.

   ![Colonne specifiche](media/k2bridge/k2bridge-specific-columns.png)
    
1. Nella barra delle query è possibile cercare i dati in base a:
    * Immissione di un termine di ricerca
    * Utilizzo della sintassi della query Lucene. 
    Ad esempio:
        * Cercare "error" per trovare tutti i record che contengono questo valore. 
        * Cercare "status: 200", per ottenere tutti i record con un valore di stato di 200. 
    * Utilizzo di operatori logici (AND, OR, NOT)
    * Utilizzo di caratteri \* jolly (asterisco " " o punto interrogativo "?") Per esempio:
        * La `"destination_city: L*"` query corrisponderà ai record in cui il valore della città di destinazione inizia con "l" (K2Bridge non fa distinzione tra maiuscole e minuscole).

    ![Esegui query](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > In [Ricerca](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md), è possibile trovare altre regole di ricerca e logica.

1. Per filtrare i risultati della ricerca, utilizza **l'elenco** dei campi nella barra laterale destra della pagina. 
    L'elenco dei campi è dove è possibile visualizzare:
    * I primi cinque valori per il campo
    * Il numero di record che contengono il campo
    * Percentuale di record che contengono ogni valore. 
    
    >[!Tip]
    > Utilizzare l'icona della lente di ingrandimento (-) per trovare tutti i record con un valore specifico.
    
    ![Elenco campi](media/k2bridge/k2bridge-field-list.png)
   
    È inoltre possibile filtrare i risultati utilizzando l'icona della lente di ingrandimento (-) nella visualizzazione in formato tabella dei risultati di ogni record nella tabella dei risultati.
    
     ![Elenco tabelle](media/k2bridge/k2bridge-table-list.png)
    
1. Selezionare **Salva** o **Condividi** ricerca.

     ![Salva ricerca](media/k2bridge/k2bridge-save-search.png)
