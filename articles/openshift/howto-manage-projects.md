---
title: Gestire le risorse in Azure Red Hat OpenShift | Microsoft Docs
description: Gestire progetti, modelli, flussi di immagini in un cluster Azure Red Hat OpenShift
services: openshift
keywords: Red Hat OpenShift Projects richiede il provisioning automatico
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 0abc086553f5e903a71bcfd0b6322bcee56d2d8b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216930"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gestire progetti, modelli, flussi di immagini in un cluster Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 verrà ritirato il 30 giugno 2022. Il supporto per la creazione di nuovi cluster Azure Red Hat OpenShift 3,11 continua fino al 30 novembre 2020. Dopo il ritiro, i cluster di Azure Red Hat OpenShift 3,11 rimanenti verranno arrestati per evitare vulnerabilità di sicurezza.
> 
> Seguire questa guida per [creare un cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Per domande specifiche, [Contattaci](mailto:arofeedback@microsoft.com).

In una piattaforma OpenShift container, i progetti vengono utilizzati per raggruppare e isolare gli oggetti correlati. In qualità di amministratore, è possibile concedere agli sviluppatori l'accesso a progetti specifici, consentire loro di creare progetti personalizzati e concedere loro diritti amministrativi a singoli progetti.

## <a name="self-provisioning-projects"></a>Progetti di provisioning automatico

È possibile consentire agli sviluppatori di creare i propri progetti. Un endpoint API è responsabile del provisioning di un progetto in base a un modello denominato Project-request. La console Web e il `oc new-project` comando usano questo endpoint quando uno sviluppatore crea un nuovo progetto.

Quando viene inviata una richiesta di progetto, l'API sostituisce i parametri seguenti nel modello:

| Parametro               | Description                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Nome del progetto. Obbligatorio.             |
| PROJECT_DISPLAYNAME     | Nome visualizzato del progetto. Può essere vuoto. |
| PROJECT_DESCRIPTION     | Descrizione del progetto. Può essere vuoto.  |
| PROJECT_ADMIN_USER      | Nome utente dell'utente che gestisce l'amministrazione.       |
| PROJECT_REQUESTING_USER | Nome utente dell'utente richiedente.           |

L'accesso all'API viene concesso agli sviluppatori con l'associazione del ruolo del cluster self-provisioning. Questa funzionalità è disponibile per tutti gli sviluppatori autenticati per impostazione predefinita.

## <a name="modify-the-template-for-a-new-project"></a>Modificare il modello per un nuovo progetto 

1. Accedere come utente con `customer-admin` privilegi.

2. Modificare il modello di richiesta di progetto predefinito.

   ```
   oc edit template project-request -n openshift
   ```

3. Rimuovere il modello di progetto predefinito dal processo di aggiornamento di Azure Red Hat OpenShift (ARO) aggiungendo l'annotazione seguente: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Il modello di richiesta di progetto non verrà aggiornato dal processo di aggiornamento di ARO. In questo modo i clienti possono personalizzare il modello e mantenere queste personalizzazioni quando il cluster viene aggiornato.

## <a name="disable-the-self-provisioning-role"></a>Disabilitare il ruolo di provisioning automatico

È possibile impedire a un gruppo di utenti autenticato di eseguire il provisioning automatico di nuovi progetti.

1. Accedere come utente con `customer-admin` privilegi.

2. Modificare l'associazione del ruolo del cluster self-provisioners.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Rimuovere il ruolo dal processo di aggiornamento di ARO aggiungendo l'annotazione seguente: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Modificare l'associazione di ruoli del cluster in modo da impedire la `system:authenticated:oauth` creazione di progetti:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Gestisci modelli e imageStreams predefiniti

In Azure Red Hat OpenShift è possibile disabilitare gli aggiornamenti per tutti i modelli e i flussi di immagini predefiniti all'interno `openshift` dello spazio dei nomi.
Per disabilitare gli aggiornamenti per tutti i `Templates` e `ImageStreams` nello `openshift` spazio dei nomi:

1. Accedere come utente con `customer-admin` privilegi.

2. Modifica `openshift` spazio dei nomi:

   ```
   oc edit namespace openshift
   ```

3. Rimuovere lo `openshift` spazio dei nomi dal processo di aggiornamento di Aro aggiungendo l'annotazione seguente: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   È possibile rimuovere qualsiasi singolo oggetto nello `openshift` spazio dei nomi dal processo di aggiornamento aggiungendo annotazioni `openshift.io/reconcile-protect: "true"` .

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
