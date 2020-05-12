---
title: Usare Helm per integrare Configurazione app di Azure con la distribuzione di Kubernetes
description: Informazioni su come usare le configurazioni dinamiche nella distribuzione di Kubernetes con Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2aebccdf18aaba345beb344a8b6fc3b37754a4a1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793265"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrare la distribuzione di Kubernetes con Helm

Helm consente di definire, installare e aggiornare le applicazioni in esecuzione in Kubernetes. Un grafico Helm contiene le informazioni necessarie per creare un'istanza di un'applicazione Kubernetes. La configurazione viene archiviata all'esterno del grafico stesso, in un file denominato *values.yaml*. 

Durante il processo di rilascio, Helm unisce il grafico alla configurazione corretta per l'esecuzione dell'applicazione. È ad esempio possibile fare riferimento alle variabili definite in *values.yaml* come variabili di ambiente all'interno dei contenitori in esecuzione. Helm supporta anche la creazione di segreti Kubernetes, che possono essere montati come volumi di dati o esposti come variabili di ambiente.

È possibile eseguire l'override dei valori archiviati in *values.yaml* specificando altri file di configurazione basati su YAML sulla riga di comando durante l'esecuzione di Helm. Configurazione app di Azure supporta l'esportazione di valori di configurazione in file YAML. L'integrazione di questa funzionalità di esportazione nella distribuzione consente alle applicazioni Kubernetes di sfruttare i valori di configurazione archiviati in Configurazione app.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Usare i valori di Configurazione app quando si distribuisce un'applicazione in Kubernetes con Helm.
> * Creare un segreto Kubernetes in base a un riferimento a Key Vault in Configurazione app.

Questa esercitazione presuppone una conoscenza di base della gestione di Kubernetes con Helm. Per altre informazioni sull'installazione di applicazioni con Helm, vedere [Servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-helm).

## <a name="prerequisites"></a>Prerequisiti

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (2.4.0 o versioni successive)
- Installare [Helm](https://helm.sh/docs/intro/install/) (2.14.0 o versioni successive)
- Un cluster Kubernetes.

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Esplora configurazioni** > **Crea** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | Valore |
    |---|---|
    | settings.color | bianco |
    | settings.message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Aggiungere a Configurazione app un riferimento a Key Vault
1. Accedere al [portale di Azure](https://portal.azure.com) e aggiungere un segreto a [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) con nome **Password** e valore **myPassword**. 
2. Selezionare l'istanza dell'archivio di Configurazione app creata nella sezione precedente.

3. Selezionare **Esplora configurazioni**.

4. Selezionare **+ Crea** > **Riferimento all'insieme di credenziali delle chiavi** e quindi specificare i valori seguenti:
    - **Chiave**: selezionare **secrets.password**.
    - **Etichetta**: lasciare vuoto questo valore.
    - **Sottoscrizione**, **Gruppo di risorse** e **Insieme di credenziali delle chiavi**: immettere i valori corrispondenti ai valori dell'insieme di credenziali delle chiavi creati nel passaggio precedente.
    - **Segreto**: selezionare il segreto denominato **Password** creato nella sezione precedente.

## <a name="create-helm-chart"></a>Creare il grafico Helm ##
Per prima cosa, creare un grafico Helm di esempio con il comando seguente:
```console
helm create mychart
```

Helm crea una nuova directory denominata mychart con la struttura illustrata di seguito. 

> [!TIP]
> Per altre informazioni, vedere questa [guida ai grafici](https://helm.sh/docs/chart_template_guide/getting_started/).

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Aggiornare quindi la sezione **spec:template:spec:containers** del file *deployment.yaml*. Il frammento di codice seguente aggiunge due variabili di ambiente al contenitore. I valori verranno impostati dinamicamente in fase di distribuzione.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Il file *deployment.yaml* dopo l'aggiornamento dovrebbe essere simile al seguente.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Per archiviare dati sensibili come segreti Kubernetes, aggiungere un file *secrets.yaml* nella cartella templates.

> [!TIP]
> Per altre informazioni, vedere l'articolo su [come usare i segreti Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password }}
```

Aggiornare infine il file *values.yaml* con il contenuto seguente per fornire facoltativamente i valori predefiniti delle impostazioni di configurazione e dei segreti a cui viene fatto riferimento nei file *deployment.yaml* e *secrets.yaml*. I valori effettivi verranno sovrascritti dalla configurazione di cui è stato eseguito il pull da Configurazione app.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Passare la configurazione da Configurazione app all'installazione di Helm ##
Per prima cosa, scaricare la configurazione da Configurazione app a un file *myConfig.yaml*. Usare un filtro per scaricare solo le chiavi che iniziano con **settings.** . Se in questo caso il filtro non è sufficiente a escludere le chiavi dei riferimenti a Key Vault, è possibile usare l'argomento **--skip-keyvault** per escluderli. 

> [!TIP]
> Altre informazioni sul [comando export](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Scaricare quindi i segreti in un file denominato *secrets.yaml*. L'argomento della riga di comando **--resolve-vault** risolve i riferimenti a Key Vault recuperando i valori effettivi in Key Vault. È necessario eseguire questo comando con credenziali che hanno autorizzazioni di accesso per il Key Vault corrispondente.

> [!WARNING]
> Dal momento che questo file contiene informazioni riservate, conservarlo in modo sicuro e pulirlo quando non è più necessario.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Usare l'argomento **-f** di helm upgrade per passare i due file di configurazione creati. Tali file eseguiranno l'override dei valori di configurazione definiti in *values.yaml* con i valori esportati da Configurazione app.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

È anche possibile usare l'argomento **--set** di helm upgrade per passare valori di chiave letterali. L'argomento **--set** è utile per evitare il salvataggio permanente di dati sensibili sul disco. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Verificare che configurazioni e segreti siano stati impostati correttamente accedendo al [dashboard di Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard). Si noterà che i valori di **color** e **message** di Configurazione app sono stati popolati nelle variabili di ambiente del contenitore.

![Avvio rapido: avvio dell'app in locale](./media/kubernetes-dashboard-env-variables.png)

Un segreto, **password**, archiviato come riferimento a Key Vault in Configurazione app, è stato aggiunto anche nei segreti Kubernetes. 

![Avvio rapido: avvio dell'app in locale](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati esportati i dati di Configurazione app di Azure da usare in una distribuzione di Kubernetes con Helm. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
