---
title: Visualizzare i dati da Esplora dati di Azure con Kibana
description: Questo articolo illustra come configurare Esplora dati di Azure come origine dati per Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164813"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualizzare i dati da Esplora dati di Azure in Kibana con il connettore open source K2Bridge

K2Bridge (Kibana-kusto Bridge) consente di usare Azure Esplora dati come origine dati e di visualizzare i dati in Kibana. K2Bridge è un'applicazione [Open Source](https://github.com/microsoft/K2Bridge) in contenitori che funge da proxy tra un'istanza di Kibana e un cluster di Azure Esplora dati. Questo articolo descrive come usare K2Bridge per creare la connessione.

K2Bridge converte le query Kibana in kusto Query Language (KQL) e invia i risultati del Esplora dati di Azure a Kibana. 

   ![grafico](media/k2bridge/k2bridge-chart.png)

K2Bridge supporta la scheda Discover di Kibana, in cui è possibile:
* Eseguire ricerche ed esplorare i dati
* Filtrare i risultati
* Aggiungere o rimuovere campi nella griglia dei risultati
* Visualizza contenuto record
* Salvare e condividere le ricerche

L'immagine seguente mostra un'istanza di Kibana associata ad Azure Esplora dati da K2Bridge. L'esperienza utente in Kibana è invariata.

   [Pagina Kibana ![](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

Prima di poter visualizzare i dati da Azure Esplora dati in Kibana, procedere come segue:

* [Helm V3](https://github.com/helm/helm#install), gestione pacchetti Kubernetes
* Il cluster Azure Kubernetes Service (AKS) o qualsiasi altro cluster Kubernetes (versione 1,14 alla versione 1,16 sono stati testati e verificati). Se è necessario un cluster AKS, vedere distribuire un cluster AKS [usando l'interfaccia della](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) riga di comando di Azure o [l'portale di Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* Un [cluster di Azure Esplora dati](create-cluster-database-portal.md), tra cui:
    * URL del cluster Esplora dati di Azure 
    * Nome del database
    
* Un'entità servizio Azure AD autorizzata a visualizzare i dati in Azure Esplora dati, tra cui:
    * ID client 
    * Il segreto client

    È consigliata un'entità servizio con l'autorizzazione ' Viewer '. È sconsigliato utilizzare autorizzazioni più elevate.

    * [Impostare le autorizzazioni di visualizzazione del cluster per l'entità servizio Azure ad](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Per ulteriori informazioni sull'entità servizio Azure AD, vedere [creare un'entità servizio Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Eseguire K2Bridge in Azure Kubernetes Service (AKS)

Per impostazione predefinita, il grafico Helm di K2Bridges's fa riferimento a un'immagine disponibile pubblicamente che si trova nel Container Registry Microsoft. Per non è necessaria alcuna credenziale e la funzione è predefinita.

1. Scaricare i grafici Helm richiesti.

1. Aggiungere la dipendenza elasticsearch a Helm. 
    Il motivo della dipendenza elasticsearch è che K2Bridge usa un'istanza di Small elasticsearch interna per soddisfare le richieste correlate ai metadati (ad esempio, i modelli di indice e le query salvate). Nessun dato business viene salvato in questa istanza interna e può essere considerato come un dettaglio di implementazione. 

    1. Per aggiungere la dipendenza elasticsearch a Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Per ottenere il grafico K2Bridge dalla directory dei grafici del repository GitHub:
        1. Clonare il repository da [GitHub](https://github.com/microsoft/K2Bridge).
        1. Passare alla directory del repository radice K2Bridges.
        1. Eseguire:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Distribuire K2Bridge:

    1. Impostare le variabili con i valori corretti per l'ambiente:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Opzionale Abilitare la telemetria di applicazione Azure Insights. 
        Se è la prima volta che si usa applicazione Azure Insights, è necessario [creare prima di tutto una risorsa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Sarà necessario [copiare la chiave di strumentazione](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) in una variabile: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installare il grafico K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        In [configurazione](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) è possibile trovare il set completo di opzioni di configurazione.

    1. L'output del comando suggerisce il comando Helm successivo da eseguire per distribuire Kibana. Facoltativamente, eseguire:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Usare il Port inoltring per accedere a Kibana in localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Connettersi a Kibana passando a http://127.0.0.1:5601.

    1. Esporre Kibana agli utenti finali. Esistono diversi metodi per eseguire questa operazione. Il metodo usato in gran parte dipende dal caso d'uso.

        Ad esempio:

        Esporre il servizio come servizio LoadBalancer. A tale scopo, aggiungere il parametro seguente al comando K2Bridge Helm Install ([sopra](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Eseguire quindi:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        L'output sarà simile al seguente: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        È quindi possibile usare l'IP esterno generato visualizzato e usarlo per accedere a Kibana aprendo un browser a: `\<EXTERNAL-IP>:5601`.

1. Configurare i modelli di indice per accedere ai dati:  
In una nuova istanza di Kibana:
     1. Aprire Kibana.
     1. Passare a gestione.
     1. Selezionare **modelli di indice**. 
     1. Creare un modello di indice.
Il nome dell'indice deve corrispondere esattamente al nome della tabella o della funzione, senza un asterisco. È possibile copiare la riga pertinente dall'elenco.

> [!Note]
> Per eseguire su altri provider Kubernetes, modificare il storageClassName elasticsearch in `values.yaml` per adattarlo a quello suggerito dal provider.

## <a name="visualize-data"></a>Visualizzare i dati

Quando Esplora dati di Azure è configurato come origine dati per Kibana, è possibile usare Kibana per esplorare i dati. 

1. In Kibana, nel menu a sinistra, selezionare la scheda **individua** .

1. Dall'elenco a discesa a sinistra selezionare un modello di indice, in questo caso una tabella di Esplora dati di Azure, che definisce l'origine dati che si desidera esplorare.
    
   ![Selezionare un modello di indice](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Se i dati hanno un campo di filtro del tempo, è possibile specificare l'intervallo di tempo. Nella parte superiore destra della pagina impostare un filtro temporale. Per impostazione predefinita, l'individuazione Mostra i dati per gli ultimi 15 minuti.

   ![Filtro temporale](media/k2bridge/k2bridge-time-filter.png)
    
1. La tabella dei risultati Mostra i primi 500 record. È possibile espandere un documento per esaminare i dati del campo in formato JSON o tabella.

   ![Espandi record a](media/k2bridge/k2bridge-expand-record.png)

1. Per impostazione predefinita, la tabella dei risultati include le colonne per il documento _source e il campo relativo all'ora (se esistente). È possibile scegliere colonne specifiche da aggiungere alla tabella dei risultati selezionando **Aggiungi** accanto al nome del campo nella barra laterale sinistra.

   ![Colonne specifiche](media/k2bridge/k2bridge-specific-columns.png)
    
1. Nella barra di query è possibile eseguire ricerche nei dati in base a quanto segue:
    * Immissione di un termine di ricerca
    * Uso della sintassi di query Lucene. 
    Ad esempio:
        * Cercare "Error" per trovare tutti i record che contengono questo valore. 
        * Cercare "status: 200" per ottenere tutti i record con un valore di stato 200. 
    * Utilizzo di operatori logici (AND, OR, NOT)
    * Utilizzo di caratteri jolly (asterisco "\*" o punto interrogativo "?") Per esempio:
        * La query `"destination_city: L*"` corrisponderà ai record in cui il valore della città di destinazione inizia con "l" (K2Bridge non fa distinzione tra maiuscole e minuscole).

    ![Esegui query](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > Nella [ricerca](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)è possibile trovare altre regole di ricerca e logica.

1. Per filtrare i risultati della ricerca, usare l' **elenco dei campi** sulla barra laterale destra della pagina. 
    L'elenco dei campi è il punto in cui è possibile visualizzare:
    * I primi cinque valori per il campo
    * Il numero di record che contengono il campo
    * Percentuale di record che contengono ogni valore. 
    
    >[!Tip]
    > Usare l'icona di ingrandimento (+) per trovare tutti i record con un valore specifico.
    
    ![Elenco dei campi](media/k2bridge/k2bridge-field-list.png)
   
    È anche possibile filtrare i risultati usando l'icona di ingrandimento (+) nella visualizzazione del formato della tabella dei risultati di ogni record nella tabella dei risultati.
    
     ![Elenco tabelle](media/k2bridge/k2bridge-table-list.png)
    
1. Selezionare questa opzione per **salvare** o **condividere** la ricerca.

     ![Salva la ricerca](media/k2bridge/k2bridge-save-search.png)
