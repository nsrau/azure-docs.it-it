---
title: Aggiungere o aggiornare il segreto di pull Red Hat in un cluster Azure Red Hat OpenShift 4
description: Aggiungere o aggiornare il segreto di pull Red Hat nei cluster di 4. x ARO esistenti
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: Pull Secret, Aro, OpenShift, Red Hat
ms.openlocfilehash: 3351052db63f095bfca5f0b91f26e1013319c582
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098897"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Aggiungere o aggiornare il segreto di pull Red Hat in un cluster Azure Red Hat OpenShift 4

Questa guida illustra l'aggiunta o l'aggiornamento del segreto di pull Red Hat per un cluster Azure Red Hat OpenShift 4. x esistente.

Se si sta creando un cluster per la prima volta, è possibile aggiungere il segreto di pull quando si crea il cluster. Per altre informazioni sulla creazione di un cluster ARO con un segreto di pull Red Hat, vedere [creare un cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Prima di iniziare

Questa guida presuppone che sia presente un cluster Azure Red Hat OpenShift 4 esistente. Assicurarsi di disporre dell'accesso di amministratore al cluster.

## <a name="prepare-your-pull-secret"></a>Preparare il segreto di pull
Quando si crea un cluster ARO senza aggiungere un segreto di pull Red Hat, viene ancora creato un segreto di pull nel cluster. Tuttavia, questo segreto di pull non è completamente popolato.

Questa sezione illustra l'aggiornamento del segreto di pull con valori aggiuntivi dal segreto di pull di Red Hat.

1. Recuperare il segreto denominato `pull-secret` nello spazio dei nomi OpenShift-config e salvarlo in un file separato eseguendo il comando seguente: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    L'output dovrebbe essere simile al seguente (si noti che il valore del segreto effettivo è stato rimosso):

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Passare al [portale di gestione cluster Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e fare clic **su Scarica segreto pull.** Il segreto di pull Red Hat avrà un aspetto simile al seguente (si noti che i valori dei segreti effettivi sono stati rimossi):

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Modificare il file del segreto di pull ottenuto dal cluster aggiungendo le voci trovate nel segreto di pull di Red Hat. 

    > [!IMPORTANT]
    > Se si include la `cloud.openshift.com` voce di Red Hat pull Secret, il cluster inizierà a inviare i dati di telemetria a Red Hat. Includere questa sezione solo se si desidera inviare dati di telemetria. In caso contrario, lasciare la sezione seguente.
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Non rimuovere o modificare la `arosvc.azurecr.io` voce del segreto di pull. Questa sezione è necessaria per il corretto funzionamento del cluster.
    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Il file finale dovrebbe essere simile al seguente (si noti che i valori dei segreti effettivi sono stati rimossi):

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Verificare che il file sia JSON valido. Esistono diversi modi per convalidare il codice JSON. L'esempio seguente usa JQ:
    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Se si verifica un errore nel file, può essere visualizzato `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Aggiungere il segreto di pull al cluster

Eseguire il comando seguente per aggiornare il segreto di pull:

> [!NOTE]
> Eseguendo questo comando, i nodi del cluster verranno riavviati uno alla volta durante l'aggiornamento. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Una volta impostato il segreto, si è pronti per abilitare gli operatori Red Hat Certified.

### <a name="modify-the-configuration-files"></a>Modificare i file di configurazione

Modificare gli oggetti seguenti per abilitare gli operatori Red Hat.

Modificare prima di tutto il file di configurazione dell'operatore Samples. Quindi, è possibile eseguire il comando seguente per modificare il file di configurazione:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Modificare i `spec.architectures.managementState` valori di e `status.architecture.managementState` da `Removed` a `Managed` . 

Il frammento di codice YAML seguente mostra solo le sezioni rilevanti del file YAML modificato.

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

In secondo luogo, eseguire il comando seguente per modificare il file di configurazione dell'hub operatore:  

```console
oc edit operatorhub cluster -o yaml
```

Modificare i `Spec.Sources.Disabled` valori di e `Status.Sources.Disabled` da `true` a `false` per le origini che si desidera attivare.

Il frammento di codice YAML seguente mostra solo le sezioni rilevanti del file YAML modificato.

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Salvare il file per applicare le modifiche.

## <a name="validate-that-your-secret-is-working"></a>Verificare che il segreto funzioni

Dopo l'aggiunta del segreto di pull e la modifica dei file di configurazione corretti, l'aggiornamento del cluster può richiedere alcuni minuti. Per verificare che il cluster sia stato aggiornato, eseguire il comando seguente per visualizzare le origini certificate Operators e Red Hat Operators disponibili:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Se gli operatori certificati e gli operatori Red Hat non vengono visualizzati, attendere alcuni minuti e riprovare.

Per assicurarsi che il segreto di pull sia stato aggiornato e funzioni correttamente, aprire OperatorHub e verificare la presenza di qualsiasi operatore Red Hat Verified. Ad esempio, verificare se l'operatore di archiviazione del contenitore OpenShift è disponibile e verificare se si dispone delle autorizzazioni per l'installazione.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui segreti pull di Red Hat, vedere [uso di segreti di pull di immagini](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html).

Per altre informazioni su Red Hat OpenShift 4, vedere [Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/welcome/index.html).