---
title: Distribuire il gateway self-hosted in Kubernetes | Microsoft Docs
description: Informazioni su come distribuire un componente gateway self-hosted di gestione API di Azure in Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205105"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Distribuire il gateway self-hosted in Kubernetes

Questo articolo descrive i passaggi per la distribuzione del componente gateway self-hosted di gestione API di Azure in un cluster Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Completare la Guida introduttiva seguente: [creare un'istanza di gestione API di Azure](get-started-create-service-instance.md)
- Creare un cluster Kubernetes.
> [!TIP]
> I [cluster a nodo singolo](https://kubernetes.io/docs/setup/#learning-environment) funzionano correttamente per scopi di sviluppo e valutazione. Usare cluster multinodo [certificati Kubernetes](https://kubernetes.io/partners/#conformance) in locale o nel cloud per i carichi di lavoro di produzione.
- Effettuare il [provisioning di una risorsa del gateway self-hosted nell'istanza di gestione API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Eseguire la distribuzione in Kubernetes

1. Selezionare **gateway** da **distribuzione e infrastruttura**.
2. Selezionare la risorsa del gateway self-hosted che si intende distribuire.
3. Selezionare **distribuzione**.
4. Si noti che un token di accesso nella casella di testo **token** è stato generato automaticamente usando i valori predefiniti per la **scadenza** e la **chiave privata** . Se necessario, selezionare i valori desiderati in uno o entrambi i controlli per generare un nuovo token.
5. Selezionare la scheda **Kubernetes** in **script di distribuzione**.
6. Fare clic sul collegamento **<gateway-name>. yml** e scaricare il file YAML.
7. Selezionare l'icona di **copia** nell'angolo inferiore destro della casella di testo **Distribuisci** per salvare `kubectl` i comandi negli Appunti.
8. Incollare i comandi nella finestra del terminale o del comando. Il primo comando crea un segreto Kubernetes contenente il token di accesso generato nel passaggio 4. Il secondo comando applica il file di configurazione scaricato nel passaggio 6 al cluster Kubernetes e prevede che il file sia presente nella directory corrente.
9. Eseguire i comandi per creare gli oggetti Kubernetes necessari nello [spazio dei nomi predefinito](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e avviare i pod del gateway self-hosted da un' [immagine del contenitore](https://aka.ms/apim/sputnik/dhub) scaricata da Microsoft container Registry.
10. Esecuzione del comando visualizzato sotto per verificare se la distribuzione è riuscita. Si noti che potrebbe essere necessario un po' di tempo per la creazione di tutti gli oggetti e per i pod da inizializzare.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Esecuzione del comando visualizzato sotto per verificare se il servizio è stato creato correttamente. Si noti che gli indirizzi IP e le porte del servizio saranno diversi.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Tornare alla portale di Azure e selezionare Overview ( **Panoramica**).
13. **Lo stato** che mostra un'icona di segno di spunta verde seguito dal numero di nodi che corrisponde al numero di repliche specificato nel file YAML conferma che i pod del gateway self-hosted distribuiti comunicano correttamente con il servizio gestione API e hanno un normale "heartbeat".

![stato del gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Eseguire <code>kubectl logs deployment/<gateway-name></code> il comando per visualizzare i log da un pod selezionato in modo casuale se ne sono presenti più di uno.
> Eseguire <code>kubectl logs -h</code> per un set completo di opzioni di comando, ad esempio come visualizzare i log per un pod o un contenitore specifico.

## <a name="production-deployment-considerations"></a>Considerazioni sulla distribuzione di produzione

### <a name="access-token"></a>Token di accesso
Senza un token di accesso valido il gateway self-hosted non è in grado di accedere e scaricare la configurazione dall'endpoint dei dati di configurazione del servizio gestione API associato. Il token di accesso può essere valido per un massimo di 30 giorni. Deve essere rigenerato e il cluster configurato con un token aggiornato manualmente o tramite l'automazione prima della scadenza. Quando si automatizza l'aggiornamento del token, usare questa [operazione](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) dell'API di gestione per generare un nuovo token. Seguire questo [collegamento](https://kubernetes.io/docs/concepts/configuration/secret) per informazioni sulla gestione dei segreti Kubernetes.

### <a name="namespace"></a>Spazio dei nomi
Gli [spazi dei nomi](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes consentono di dividere un singolo cluster tra più team, progetti o applicazioni. Gli spazi dei nomi forniscono un ambito per le risorse e i nomi e possono essere associati a criteri di quota di risorse e di controllo di accesso.
I comandi disponibili nella portale di Azure creare risorse del gateway Self-Hosted nello spazio dei nomi **predefinito** , che viene creato automaticamente, esiste in ogni cluster e non può essere eliminato.
Valutare la possibilità di [creare e distribuire](https://kubernetesbyexample.com/ns/) il gateway self-hosted in uno spazio dei nomi separato in produzione.

### <a name="number-of-replicas"></a>Numero di repliche
Il numero minimo di repliche idonee per la produzione è due.

Per impostazione predefinita, il gateway self-hosted viene distribuito con una [strategia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)di distribuzione **RollingUpdate** . Esaminare i valori predefiniti e prendere in considerazione l'impostazione esplicita dei campi [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , soprattutto quando si usa un numero di repliche elevato.

### <a name="container-resources"></a>Risorse del contenitore
Per impostazione predefinita, il file YAML fornito nella portale di Azure non specifica le richieste di risorse del contenitore.

È impossibile prevedere e consigliare in modo affidabile la quantità di risorse di memoria e CPU per contenitore e il numero di repliche necessarie per supportare un carico di lavoro specifico a causa di molti fattori in gioco, ad esempio:

- Hardware specifico in cui è in esecuzione il cluster
- Presenza e tipo di virtualizzazione
- Numero e frequenza delle connessioni client simultanee
- Frequenza di richieste
- Tipo e numero di criteri configurati
- Dimensioni del payload e se i payload vengono memorizzati nel buffer o trasmessi in flusso
- Latenza del servizio back-end

Si consiglia di impostare la richiesta di risorse su 2 core e 2 GiB come punto di partenza, eseguendo un test di carico e scalando in verticale o in basso o in base ai risultati.

### <a name="container-image-tag"></a>Tag dell'immagine del contenitore
Il file YAML fornito nella portale di Azure usa il tag **più recente** che fa sempre riferimento alla versione più recente dell'immagine del contenitore del gateway self-hosted.

Provare a usare un tag di versione specifico in produzione per evitare l'aggiornamento accidentale a una versione più recente.

Seguire questo [collegamento](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) per visualizzare l'elenco completo dei tag disponibili.

### <a name="dns-policy"></a>Criteri DNS
La risoluzione dei nomi DNS gioca un ruolo fondamentale nella capacità del gateway self-hosted di connettersi alle dipendenze in Azure e inviare le chiamate API ai servizi back-end.

Il file YAML fornito nella portale di Azure applica i criteri predefiniti di [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) , che causano l'invio delle richieste di risoluzione dei nomi non risolte dal DNS del cluster al server DNS upstream ereditato dal nodo.

Seguire questo [collegamento](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) per informazioni sulla risoluzione dei nomi in Kubernetes e prendere in considerazione la personalizzazione dei [criteri DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) o della[configurazione di NS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) in base alle esigenze della configurazione.

### <a name="configuration-backup"></a>Backup configurazione
Seguire questo [collegamento](self-hosted-gateway-overview.md#connectivity-to-azure) per informazioni sul comportamento del gateway self-hosted in presenza di un'interruzione temporanea della connettività di Azure.
Configurare il volume di archiviazione locale per il contenitore del gateway self-hosted, in modo che possa rendere permanente una copia di backup dell'ultima configurazione scaricata e, se la connettività non è attiva, usarla al riavvio. Il percorso di montaggio del volume <code>/apim/config</code>deve essere. Vedere un esempio [qui](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Per informazioni sull'archiviazione in Kubernetes, seguire questo [collegamento](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Metriche e log locali
Il gateway self-hosted invia dati di telemetria a [monitoraggio di Azure](api-management-howto-use-azure-monitor.md) e [applicazione Azure informazioni dettagliate](api-management-howto-app-insights.md) per ogni impostazione di configurazione nel servizio gestione API associato.
Quando la [connettività ad Azure](self-hosted-gateway-overview.md#connectivity-to-azure) viene persa temporaneamente, il flusso dei dati di telemetria in Azure viene interrotto e i dati vengono persi per la durata dell'interruzione.
Si consiglia di [configurare il monitoraggio locale](how-to-configure-local-metrics-logs.md) per garantire la possibilità di osservare il traffico API ed evitare la perdita di dati di telemetria durante le interruzioni della connettività di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)