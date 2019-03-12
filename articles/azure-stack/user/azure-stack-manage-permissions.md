---
title: Gestire le autorizzazioni alle risorse per ogni utente in Azure Stack | Microsoft Docs
description: Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni RBAC.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3565bf4c4a19bcf1b136b4cbb781006658865a1c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767210"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gestire l'accesso alle risorse con controllo degli accessi Azure Stack Role-Based

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Azure Stack supporta il controllo di accesso basato sui ruoli (RBAC), lo stesso [modello di sicurezza per la gestione degli accessi](https://docs.microsoft.com/azure/role-based-access-control/overview) che usa Microsoft Azure. È possibile usare RBAC per gestire utenti, gruppo o applicazione l'accesso a servizi, risorse e sottoscrizioni.

## <a name="basics-of-access-management"></a>Nozioni di base della gestione degli accessi

Controllo degli accessi in base al ruolo offre controllo di accesso con granularità fine che è possibile usare per proteggere l'ambiente. Concedere agli utenti le autorizzazioni esatte che necessarie tramite l'assegnazione di un ruolo RBAC in un determinato ambito. L'ambito dell'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Leggere il [basata sui ruoli controllo degli accessi nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) articolo per ottenere informazioni più dettagliate sulla gestione degli accessi.

### <a name="built-in-roles"></a>Ruoli predefiniti

Azure Stack presenta tre ruoli di base che è possibile applicare a tutti i tipi di risorse:

* **Proprietario** può gestire tutto, incluso l'accesso alle risorse.
* **Collaboratore** può gestire tutto ad eccezione degli accessi alle risorse.
* **Lettore** può visualizzare tutto, ma non è possibile apportare modifiche.

### <a name="resource-hierarchy-and-inheritance"></a>Ereditarietà e gerarchia di risorse

Azure Stack è la gerarchia delle risorse seguenti:

* Ogni sottoscrizione appartiene a una directory.
* Ogni gruppo di risorse appartiene a una sottoscrizione.
* Ogni risorsa appartiene a un gruppo di risorse.

Accesso che concede a un ambito padre viene ereditato dall'ambito figlio. Ad esempio: 

* Si assegna il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
* Si assegna il ruolo Collaboratore a un'applicazione nell'ambito del gruppo di risorse. L'applicazione può gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### <a name="assigning-roles"></a>Assegnazione di ruoli

È possibile assegnare più di un ruolo a un utente e ogni ruolo può essere associato a un ambito diverso. Ad esempio: 

* Si assegna il ruolo di lettore the TestUser-A alla sottoscrizione 1.
* Si assegna ruolo TestUser-A il proprietario TestVM-1.

Azure [assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) articolo fornisce informazioni dettagliate su come visualizzare, assegnare e l'eliminazione di ruoli.

### <a name="resource-hierarchy-and-inheritance"></a>Ereditarietà e gerarchia di risorse

Azure Stack è la gerarchia delle risorse seguenti:

* Ogni sottoscrizione appartiene a una directory.
* Ogni gruppo di risorse appartiene a una sottoscrizione.
* Ogni risorsa appartiene a un gruppo di risorse.

Accesso che concede a un ambito padre viene ereditato dall'ambito figlio. Ad esempio: 

* Si assegna il **lettore** ruolo a un gruppo di Azure AD nell'ambito della sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
* Si assegna il **collaboratore** ruolo a un'applicazione nell'ambito del gruppo di risorse. L'applicazione può gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### <a name="assigning-roles"></a>Assegnazione di ruoli

È possibile assegnare più di un ruolo a un utente e ogni ruolo può essere associato a un ambito diverso. Ad esempio: 

* Si assegna il ruolo di lettore the TestUser-A alla sottoscrizione 1.
* Si assegna ruolo TestUser-A il proprietario TestVM-1.

Azure [assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) articolo fornisce informazioni dettagliate su come visualizzare, assegnare e l'eliminazione di ruoli.

## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente

I passaggi seguenti descrivono come configurare le autorizzazioni per un utente.

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel riquadro di spostamento a sinistra scegliere **Gruppi di risorse**.
3. Scegliere il nome del gruppo di risorse che si desidera impostare le autorizzazioni su.
4. Nel riquadro di spostamento del gruppo di risorse, scegliere **controllo di accesso (IAM)**. Il **assegnazioni di ruolo** visualizzazione Elenca gli elementi che dispongono dell'accesso al gruppo di risorse. È possibile filtrare e raggruppare i risultati.
5. Nel **controllo di accesso** menu barra, scegliere **Add**.
6. Sul **aggiungere autorizzazioni** riquadro:

   * Scegliere il ruolo da assegnare il **ruolo** elenco a discesa.
   * Scegliere la risorsa che si desidera assegnare dal **assegna accesso a** elenco a discesa.
   * Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.

7. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Creare entità servizio](azure-stack-create-service-principals.md)