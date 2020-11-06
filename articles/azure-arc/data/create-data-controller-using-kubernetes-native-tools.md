---
title: Creare un controller dati usando gli strumenti di Kubernetes
description: Creare un controller dati usando gli strumenti di Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 051a7f506d351a17764e38c760ffba06d224cc38
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422570"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Creare un controller di dati di Azure ARC usando gli strumenti di Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Vedere l'argomento [creare il controller dati di Azure Arc](create-data-controller.md) per informazioni generali.

Per creare il controller di dati di Azure ARC usando gli strumenti di Kubernetes, è necessario che siano installati gli strumenti di Kubernetes.  Gli esempi in questo articolo useranno `kubectl` , ma approcci simili potrebbero essere usati con altri strumenti Kubernetes, ad esempio il dashboard di Kubernetes, `oc` o `helm` se si ha familiarità con questi strumenti e Kubernetes YAML/JSON.

[Installare lo strumento kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Alcuni dei passaggi per creare il controller di dati di Azure Arc indicati di seguito richiedono le autorizzazioni di amministratore del cluster Kubernetes.  Se non si è un amministratore del cluster Kubernetes, è necessario che l'amministratore del cluster Kubernetes esegua questi passaggi per conto dell'utente.

### <a name="cleanup-from-past-installations"></a>Pulizia dalle installazioni precedenti

Se il controller dati di Azure Arc è stato installato in passato, nello stesso cluster ed è stato eliminato il controller di Azure Arc data controller usando il `azdata arc dc delete` comando, potrebbe essere necessario eliminare alcuni oggetti a livello di cluster. Eseguire i comandi seguenti per eliminare gli oggetti a livello di cluster del controller di dati di Azure Arc:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

## <a name="overview"></a>Panoramica

La creazione del controller di dati di Azure Arc prevede i passaggi di alto livello seguenti:
1. Creare le definizioni di risorse personalizzate per il controller di dati Arc, l'istanza gestita di SQL di Azure e la scalabilità di PostgreSQL. **[Richiede autorizzazioni di amministratore del cluster Kubernetes]**
2. Creare uno spazio dei nomi in cui verrà creato il controller dati. **[Richiede autorizzazioni di amministratore del cluster Kubernetes]**
3. Creare il servizio del programma di avvio automatico, inclusi il set di repliche, l'account del servizio, il ruolo e l'associazione di ruolo
4. Creare un segreto per il nome utente e la password dell'amministratore del controller di dati.
5. Creare il controller di dati.
   
## <a name="create-the-custom-resource-definitions"></a>Creare le definizioni di risorse personalizzate

Eseguire il comando seguente per creare le definizioni di risorse personalizzate.  **[Richiede autorizzazioni di amministratore del cluster Kubernetes]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Creare uno spazio dei nomi in cui verrà creato il controller dati

Eseguire un comando simile al seguente per creare un nuovo spazio dei nomi dedicato in cui verrà creato il controller dati.  In questo esempio e il resto degli esempi in questo articolo verrà usato il nome di uno spazio dei nomi `arc` . Se si sceglie di usare un nome diverso, usare lo stesso nome in tutto.

```console
kubectl create namespace arc
```

Se altri utenti utilizzeranno questo spazio dei nomi che non sono amministratori del cluster, è consigliabile creare un ruolo di amministratore dello spazio dei nomi e concedere tale ruolo a tali utenti tramite un'associazione di ruolo.  L'amministratore dello spazio dei nomi deve disporre delle autorizzazioni complete per lo spazio dei nomi.  Più avanti verranno fornite ulteriori informazioni su come fornire agli utenti un accesso più granulare basato sui ruoli.

## <a name="create-the-bootstrapper-service"></a>Creare il servizio del programma di avvio automatico

Il servizio del programma di avvio automatico gestisce le richieste in ingresso per la creazione, la modifica e l'eliminazione di risorse personalizzate, ad esempio un controller di dati, un'istanza gestita di SQL o un gruppo di server di scalabilità di PostgreSQL.

Eseguire il comando seguente per creare un servizio del programma di avvio automatico, un account del servizio per il servizio del programma di avvio automatico e un ruolo e un'associazione di ruolo per l'account del servizio del programma di avvio automatico.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Verificare che il pod del programma di avvio automatico sia in esecuzione usando il comando seguente.  Potrebbe essere necessario eseguirlo alcune volte fino a quando lo stato non diventa `Running` .

```console
kubectl get pod --namespace arc
```

Per impostazione predefinita, il file di modello del programma di avvio automatico. YAML estrae l'immagine del contenitore del programma di avvio automatico da Microsoft Container Registry.  Se l'ambiente non ha accesso diretto al Container Registry Microsoft, è possibile eseguire le operazioni seguenti:
- Seguire i passaggi per [estrarre le immagini del contenitore dal container Registry Microsoft e inserirle in un registro contenitori privato](offline-deployment.md).
- [Creare un segreto di pull immagine](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) per il registro contenitori privato.
- Aggiungere un'immagine di pull Secret al contenitore del programma di avvio automatico. Vedi l'esempio seguente.
- Modificare il percorso dell'immagine per l'immagine del programma di avvio automatico. Vedi l'esempio seguente.

Nell'esempio seguente si presuppone che sia stato creato un nome del segreto di pull immagine `regcred` come indicato nella documentazione di Kubernetes.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-oct-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Creare un segreto per l'amministratore del controller di dati

Il nome utente e la password dell'amministratore del controller dati vengono usati per eseguire l'autenticazione all'API del controller dei dati per eseguire le funzioni amministrative.  Scegliere una password sicura e condividerla con solo quelle che devono disporre dei privilegi di amministratore del cluster.

Un segreto Kubernetes viene archiviato come stringa con codifica Base64, uno per il nome utente e uno per la password.

È possibile usare uno strumento online per codificare con Base64 il nome utente e la password desiderati oppure è possibile usare gli strumenti dell'interfaccia della riga di comando predefiniti a seconda della piattaforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

Dopo aver codificato il nome utente e la password, è possibile creare un file in base al [file modello](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) e sostituire i valori di nome utente e password con quelli personalizzati.

Eseguire quindi il comando seguente per creare il segreto.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Creare il controller dati

A questo punto si è pronti per creare il controller di dati stesso.

Per prima cosa, creare una copia del [file modello](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) in locale nel computer in modo da poter modificare alcune impostazioni.

Modificare quanto segue in base alle esigenze:

**Obbligatorio**
- **località** : modificare il percorso in modo che sia il percorso di Azure in cui verranno archiviati i _metadati_ sul controller dati.  È possibile visualizzare l'elenco delle località di Azure disponibili nell'articolo [sulla panoramica della creazione](create-data-controller.md) di un controller di dati.
- **resourceGroup** : il gruppo di risorse di Azure in cui si vuole creare la risorsa di Azure per il controller dati in Azure Resource Manager.  In genere questo gruppo di risorse deve esistere già, ma non è necessario fino al momento in cui si caricano i dati in Azure.
- **Subscription (sottoscrizione** ): GUID della sottoscrizione di Azure per la sottoscrizione in cui si vogliono creare le risorse di Azure.

**CONSIGLIATO PER RIVEDERE ED EVENTUALMENTE MODIFICARE LE IMPOSTAZIONI PREDEFINITE**
- **archiviazione.. NomeClasse** : classe di archiviazione da usare per i file di dati e di log del controller di dati.  Se non si è certi delle classi di archiviazione disponibili nel cluster Kubernetes, è possibile eseguire il comando seguente: `kubectl get storageclass` .  Il valore predefinito è `default` che presuppone la presenza di una classe di archiviazione esistente e con il nome di `default` una classe di archiviazione _is_ predefinita.  Nota: sono disponibili due impostazioni di NomeClasse da impostare sulla classe di archiviazione desiderata, una per i dati e una per i log.
- **serviceType** : modificare il tipo di servizio in `NodePort` se non si usa un LoadBalancer.  Nota: sono disponibili due impostazioni serviceType che è necessario modificare.

**OPZIONALE**
- **nome** : il nome predefinito del controller dati è `arc` , ma è possibile modificarlo se lo si desidera.
- **DisplayName** : impostare questa impostazione sullo stesso valore dell'attributo Name all'inizio del file.
- **Registro di sistema** : il container Registry Microsoft è il valore predefinito.  Se si stanno estraendo le immagini dal Container Registry Microsoft e ne viene eseguito il [push in un registro contenitori privato](offline-deployment.md), immettere l'indirizzo IP o il nome DNS del registro di sistema.
- **dockerRegistry** : il segreto di pull dell'immagine da usare per estrarre le immagini da un registro contenitori privato, se necessario.
- **repository** : il repository predefinito in Microsoft container Registry è `arcdata` .  Se si usa un registro contenitori privato, immettere il percorso della cartella o del repository contenente le immagini del contenitore di servizi dati abilitati per Azure arr.
- **imageTag** : il tag della versione più recente corrente viene impostato come valore predefinito nel modello, ma è possibile modificarlo se si vuole usare una versione precedente.

Esempio di file YAML del controller di dati completato:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-oct-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Salvare il file modificato nel computer locale ed eseguire il comando seguente per creare il controller dati:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome `arc` .  Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire `arc` con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Estensione di Azure Arc per Azure Data Studio fornisce un notebook per illustrare l'esperienza di configurazione di Azure Arc Enabled Kubernetes e configurarlo per il monitoraggio di un repository git contenente un file SQL Istanza gestita YAML di esempio. Quando tutto è connesso, viene distribuito un nuovo Istanza gestita SQL nel cluster Kubernetes.

Per Azure Data Studio, vedere la pagina relativa alla **distribuzione di un istanza gestita SQL con Azure Arc Enabled Kubernetes e Flux** notebook nell'estensione Azure Arc.

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'istanza gestita di SQL con Kubernetes-native Tools](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Creare un gruppo di server con iperscalabilità PostgreSQL usando Kubernetes-native Tools](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
