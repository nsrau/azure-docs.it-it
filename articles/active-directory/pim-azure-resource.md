---
title: Gestire l'accesso alle risorse di Azure con Privileged Identity Management (PIM)
description: Informazioni sull'uso della gestione degli accessi basata sui ruoli in PIM per accedere alle risorse di Azure.
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4245d0dd9798046674f9ae3b197cec6b9b5d2545
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="manage-access-to-azure-resources-with-privileged-identity-management-preview"></a>Gestire l'accesso alle risorse di Azure con Privileged Identity Management (Anteprima)

Per proteggere gli account con privilegi da attacchi informatici, è possibile usare Azure Active Directory Privileged Identity Management (PIM) per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità nel loro uso tramite report e avvisi. Per fare ciò, PIM consente agli utenti di assumere i loro privilegi soltanto "just in time" (JIT), oppure tramite l'assegnazione dei privilegi per una durata abbreviata, dopo la quale i privilegi vengono revocati automaticamente. 

È attualmente possibile usare PIM con il controllo degli accessi in base al ruolo di Azure per gestire, controllare e monitorare l'accesso alle risorse di Azure. PIM può gestire l'appartenenza dei ruoli predefiniti e personalizzati per consentire di: 

- Abilitare l'accesso on demand, "just in time" alle risorse di Azure
- Terminare l'accesso alle risorse automaticamente per gli utenti e i gruppi assegnati
- Assegnare l'accesso temporaneo alle risorse di Azure per attività rapide o pianificazioni di chiamate
- Ottenere avvisi quando a nuovi utenti o gruppi viene assegnato l'accesso alle risorse e quando vengono attivate assegnazioni idonee

Per altre informazioni, vedere [Overview of Role-Based Access Control in Azure PIM](privileged-identity-management/azure-pim-resource-rbac.md) (Panoramica del controllo degli accessi in base al ruolo in PIM di Azure).
