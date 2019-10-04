---
title: Ruolo di amministratore del cluster OpenShift di Azure Red Hat | Microsoft Docs
description: Assegnazione e utilizzo del ruolo di amministratore del cluster OpenShift di Azure Red Hat
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936921"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Ruolo di amministratore del cliente Red Hat OpenShift di Azure

Si è l'amministratore del cluster di un cluster OpenShift di Azure Red Hat. L'account dispone di autorizzazioni maggiori e di accesso a tutti i progetti creati dall'utente.

Quando al proprio account è associato il ruolo di autorizzazione osa-Customer-Admin, può gestire automaticamente un progetto.

> [!Note] 
> Il ruolo del cluster osa-Customer-Admin non corrisponde al ruolo del cluster di amministrazione del cluster.


È ad esempio possibile eseguire azioni associate a un set di verbi (`create`) per operare su un set di nomi di risorse (`templates`). Per visualizzare i dettagli di questi ruoli e dei relativi set di verbi e risorse, eseguire il comando seguente:

`$ oc describe clusterrole/osa-customer-admin`

I nomi dei verbi non devono necessariamente eseguire il mapping diretto ai comandi `oc`. Essi equivalgono in modo più generale ai tipi di operazioni dell'interfaccia della riga di comando che è possibile eseguire. 

Se, ad esempio, il verbo `list` indica che è possibile visualizzare un elenco di tutti gli oggetti di un nome di risorsa (`oc get`). Il verbo `get` indica che è possibile visualizzare i dettagli di un oggetto specifico se si conosce il nome (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configurare il ruolo di amministratore del cliente

È possibile configurare il ruolo di amministratore del cliente solo durante la creazione del cluster fornendo il flag `--customer-admin-group-id`. Per informazioni su come configurare Azure Active Directory e il gruppo Administrators, vedere [integrazione Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

Configurare il ruolo osa-Customer-Admin:
> [!div class="nextstepaction"]
> [Integrazione di Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md)
