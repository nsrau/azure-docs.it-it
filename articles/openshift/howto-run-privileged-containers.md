---
title: Eseguire contenitori con privilegi in un cluster Azure Red Hat OpenShift Documenti Microsoft
description: Eseguire contenitori con privilegi per monitorare la sicurezza e la conformità.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, acquasec, twistlock, cappello rosso
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271367"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Eseguire contenitori con privilegi in un cluster di Azure Red Hat OpenShift

Non è possibile eseguire contenitori con privilegi arbitrari nei cluster Azure Red Hat OpenShift.You can't run arbitrary privileged containers on Azure Red Hat OpenShift clusters.
Due soluzioni di monitoraggio e conformità della sicurezza possono essere eseguite nei cluster ARO.
In questo documento vengono descritte le differenze rispetto alla documentazione generica di distribuzione OpenShift dei fornitori di prodotti di protezione.


Leggere queste istruzioni prima di seguire le istruzioni del fornitore.
I titoli delle sezioni nei passaggi specifici del prodotto riportati di seguito si riferiscono direttamente ai titoli della sezione nella documentazione dei fornitori.

## <a name="before-you-begin"></a>Prima di iniziare

La documentazione della maggior parte dei prodotti di sicurezza presuppone che si disponga di privilegi di amministratore del cluster.
Gli amministratori dei clienti non dispongono di tutti i privilegi in Azure Red Hat OpenShift.Customer admins don't have all privileges in Azure Red Hat OpenShift. Le autorizzazioni necessarie per modificare le risorse a livello di cluster sono limitate.

Verificare innanzitutto che l'utente sia connesso al `oc get scc`cluster come amministratore del cliente eseguendo . Tutti gli utenti membri del gruppo di amministratori dei clienti dispongono delle autorizzazioni per visualizzare i vincoli del contesto di sicurezza (SCC) nel cluster.

Assicurarsi quindi `oc` che la `3.11.154`versione binaria sia .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Passaggi specifici del prodotto per Aqua Security
Le istruzioni di base che verranno modificate sono disponibili nella documentazione relativa alla distribuzione di [Aqua Security.](https://docs.aquasec.com/docs/openshift-red-hat) I passaggi qui verranno eseguiti insieme alla documentazione relativa alla distribuzione di Aqua.

Il primo passaggio consiste nell'annotare gli SCC necessari che verranno aggiornati. Queste annotazioni impediscono al pod di sincronizzazione del cluster di ripristinare le modifiche apportate a questi SSC.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Passaggio 1: Preparare i prerequisitiStep 1: Prepare prerequisites
Ricordarsi di accedere al cluster come amministratore cliente ARO anziché come ruolo di amministratore del cluster.

Creare il progetto e l'account del servizio.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Anziché assegnare il ruolo di lettore di cluster, assegnare il ruolo customer-admin-cluster all'account aqua con il comando seguente.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continuare a seguire le istruzioni rimanenti nel passaggio 1.  Queste istruzioni descrivono l'impostazione del segreto per il registro Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Passaggio 2: Distribuire Aqua Server, Database e GatewayStep 2: Deploy the Aqua Server, Database, and Gateway
Seguire i passaggi forniti nella documentazione di Aqua per l'installazione di aqua-console.yaml.

Modificare il `aqua-console.yaml`file fornito.  Rimuovere i primi due `kind: ClusterRole` oggetti `kind: ClusterRoleBinding`etichettati e .  Queste risorse non verranno create in quanto l'amministratore del `ClusterRole` cliente `ClusterRoleBinding` non dispone dell'autorizzazione per la modifica e gli oggetti.

La seconda modifica sarà `kind: Route` apportata `aqua-console.yaml`alla parte del file . Sostituire il seguente yaml per l'oggetto `kind: Route` nel `aqua-console.yaml` file.
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

### <a name="step-3-login-to-the-aqua-server"></a>Passaggio 3: Accedere all'Aqua Server
Questa sezione non viene modificata in alcun modo.  Seguire la documentazione di Aqua.

Utilizzare il comando seguente per ottenere l'indirizzo Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Passaggio 4: Distribuire gli aqua EnforcersStep 4: Deploy Aqua Enforcers
Impostare i campi seguenti durante la distribuzione delle applicazioni:

| Campo          | valore         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | acqua-account  |
| Project        | acqua-sicurezza |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Passaggi specifici del prodotto per Prisma Cloud / Twistlock

Le istruzioni di base che verranno modificate sono disponibili nella documentazione relativa alla distribuzione di [Prisma Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Iniziare installando `twistcli` lo strumento come descritto nelle sezioni "Installare Prisma Cloud" e "Scaricare il software Prisma Cloud".

Creare un nuovo progetto OpenShift
```
oc new-project twistlock
```

Saltare la sezione opzionale "Push the Prisma Cloud images to a private registry". Non funzionerà su Azure Red Hat Openshift. Utilizzare invece il Registro di sistema online.

È possibile seguire la documentazione ufficiale durante l'applicazione delle correzioni descritte di seguito.
Inizia con la sezione "Installa Console".

### <a name="install-console"></a>Installa console

Durante `oc create -f twistlock_console.yaml` il passaggio 2, si otterrà un errore durante la creazione dello spazio dei nomi.
È possibile ignorarlo, lo spazio dei `oc new-project` nomi è stato creato in precedenza con il comando.

Utilizzare `azure-disk` per il tipo di archiviazione.

### <a name="create-an-external-route-to-console"></a>Creare un percorso esterno alla consoleCreate an external route to Console

È possibile seguire la documentazione o le istruzioni riportate di seguito se si preferisce il comando oc.
Copiare la seguente definizione di route in un file denominato twistlock_route.yaml sul computer
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
quindi eseguire:
```
oc create -f twistlock_route.yaml
```

È possibile ottenere l'URL assegnato alla console Twistlock con questo comando:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configura console

Seguire la documentazione di Twistlock.

### <a name="install-defender"></a>Installare Defender

Durante `oc create -f defender.yaml` il passaggio 2, si otterranno errori durante la creazione del ruolo del cluster e l'associazione del ruolo del cluster.
possono essere ignorate.

I difensori verranno distribuiti solo nei nodi di calcolo. Non è necessario limitarli con un selettore di nodo.
