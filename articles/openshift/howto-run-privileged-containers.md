---
title: Eseguire i contenitori con privilegi in un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Eseguire i contenitori con privilegi per monitorare la sicurezza e la conformità.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: Aro, OpenShift, aquasec, Twistlock, Red Hat
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271367"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Eseguire contenitori con privilegi in un cluster di Azure Red Hat OpenShift

Non è possibile eseguire contenitori con privilegi arbitrari nei cluster OpenShift di Azure Red Hat.
È consentita l'esecuzione di due soluzioni di monitoraggio e conformità di sicurezza nei cluster ARO.
In questo documento vengono descritte le differenze rispetto alla documentazione di distribuzione OpenShift generica dei fornitori di prodotti per la sicurezza.


Leggere queste istruzioni prima di seguire le istruzioni del fornitore.
I titoli di sezione nei passaggi specifici del prodotto indicati di seguito si riferiscono direttamente ai titoli delle sezioni nella documentazione dei fornitori.

## <a name="before-you-begin"></a>Prima di iniziare

La documentazione della maggior parte dei prodotti di sicurezza presuppone che si disponga di privilegi di amministratore del cluster.
Gli amministratori del cliente non hanno tutti i privilegi in Azure Red Hat OpenShift. Le autorizzazioni necessarie per modificare le risorse a livello di cluster sono limitate.

Prima di tutto, assicurarsi che l'utente sia connesso al cluster come amministratore del cliente, eseguendo `oc get scc`. Tutti gli utenti membri del gruppo di amministratori del cliente dispongono delle autorizzazioni per visualizzare i vincoli del contesto di sicurezza (SCCs) nel cluster.

Assicurarsi quindi che la versione binaria `oc` sia `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Passaggi specifici del prodotto per la sicurezza di Aqua
Le istruzioni di base che verranno modificate sono disponibili nella [documentazione relativa alla distribuzione della sicurezza di Aqua](https://docs.aquasec.com/docs/openshift-red-hat). Questa procedura viene eseguita insieme alla documentazione relativa alla distribuzione di Aqua.

Il primo passaggio consiste nell'annotare il SCCs obbligatorio che verrà aggiornato. Queste annotazioni impediscono al pod di sincronizzazione del cluster di ripristinare le modifiche apportate a questi SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Passaggio 1: preparare i prerequisiti
Ricordarsi di accedere al cluster come amministratore del cliente ARO anziché al ruolo di amministratore del cluster.

Creare il progetto e l'account del servizio.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Anziché assegnare il ruolo di lettore del cluster, assegnare il ruolo Customer-Admin-cluster a Aqua-account con il comando seguente.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continuare seguendo le istruzioni rimanenti nel passaggio 1.  Queste istruzioni descrivono la configurazione del segreto per il registro di sistema Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Passaggio 2: distribuire il server Aqua, il database e il gateway
Seguire i passaggi indicati nella documentazione di Aqua per l'installazione di Aqua-console. yaml.

Modificare il `aqua-console.yaml`fornito.  Rimuovere i primi due oggetti con etichetta, `kind: ClusterRole` e `kind: ClusterRoleBinding`.  Queste risorse non verranno create perché l'amministratore del cliente non dispone dell'autorizzazione al momento per modificare `ClusterRole` e `ClusterRoleBinding` oggetti.

La seconda modifica verrà apportata al `kind: Route` parte del `aqua-console.yaml`. Sostituire il seguente YAML per l'oggetto `kind: Route` nel file di `aqua-console.yaml`.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Seguire le istruzioni rimanenti.

### <a name="step-3-login-to-the-aqua-server"></a>Passaggio 3: accedere al server Aqua
Questa sezione non è stata modificata in alcun modo.  Seguire la documentazione di Aqua.

Usare il comando seguente per ottenere l'indirizzo della console Aqua.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Passaggio 4: distribuire l'applicazione Aqua
Quando si distribuiscono i campi, impostare i campi seguenti:

| Campo          | valore         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | Aqua-account  |
| Project        | Aqua-sicurezza |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Passaggi specifici del prodotto per Prism cloud/Twistlock

Le istruzioni di base che verranno modificate sono disponibili nella [documentazione relativa alla distribuzione di Prism cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Per iniziare, installare lo strumento `twistcli` come descritto nelle sezioni "Install Prismal cloud" e "download the Prism Cloud software".

Creare un nuovo progetto OpenShift
```
oc new-project twistlock
```

Ignorare la sezione facoltativa "inserire le immagini del cloud prisma in un registro privato". Non funzionerà in Azure Red Hat OpenShift. Usare invece il registro online.

È possibile seguire la documentazione ufficiale durante l'applicazione delle correzioni descritte di seguito.
Iniziare con la sezione "installare la console".

### <a name="install-console"></a>Installare la console

Durante la `oc create -f twistlock_console.yaml` nel passaggio 2, si verifica un errore durante la creazione dello spazio dei nomi.
È possibile ignorarlo in modo sicuro, lo spazio dei nomi è stato creato in precedenza con il comando `oc new-project`.

Usare `azure-disk` per il tipo di archiviazione.

### <a name="create-an-external-route-to-console"></a>Creare una route esterna alla console

Se si preferisce il comando OC, è possibile seguire la documentazione o le istruzioni riportate di seguito.
Copiare la definizione di route seguente in un file denominato twistlock_route. YAML nel computer
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
eseguire quindi:
```
oc create -f twistlock_route.yaml
```

È possibile ottenere l'URL assegnato alla console di Twistlock con questo comando: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configura console

Seguire la documentazione di Twistlock.

### <a name="install-defender"></a>Installare Defender

Durante la `oc create -f defender.yaml` nel passaggio 2, si otterranno errori durante la creazione del ruolo del cluster e dell'associazione del ruolo cluster.
È possibile ignorarli.

I difensori verranno distribuiti solo nei nodi di calcolo. Non è necessario limitarli con un selettore di nodo.
