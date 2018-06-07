---
title: Gestire le autorizzazioni alle risorse per ogni utente nello Stack di Azure | Documenti Microsoft
description: Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni RBAC.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 026c686b1d5654aa50dd63b9addd619dd5322da0
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808146"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gestire l'accesso alle risorse con il controllo di accesso Azure Stack Role-Based

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure supporta il controllo di accesso basate sui ruoli (RBAC), lo stesso [modello di sicurezza per la gestione accessi](https://docs.microsoft.com/azure/role-based-access-control/overview) che usa Microsoft Azure. È possibile utilizzare RBAC per gestire utenti, gruppo o l'accesso dell'applicazione per le sottoscrizioni, le risorse e i servizi.

## <a name="basics-of-access-management"></a>Nozioni fondamentali sulla gestione dell'accesso

Controllo degli accessi basata su ruoli fornisce controllo di accesso con granularità fine che è possibile utilizzare per proteggere l'ambiente. È concedere agli utenti le autorizzazioni esatte che necessarie tramite l'assegnazione di un ruolo RBAC a un determinato ambito. L'ambito dell'assegnazione del ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Lettura di [basata sui ruoli di controllo di accesso nel portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) articolo per ottenere informazioni più dettagliate sulla gestione degli accessi.

### <a name="built-in-roles"></a>Ruoli predefiniti

Azure Stack ha tre ruoli di base che è possibile applicare a tutti i tipi di risorsa:

* **Proprietario** possono gestire tutto, incluso l'accesso alle risorse.
* **Collaboratore** possono gestire tutto, tranne l'accesso alle risorse.
* **Lettore** possono visualizzare tutto, ma non possono apportare alcuna modifica.

### <a name="resource-hierarchy-and-inheritance"></a>L'ereditarietà e gerarchia di risorse

Stack di Azure è la seguente gerarchia di risorse:

* Ogni sottoscrizione appartiene a una directory.
* Ogni gruppo di risorse appartiene a una sottoscrizione.
* Ogni risorsa appartiene a un gruppo di risorse.

Accesso concessa a un ambito padre verrà ereditata in ambiti figlio. Ad esempio: 

* Si assegna il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
* Si assegna il ruolo Collaboratore a un'applicazione nell'ambito del gruppo di risorse. L'applicazione può gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### <a name="assigning-roles"></a>Assegnazione di ruoli

È possibile assegnare più di un ruolo a un utente e ogni ruolo può essere associato a un ambito diverso. Ad esempio: 

* Si assegna ruolo TestUser-A il lettore a sottoscrizione-1.
* Si assegna il ruolo di proprietario di TestUser-A a TestVM-1.

Azure [assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) articolo vengono fornite informazioni dettagliate su come visualizzare, assegnare e l'eliminazione di ruoli.

### <a name="resource-hierarchy-and-inheritance"></a>L'ereditarietà e gerarchia di risorse

Stack di Azure è la seguente gerarchia di risorse:

* Ogni sottoscrizione appartiene a una directory.
* Ogni gruppo di risorse appartiene a una sottoscrizione.
* Ogni risorsa appartiene a un gruppo di risorse.

Accesso concessa a un ambito padre verrà ereditata in ambiti figlio. Ad esempio: 

* Assegnare il **lettore** ruolo a un gruppo di Azure AD nell'ambito di sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
* Assegnare il **collaboratore** ruolo a un'applicazione nell'ambito del gruppo di risorse. L'applicazione può gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### <a name="assigning-roles"></a>Assegnazione di ruoli

È possibile assegnare più di un ruolo a un utente e ogni ruolo può essere associato a un ambito diverso. Ad esempio: 

* Si assegna ruolo TestUser-A il lettore a sottoscrizione-1.
* Si assegna il ruolo di proprietario di TestUser-A a TestVM-1.

Azure [assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) articolo vengono fornite informazioni dettagliate su come visualizzare, assegnare e l'eliminazione di ruoli.

## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente

I passaggi seguenti descrivono come configurare le autorizzazioni per un utente.

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel riquadro di spostamento a sinistra scegliere **Gruppi di risorse**.
3. Scegliere il nome del gruppo di risorse che si desidera impostare le autorizzazioni per.
4. Nel riquadro di navigazione del gruppo di risorse, scegliere **Access control (IAM)**. Il **il controllo degli accessi** Vista sono elencati gli elementi che dispongono dell'accesso al gruppo di risorse. È possibile filtrare questi risultati e utilizzare una barra dei menu per aggiungere o rimuovere le autorizzazioni.
5. Nel **il controllo degli accessi** menu barra, scegliere **+ Aggiungi**.
6. Nel **aggiungere le autorizzazioni**:

   * Scegliere il ruolo che si desidera assegnare il **ruolo** elenco a discesa.
   * Fare clic sulla risorsa che si desidera assegnare il **assegnare l'accesso** elenco a discesa.
   * Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.

7. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Creare entità servizio](azure-stack-create-service-principals.md)