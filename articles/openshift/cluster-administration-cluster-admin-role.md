---
title: Ruolo di amministratore del cluster OpenShift di Azure Red Hat | Microsoft Docs
description: Assegnazione e utilizzo del ruolo amministratore del cluster OpenShift di Azure Red Hat
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709946"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Ruolo di amministratore del cliente Red Hat OpenShift di Azure

In qualità di amministratore del cluster ARO (Azure Red Hat OpenShift) di un cluster OpenShift, l'account dispone di autorizzazioni maggiori e di accesso a tutti i progetti creati dall'utente.

Quando al proprio account è associato il ruolo di autorizzazione osa-Customer-Admins, può gestire automaticamente un progetto.

> [!Note] 
> osa-Customer-Admin clusterrole non corrisponde a clusterrole di amministrazione cluster


È ad esempio possibile eseguire azioni associate a un set di verbi (`create`) per operare su un set di nomi di risorse (`templates`). Per visualizzare i dettagli di questi ruoli e dei relativi set di verbi e risorse, eseguire il comando seguente:

`$ oc describe clusterrole/osa-customer-admin`

I nomi dei verbi non necessariamente eseguono il mapping direttamente ai comandi di OC, bensì equivalgono più in generale ai tipi di operazioni dell'interfaccia della riga di comando che è possibile eseguire. Se, ad esempio, il verbo `list` indica che è possibile visualizzare un elenco di tutti gli oggetti di un determinato nome di risorsa (`oc get`), mentre il verbo `get` significa che è possibile visualizzare i dettagli di un oggetto specifico se si conosce il nome (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Come configurare il ruolo di amministratore del cliente

Il ruolo di amministratore del cliente può essere configurato solo durante la creazione del cluster fornendo flag `--customer-admin-group-id`. Come configurare Azure Active Directory e il gruppo Administrators attenersi alla procedura seguente: [Integrazione di Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Passaggi successivi

Come configurare il ruolo osa-Customer-Admin:
> [!div class="nextstepaction"]
> [Integrazione di Azure Active Directory per Azure Red Hat OpenShift](howto-aad-app-configuration.md)
