---
title: Ruolo di amministratore del cluster OpenShift di Azure Red Hat | Microsoft Docs
description: Assegnazione e utilizzo del ruolo di amministratore del cluster OpenShift di Azure Red Hat
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79139097"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Ruolo di amministratore del cliente Red Hat OpenShift di Azure

Si è l'amministratore del cluster di un cluster OpenShift di Azure Red Hat. L'account dispone di autorizzazioni maggiori e di accesso a tutti i progetti creati dall'utente.

Quando al proprio account è associato il ruolo di autorizzazione Customer-Admin-cluster, può gestire automaticamente un progetto.

> [!Note] 
> Il ruolo cluster Customer-Admin-cluster non corrisponde a quello del cluster di amministrazione del cluster.

È ad esempio possibile eseguire azioni associate a un set di verbi (`create`) per operare su un set di nomi di risorse (`templates`). Per visualizzare i dettagli di questi ruoli e dei relativi set di verbi e risorse, eseguire il comando seguente:

`$ oc get clusterroles customer-admin-cluster -o yaml`

I nomi dei verbi non devono necessariamente eseguire il `oc` mapping direttamente ai comandi. Essi equivalgono in modo più generale ai tipi di operazioni dell'interfaccia della riga di comando che è possibile eseguire. 

Se, ad esempio, `list` il verbo indica che è possibile visualizzare un elenco di tutti gli oggetti di un nome`oc get`di risorsa (). Il `get` verbo indica che è possibile visualizzare i dettagli di un oggetto specifico se si conosce il nome (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configurare il ruolo di amministratore del cliente

È possibile configurare il ruolo cluster Customer-Admin-cluster solo durante la creazione del cluster fornendo il `--customer-admin-group-id`flag. Questo campo non è attualmente configurabile nel portale di Azure. Per informazioni su come configurare Azure Active Directory e il gruppo Administrators, vedere [integrazione Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confermare l'appartenenza al ruolo di amministratore del cliente

Per confermare l'appartenenza al gruppo Customer Admin, provare i comandi dell'interfaccia della `oc get nodes` riga `oc projects`di comando di OpenShift o. `oc get nodes`visualizzerà un elenco di nodi se si ha il ruolo Customer-Admin-cluster e un errore di autorizzazione se si ha solo il ruolo Customer-Admin-Project. `oc projects`mostrerà tutti i progetti nel cluster invece dei soli progetti in uso.

Per esplorare ulteriormente i ruoli e le autorizzazioni nel cluster, è possibile usare [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) il comando.

## <a name="next-steps"></a>Passaggi successivi

Configurare il ruolo cluster Customer-Admin-cluster:
> [!div class="nextstepaction"]
> [Integrazione di Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md)
