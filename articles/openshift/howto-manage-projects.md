---
title: Gestire le risorse in Azure Red Hat OpenShift Documenti Microsoft
description: Gestire progetti, modelli, flussi di immagini in un cluster Azure Red Hat OpenShiftManage projects, templates, image-streams in an Azure Red Hat OpenShift cluster
services: openshift
keywords: cappello rosso openshift progetti richiede auto-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139114"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gestire progetti, modelli, flussi di immagini in un cluster Azure Red Hat OpenShiftManage projects, templates, image-streams in an Azure Red Hat OpenShift cluster 

In una piattaforma contenitore OpenShift, i progetti vengono utilizzati per raggruppare e isolare gli oggetti correlati. In qualità di amministratore, è possibile concedere agli sviluppatori l'accesso a progetti specifici, consentire loro di creare i propri progetti e concedere loro diritti amministrativi per singoli progetti.

## <a name="self-provisioning-projects"></a>Progetti di auto-provisioning

È possibile consentire agli sviluppatori di creare i propri progetti. Un endpoint API è responsabile del provisioning di un progetto in base a un modello denominato project-request. La console Web `oc new-project` e il comando utilizzano questo endpoint quando uno sviluppatore crea un nuovo progetto.

Quando viene inviata una richiesta di progetto, l'API sostituisce i seguenti parametri nel modello:

| Parametro               | Descrizione                                    |
| ----------------------- | ---------------------------------------------- |
| Project_name            | Nome del progetto. Obbligatorio.             |
| PROJECT_DISPLAYNAME     | Nome visualizzato del progetto. Può essere vuoto. |
| PROJECT_DESCRIPTION     | Descrizione del progetto. Può essere vuoto.  |
| PROJECT_ADMIN_USER      | Nome utente dell'utente amministratore.       |
| PROJECT_REQUESTING_USER | Nome utente dell'utente richiedente.           |

L'accesso all'API viene concesso agli sviluppatori con l'associazione del ruolo del cluster auto-provisioning. Questa funzionalità è disponibile per tutti gli sviluppatori autenticati per impostazione predefinita.

## <a name="modify-the-template-for-a-new-project"></a>Modificare il modello per un nuovo progetto 

1. Accedere come utente `customer-admin` con privilegi.

2. Modificare il modello di richiesta di progetto predefinito.

   ```
   oc edit template project-request -n openshift
   ```

3. Rimuovere il modello di progetto predefinito dal processo di aggiornamento di Azure Red Hat OpenShift (ARO) aggiungendo l'annotazione seguente:Remove the default project template from the Azure Red Hat OpenShift (ARO) update process by adding the following annotation:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Il modello di richiesta di progetto non verrà aggiornato dal processo di aggiornamento ARO. Ciò consente ai clienti di personalizzare il modello e di conservarle quando il cluster viene aggiornato.

## <a name="disable-the-self-provisioning-role"></a>Disabilitare il ruolo di provisioning automaticoDisable the self-provisioning role

È possibile impedire a un gruppo di utenti autenticato di eseguire il provisioning automatico di nuovi progetti.

1. Accedere come utente `customer-admin` con privilegi.

2. Modificare l'associazione del ruolo del cluster auto-provisioning.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Rimuovere il ruolo dal processo di aggiornamento ARO aggiungendo l'annotazione seguente: `openshift.io/reconcile-protect: "true"`.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Modificare l'associazione `system:authenticated:oauth` del ruolo del cluster per impedire la creazione di progetti:Change the cluster role binding to prevent from creating projects:

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

## <a name="manage-default-templates-and-imagestreams"></a>Gestire i modelli predefiniti e imageStreams

In Azure Red Hat OpenShift è possibile disabilitare gli `openshift` aggiornamenti per tutti i modelli predefiniti e i flussi di immagini all'interno dello spazio dei nomi.
Per disabilitare `Templates` gli `ImageStreams` `openshift` aggiornamenti per TUTTI e nello spazio dei nomi:

1. Accedere come utente `customer-admin` con privilegi.

2. Modifica `openshift` spazio dei nomi:

   ```
   oc edit namespace openshift
   ```

3. Rimuovere `openshift` lo spazio dei nomi dal processo di aggiornamento ARO aggiungendo l'annotazione seguente:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Qualsiasi singolo oggetto `openshift` nello spazio dei nomi può `openshift.io/reconcile-protect: "true"` essere rimosso dal processo di aggiornamento aggiungendovi l'annotazione.

## <a name="next-steps"></a>Passaggi successivi

Prova il tutorial:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)
