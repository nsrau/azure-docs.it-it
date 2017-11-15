---
title: Monitorare i servizi e le applicazioni di Azure mediante Grafana | Microsoft Docs
description: Indirizzare dati di Monitoraggio di Azure e Application Insights i modo da poterli vedere in Grafana.
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 709a98f8bcdb75962f8e41de348ca7a41c677610
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorare i servizi di Azure in Grafana
Ora è possibile monitorare i servizi e le applicazioni di Azure da [Grafana](https://grafana.com/) usando il [plug-in dell'origine dati di Monitoraggio di Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). Il plug-in raccoglie i dati sulle prestazioni dell'applicazione raccolti da Application Insights SDK, nonché i dati di infrastruttura forniti da Monitoraggio di Azure. È quindi possibile visualizzare questi dati nel dashboard di Grafana.

Il plug-in è attualmente in fase di anteprima.

Usare la procedura seguente per configurare un server Grafana da Azure Marketplace e creare dashboard per le metriche da Monitoraggio di Azure e Application Insights.

## <a name="set-up-a-grafana-instance"></a>Configurare un'istanza di Grafana
1. Passare ad Azure Marketplace e selezionare Grafana di Grafana Labs.

2. Compilare i nomi e i dettagli. Creare un nuovo gruppo di risorse. Tenere traccia dei valori scelti per il nome utente della VM, la password della VM e la password di amministratore del server Grafana.  

3. Scegliere la dimensione della VM e un account di archiviazione.

4. Scegliere le impostazioni di configurazione della rete.

5. Visualizzare il riepilogo e selezionare **Crea** dopo aver accettato le condizioni di utilizzo.

## <a name="log-in-to-grafana"></a>Accedere a Grafana
1. Al termine della distribuzione selezionare **Vai al gruppo di risorse**. Viene visualizzato un elenco di risorse appena create. 

    ![Oggetti gruppo di risorse di Grafana](.\media\monitor-how-to-grafana\grafana1.png) 

    Se si seleziona il gruppo di sicurezza di rete (*grafana-nsg* in questo caso), è possibile vedere che la porta 3000 viene usata per accedere al server Grafana. 

2. Tornare all'elenco di risorse e selezionare l'**indirizzo IP pubblico**. Usando i valori contenuti in questa schermata, digitare *http://<IP address>:3000* o *<DNSName>:3000* nel browser. Compare una pagina di accesso per il server Grafana appena compilato.
    
    ![Schermata di accesso di Grafana](.\media\monitor-how-to-grafana\grafana2.png) 

3. Accedere con il nome utente *admin* e la password di amministratore del server Grafana creata in precedenza. 

## <a name="configure-data-source-plugin"></a>Configurare il plug-in di origine dati

Una volta eseguito l'accesso, si dovrebbe vedere che il plug-di origine dati di Monitoraggio di Azure è già incluso.

![Grafana mostra il plug-in Monitoraggio di Azure](.\media\monitor-how-to-grafana\grafana3.png) 

1. Selezionare **Aggiungi origine dati** per configurare Monitoraggio di Azure e Application Insights. 
    
2. Scegliere un nome per l'origine dati e selezionare **Monitoraggio di Azure** come origine dati nell'elenco a discesa.
    
    
## <a name="create-a-service-principal"></a>Creare un’entità servizio 

Grafana usa un'entità servizio di Azure Active Directory per connettersi alle API di Monitoraggio di Azure e raccogliere i dati delle metriche. È necessario creare un'entità servizio per gestire l'accesso alle risorse di Azure.

1. Per creare un'entità servizio, seguire [queste istruzioni](../azure-resource-manager/resource-group-create-service-principal-portal.md). Copiare e salvare l'ID tenant, l'ID client e un segreto client.

2. Per informazioni su come assegnare il ruolo di lettore all'applicazione Azure Active Directory, vedere [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role).   

3. Se si usa Application Insights, è possibile anche includere l'API di Application Insights e l'ID applicazione per raccogliere le metriche basate su Application Insights. Per altre informazioni, vedere [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Ottenere la chiave API e l'ID applicazione).

4. Dopo aver immesso tutte queste informazioni, selezionare **Salva** in modo che Grafana testi l'API. Verrà visualizzato un messaggio simile al seguente.  

    ![Grafana mostra il plug-in Monitoraggio di Azure](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Creare un dashboard di Grafana

1. Passare a Home e selezionare **Nuovo dashboard**.

2. Nel nuovo dashboard selezionare il **Grafico**. È possibile provare altre opzioni di creazione di grafici, ma questo articolo usa *Grafico* come esempio. 

    ![Nuovo dashboard di Grafana](.\media\monitor-how-to-grafana\grafana5.png) 

3. Un grafico vuoto viene visualizzato nel dashboard. 

4. Fare clic sul titolo del pannello e selezionare **Modifica** per immettere i dettagli dei dati che si desidera tracciare in questo grafico.
    
5. Dopo aver selezionato tutte le VM giuste, è possibile iniziare a visualizzare le metriche nel dashboard. 

Di seguito è mostrato un semplice dashboard con due grafici. Quello a sinistra mostra la percentuale di CPU di due VM. Il grafico a destra mostra le transazioni in un account Archiviazione di Azure ripartite per tipo di transazione API.
    
![Esempio di Grafana con due grafici](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Facoltativo: Creare playlist di dashboard

Una delle molte funzionalità utili di Grafana è la playlist di dashboard. È possibile creare più dashboard e aggiungerli a una playlist configurando un intervallo per ogni dashboard da visualizzare. Selezionare **Play** per vedere i dashboard scorrere in sequenza. È consigliabile visualizzarli su un monitor a parete di grandi dimensioni per fornire un "tabellone di stato" per il gruppo. 
    
![Esempio di playlist di Grafana](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Facoltativo: Monitorare le metriche personalizzate nello stesso server Grafana

È anche possibile installare Telegraf e InfluxDB per raccogliere e monitorare metriche personalizzate e basate su agenti nella stessa istanza di Grafana. Esistono molti plug-in di origine dati che possono essere usati per riunire queste metriche in un dashboard. 
    
È anche possibile riutilizzare questa configurazione per includere le metriche del server Prometheus. Usare il plug-in di origine dati di Prometheus nella raccolta di plug-in di Grafana.
    
Di seguito sono riportati utili articoli di riferimento su come usare Telegraf, InfluxDB, Prometheus e Docker
 - [Come monitorare le metriche di sistema con lo stack TICK in Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorare le metriche delle risorse Docker con Grafana InfluxDB e Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Una soluzione di monitoraggio per host, contenitori e servizi in contenitori Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Di seguito è riportata un'immagine di un dashboard di Grafana completo che include metriche di monitoraggio di Azure e Application Insights.
![Metriche di Grafana di esempio](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Pulire le risorse

L'addebito si verifica quando le VM sono in esecuzione, sia che vengano usate o meno. Per evitare di dover sostenere costi aggiuntivi, eliminare il gruppo di risorse creato in questo articolo. 

1. Scegliere **Gruppi di risorse** nel menu a sinistra del portale di Azure e quindi fare clic su **Grafana**. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **Grafana** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica delle metriche di Monitoraggio di Azure](monitoring-overview-metrics.md)


