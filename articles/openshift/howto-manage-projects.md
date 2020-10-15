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
ms.openlocfilehash: 35474df96254b158a39b6d913171c553bf30aea4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816440"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Gestire progetti, modelli, flussi di immagini in un cluster Azure Red Hat OpenShift

In una piattaforma OpenShift container, i progetti vengono utilizzati per raggruppare e isolare gli oggetti correlati. In qualità di amministratore, è possibile concedere agli sviluppatori l'accesso a progetti specifici, consentire loro di creare progetti personalizzati e concedere loro diritti amministrativi a singoli progetti.

## <a name="self-provisioning-projects"></a>Progetti di provisioning automatico

È possibile consentire agli sviluppatori di creare i propri progetti. Un endpoint API è responsabile del provisioning di un progetto in base a un modello denominato Project-request. La console Web e il `oc new-project` comando usano questo endpoint quando uno sviluppatore crea un nuovo progetto.

Quando viene inviata una richiesta di progetto, l'API sostituisce i parametri seguenti nel modello:

| Parametro               | Descrizione                                    |
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
