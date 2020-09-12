---
title: Distribuire un gateway self-hosted in Kubernetes | Microsoft Docs
description: Informazioni su come distribuire un componente gateway self-hosted di gestione API di Azure in Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500131"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Distribuire un gateway self-hosted in Kubernetes

Questo articolo descrive i passaggi per la distribuzione del componente gateway self-hosted di gestione API di Azure in un cluster Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Creare un cluster Kubernetes.
   > [!TIP]
   > I [cluster a nodo singolo](https://kubernetes.io/docs/setup/#learning-environment) funzionano correttamente per scopi di sviluppo e valutazione. Usare cluster multinodo [certificati Kubernetes](https://kubernetes.io/partners/#conformance) in locale o nel cloud per i carichi di lavoro di produzione.
- Effettuare il [provisioning di una risorsa del gateway self-hosted nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Eseguire la distribuzione in Kubernetes

1. Selezionare **gateway** in **distribuzione e infrastruttura**.
2. Selezionare la risorsa del gateway self-hosted che si vuole distribuire.
3. Selezionare **distribuzione**.
4. Un token di accesso nella casella di testo **token** è stato generato automaticamente, in base ai valori predefiniti per la **scadenza** e la **chiave privata** . Se necessario, scegliere i valori in uno o entrambi i controlli per generare un nuovo token.
5. Selezionare la scheda **Kubernetes** in **script di distribuzione**.
6. Selezionare il collegamento al file con estensione ** \<gateway-name\> yml** e scaricare il file YAML.
7. Selezionare l'icona **copia** nell'angolo inferiore destro della casella di testo **Distribuisci** per salvare i `kubectl` comandi negli Appunti.
8. Incollare i comandi nella finestra del terminale o del comando. Il primo comando crea un segreto Kubernetes che contiene il token di accesso generato nel passaggio 4. Il secondo comando applica il file di configurazione scaricato nel passaggio 6 al cluster Kubernetes e prevede che il file si trovi nella directory corrente.
9. Eseguire i comandi per creare gli oggetti Kubernetes necessari nello [spazio dei nomi predefinito](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e avviare i pod del gateway self-hosted dall' [immagine del contenitore](https://aka.ms/apim/sputnik/dhub) scaricata da Microsoft container Registry.
10. Eseguire il comando seguente per verificare se la distribuzione è riuscita. Si noti che potrebbe essere necessario un po' di tempo per la creazione di tutti gli oggetti e per l'inizializzazione dei pod.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Eseguire il comando seguente per verificare se il servizio è stato creato correttamente. Si noti che gli indirizzi IP e le porte del servizio saranno diversi.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Tornare alla portale di Azure e selezionare Overview ( **Panoramica**).
13. Verificare che **lo stato** mostri un segno di spunta verde, seguito da un numero di nodi che corrisponde al numero di repliche specificato nel file YAML. Questo stato indica che i pod del gateway self-hosted distribuiti sono in grado di comunicare correttamente con il servizio gestione API e hanno un normale "heartbeat".

    ![Stato del gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Eseguire il <code>kubectl logs deployment/<gateway-name></code> comando per visualizzare i log da un pod selezionato in modo casuale se ne sono presenti più di uno.
> Eseguire <code>kubectl logs -h</code> per un set completo di opzioni di comando, ad esempio come visualizzare i log per un pod o un contenitore specifico.

## <a name="production-deployment-considerations"></a>Considerazioni sulla distribuzione di produzione

### <a name="access-token"></a>Token di accesso
Senza un token di accesso valido, un gateway self-hosted non può accedere ai dati di configurazione e scaricarli dall'endpoint del servizio gestione API associato. Il token di accesso può essere valido per un massimo di 30 giorni. Deve essere rigenerata e il cluster configurato con un token aggiornato, manualmente o tramite automazione, prima della scadenza.

Quando si automatizza l'aggiornamento del token, usare [questa operazione dell'API di gestione](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) per generare un nuovo token. Per informazioni sulla gestione dei segreti Kubernetes, vedere il [sito Web Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Spazio dei nomi
Gli [spazi dei nomi](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes consentono di dividere un singolo cluster tra più team, progetti o applicazioni. Gli spazi dei nomi forniscono un ambito per le risorse e i nomi. Possono essere associati a criteri di quota di risorse e di controllo di accesso.

Il portale di Azure fornisce i comandi per creare risorse del gateway Self-Hosted nello spazio dei nomi **predefinito** . Questo spazio dei nomi viene creato automaticamente, esiste in ogni cluster e non può essere eliminato.
Valutare la possibilità di [creare e distribuire](https://kubernetesbyexample.com/ns/) un gateway self-hosted in uno spazio dei nomi separato nell'ambiente di produzione.

### <a name="number-of-replicas"></a>Numero di repliche
Il numero minimo di repliche adatte per la produzione è due.

Per impostazione predefinita, un gateway self-hosted viene distribuito con una [strategia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)di distribuzione **RollingUpdate** . Esaminare i valori predefiniti e prendere in considerazione la possibilità di impostare in modo esplicito i campi [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , soprattutto quando si usa un numero di repliche elevato.

### <a name="container-resources"></a>Risorse del contenitore
Per impostazione predefinita, il file YAML fornito nella portale di Azure non specifica le richieste di risorse del contenitore.

È impossibile prevedere e consigliare in modo affidabile la quantità di risorse di memoria e CPU per contenitore e il numero di repliche necessarie per supportare un carico di lavoro specifico. Molti fattori sono in gioco, ad esempio:

- Hardware specifico su cui è in esecuzione il cluster.
- Presenza e tipo di virtualizzazione.
- Numero e frequenza delle connessioni client simultanee.
- Frequenza delle richieste.
- Tipo e numero di criteri configurati.
- Dimensioni del payload e se i payload vengono memorizzati nel buffer o trasmessi in flusso.
- Latenza del servizio back-end.

È consigliabile impostare le richieste di risorse su due core e 2 GiB come punto di partenza. Eseguire un test di carico e aumentare o ridurre le prestazioni o il ridimensionamento in base ai risultati.

### <a name="container-image-tag"></a>Tag dell'immagine del contenitore
Il file YAML fornito nella portale di Azure usa il tag **più recente** . Questo tag fa sempre riferimento alla versione più recente dell'immagine del contenitore del gateway self-hosted.

Provare a usare un tag di versione specifico in produzione per evitare l'aggiornamento accidentale a una versione più recente.

È possibile [scaricare un elenco completo dei tag disponibili](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>Criteri DNS
La risoluzione dei nomi DNS gioca un ruolo fondamentale nella capacità di un gateway self-hosted di connettersi alle dipendenze in Azure e inviare le chiamate API ai servizi back-end.

Il file YAML fornito nella portale di Azure applica i criteri predefiniti di [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) . Questo criterio fa sì che le richieste di risoluzione dei nomi non risolte dal DNS del cluster vengano inviate al server DNS upstream ereditato dal nodo.

Per informazioni sulla risoluzione dei nomi in Kubernetes, vedere il [sito Web Kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Provare a personalizzare i [criteri DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) o la [configurazione DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) nel modo appropriato per la configurazione.

### <a name="external-traffic-policy"></a>Criteri di traffico esterno
Il file YAML fornito nel campo portale di Azure imposta nell' `externalTrafficPolicy` oggetto [servizio](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) su `Local` . Questo consente di mantenere l'indirizzo IP del chiamante (accessibile nel [contesto della richiesta](api-management-policy-expressions.md#ContextVariables)) e di disabilitare il bilanciamento del carico tra nodi, eliminando gli hop di rete causati dall'it. Tenere presente che questa impostazione potrebbe causare la distribuzione asimmetrica del traffico nelle distribuzioni con un numero diverso di Pod del gateway per ogni nodo.

### <a name="custom-domain-names-and-ssl-certificates"></a>Nomi di dominio personalizzati e certificati SSL

Se si usano nomi di dominio personalizzati per gli endpoint di gestione API, soprattutto se si usa un nome di dominio personalizzato per l'endpoint di gestione, potrebbe essere necessario aggiornare il valore di `config.service.endpoint` nel file ** \<gateway-name\> YAML** per sostituire il nome di dominio predefinito con il nome di dominio personalizzato. Verificare che sia possibile accedere all'endpoint di gestione dal Pod del gateway self-hosted nel cluster Kubernetes.

In questo scenario, se il certificato SSL usato dall'endpoint di gestione non è firmato da un certificato CA noto, è necessario assicurarsi che il certificato della CA sia considerato attendibile dal Pod del gateway self-hosted.

### <a name="configuration-backup"></a>Backup configurazione
Per informazioni sul comportamento del gateway self-hosted in presenza di un'interruzione temporanea della connettività di Azure, vedere [Panoramica del gateway self-hosted](self-hosted-gateway-overview.md#connectivity-to-azure).

Configurare un volume di archiviazione locale per il contenitore del gateway self-hosted, in modo che possa rendere permanente una copia di backup dell'ultima configurazione scaricata. Se la connettività è inattiva, il volume di archiviazione può utilizzare la copia di backup al riavvio. Il percorso di montaggio del volume deve essere <code>/apim/config</code> . Vedere un esempio su [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Per informazioni sull'archiviazione in Kubernetes, vedere il [sito Web Kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Metriche e log locali
Il gateway self-hosted invia dati di telemetria a [monitoraggio di Azure](api-management-howto-use-azure-monitor.md) e [applicazione Azure Insights](api-management-howto-app-insights.md) in base alle impostazioni di configurazione nel servizio gestione API associato.
Quando la [connettività ad Azure](self-hosted-gateway-overview.md#connectivity-to-azure) viene persa temporaneamente, il flusso dei dati di telemetria in Azure viene interrotto e i dati vengono persi per la durata dell'interruzione.
Provare a [configurare il monitoraggio locale](how-to-configure-local-metrics-logs.md) per garantire la possibilità di osservare il traffico API ed evitare la perdita di dati di telemetria durante le interruzioni della connettività di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Cenni preliminari sul gateway](self-hosted-gateway-overview.md)indipendente.
