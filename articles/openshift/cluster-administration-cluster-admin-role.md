---
title: Ruolo di amministratore del cluster Azure Red Hat OpenShift Documenti Microsoft
description: Assegnazione e utilizzo del ruolo di amministratore cluster Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139097"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Ruolo di amministratore del cliente Azure Red Hat OpenShiftAzure Red Hat OpenShift customer administrator role

Sei l'amministratore del cluster di un cluster Azure Red Hat OpenShift. Il tuo account ha aumentato le autorizzazioni e l'accesso a tutti i progetti creati dall'utente.

Quando all'account è associato il ruolo di autorizzazione cliente-amministratore-cluster, può gestire automaticamente un progetto.

> [!Note] 
> Il ruolo del cluster customer-admin-cluster non corrisponde al ruolo del cluster cluster.The customer-admin-cluster cluster role is not the same as the cluster-admin cluster role.

Ad esempio, è possibile eseguire azioni associate`create`a un set di verbi`templates`( ) per operare su un set di nomi di risorse ( ). Per visualizzare i dettagli di questi ruoli e dei relativi set di verbi e risorse, eseguire il comando seguente:

`$ oc get clusterroles customer-admin-cluster -o yaml`

I nomi dei verbi non `oc` vengono necessariamente tutti mappati direttamente ai comandi. Essi equiparano più in generale ai tipi di operazioni CLI che è possibile eseguire. 

Ad esempio, `list` con il verbo significa che è possibile visualizzare`oc get`un elenco di tutti gli oggetti di un nome di risorsa ( ). Il `get` verbo significa che è possibile visualizzare i dettagli`oc describe`di un oggetto specifico se si conosce il nome ( ).

## <a name="configure-the-customer-administrator-role"></a>Configurare il ruolo di amministratore del cliente

È possibile configurare il ruolo del cluster customer-admin-cluster solo durante la creazione del cluster specificando il flag `--customer-admin-group-id`. Questo campo non è attualmente configurabile nel portale di Azure.This field is not currently configurable in the Azure portal. Per informazioni su come configurare Azure Active Directory e il gruppo Administrators, vedere Integrazione di [Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confermare l'appartenenza al ruolo di amministratore del cliente

Per confermare l'appartenenza al gruppo di amministratori `oc get nodes` `oc projects`del cliente, provare i comandi OpenShift CLI o . `oc get nodes`verrà visualizzato un elenco di nodi se si dispone del ruolo customer-admin-cluster e di un errore di autorizzazione se si dispone solo del ruolo customer-admin-project. `oc projects`mostrerà tutti i progetti nel cluster invece che solo i progetti in cui si sta lavorando.

Per esplorare ulteriormente i ruoli e [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) le autorizzazioni nel cluster, è possibile usare il comando .

## <a name="next-steps"></a>Passaggi successivi

Configurare il ruolo del cluster customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integrazione di Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md)
