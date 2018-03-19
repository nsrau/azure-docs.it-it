---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
I [criteri di Azure](/azure/azure-policy/) consentono di verificare che tutte le risorse nella sottoscrizione soddisfino gli standard aziendali. Usare i criteri per ridurre i costi limitando le opzioni di distribuzione solo alle SKU e ai tipi di risorse approvati. È possibile definire regole e azioni per le risorse e tali regole verranno applicate automaticamente durante la distribuzione. È ad esempio possibile controllare i tipi di risorse che vengono distribuiti. In alternativa, è possibile limitare le posizioni approvate per le risorse. Alcuni criteri negano un'azione, mentre altri impostano il controllo di un'azione.

I criteri sono complementari al controllo degli accessi in base al ruolo. Il controllo degli accessi in base al ruolo è incentrato sull'accesso utente ed è un sistema con negazione predefinita e autorizzazioni esplicite. I criteri si concentrano sulle proprietà delle risorse durante e dopo la distribuzione. Rappresentano un sistema con autorizzazioni predefinite e negazione esplicita.

È importante comprendere due concetti sui criteri: le *definizioni di criteri* e le *assegnazioni di criteri*. Una definizione di criteri descrive le condizioni di gestione da applicare. Un'assegnazione di criteri applica una definizione di criteri in un particolare ambito.

![Assegnare criteri](./media/resource-manager-governance-policy/policy-concepts.png)

Azure offre diverse definizioni di criteri predefinite che è possibile usare senza alcuna modifica. È possibile passare i valori dei parametri per specificare i valori ammessi nell'ambito. Se una definizione di criteri predefinita non soddisfa i requisiti, è possibile [creare definizioni di criteri personalizzate](../articles/azure-policy/create-manage-policy.md).
