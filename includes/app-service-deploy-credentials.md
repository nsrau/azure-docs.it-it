---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649138"
---
* **Credenziali a livello di utente**: un insieme di credenziali per tutto l'account Azure. Può essere usato per distribuire il Servizio app per qualsiasi app, in tutte le sottoscrizioni a cui l'account di Azure è autorizzato ad accedere. Corrisponde al set predefinito indicato nell'interfaccia utente grafica del portale, ad esempio nelle aree **Panoramica** e **Proprietà** della [pagina delle risorse](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources) dell'app. Quando a un utente viene concesso l'accesso all'app tramite il controllo degli accessi in base al ruolo (RBAC) o le autorizzazioni di co-amministratore, tale utente può usare le proprie credenziali a livello di utente fino a quando non viene revocato l'accesso. Non condividere queste credenziali con altri utenti di Azure.

* **Credenziali a livello di applicazione**: un insieme di credenziali per ogni applicazione. Può essere usato per distribuire solo in quella app. Le credenziali per ogni app vengono generate automaticamente al momento della creazione dell'app. Non possono essere configurate manualmente, ma possono essere reimpostate in qualsiasi momento. Per ottenere l'accesso alle credenziali a livello di app tramite il controllo degli accessi in base al ruolo (RBAC), l'utente deve avere il ruolo di Collaboratore o un ruolo superiore per l'app, incluso il ruolo predefinito Collaboratore sito Web. I lettori non sono autorizzati alla pubblicazione e quindi non possono accedere a queste credenziali.