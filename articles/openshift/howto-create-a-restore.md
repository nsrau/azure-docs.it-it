---
title: Creare un ripristino dell'applicazione cluster di Azure Red Hat OpenShift 4 con Velero
description: Informazioni su come creare un ripristino delle applicazioni cluster Red Hat OpenShift di Azure con Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: a4b22036612aac3d2b67e299881ba11b896979e5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492404"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Creare un ripristino dell'applicazione cluster Red Hat OpenShift 4 di Azure

In questo articolo si prepara l'ambiente per la creazione di un ripristino dell'applicazione cluster Red Hat OpenShift 4 di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti e installare gli strumenti necessari
> * Creare un ripristino dell'applicazione Azure Red Hat OpenShift 4

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Creare un backup dell'applicazione Azure Red Hat OpenShift 4

Per creare un backup dell'applicazione Azure Red Hat OpenShift 4, vedere [creare un backup di Azure Red Hat OpenShift 4](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Ripristinare un'applicazione Azure Red Hat OpenShift 4

Questa procedura consente di ripristinare un'applicazione di cui è stato eseguito il backup in precedenza con Velero.
È possibile controllare l'elenco dei backup attualmente riconosciuti dal cluster per vedere quali backup sono disponibili per il ripristino.  Per eseguire questo passaggio, è necessario eseguire il comando seguente:

_(Questo passaggio presuppone che sia stato installato Velero in un progetto denominato "Velero")_

```bash
oc get backups -n velero
```

Una volta ottenuto il backup che si desidera ripristinare, sarà necessario eseguire il ripristino con il comando seguente:

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

Questo passaggio creerà gli oggetti Kubernetes di cui è stato eseguito il backup dal passaggio precedente durante la creazione di un backup.

Per visualizzare lo stato del ripristino, eseguire il passaggio seguente:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando la fase indica `Completed` , l'applicazione Azure Red Hat 4 deve essere ripristinata.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>Ripristinare un'applicazione Azure Red Hat OpenShift 4 con snapshot inclusi


Per creare un ripristino di un'applicazione Azure Red Hat OpenShift 4 con volumi permanenti usando Velero, è necessario eseguire il ripristino con il comando seguente:

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
Questo passaggio creerà gli oggetti Kubernetes di cui è stato eseguito il backup dal passaggio precedente durante la creazione di un backup.

Per visualizzare lo stato del ripristino, eseguire il passaggio seguente:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando la fase indica `Completed` , l'applicazione Azure Red Hat 4 deve essere ripristinata.

Per altre informazioni su come creare backup e ripristini usando Velero, vedere [backup OpenShift Resources in modalità nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata ripristinata un'applicazione cluster Azure Red Hat OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Creare un ripristino dell'applicazione cluster OpenShift V4 con Velero
> * Creare un ripristino dell'applicazione cluster OpenShift V4 con snapshot usando Velero


Passare all'articolo successivo per informazioni sulle risorse supportate da Azure Red Hat OpenShift 4.

* [Risorse supportate da Azure Red Hat OpenShift V4](supported-resources.md)