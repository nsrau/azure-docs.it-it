---
title: Configurare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per l'API di Azure per FHIR
description: Questo articolo descrive come configurare il controllo degli accessi in base al ruolo di Azure per l'API di Azure per FHIR.
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: 8e4b5fdecfc5b9fcb2deb68a482b084a6e6ed6d9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496169"
---
# <a name="configure-azure-rbac-for-fhir"></a>Configurare RBAC di Azure per FHIR 

In questo articolo si apprenderà come usare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/) per assegnare l'accesso al piano dati dell'API di Azure per FHIR. Il controllo degli accessi in base al ruolo di Azure è il metodo preferito per l'assegnazione dell'accesso al piano dati quando gli utenti del piano dati vengono gestiti nel tenant Azure Active Directory associato alla sottoscrizione di Azure. Se si usa un tenant di Azure Active Directory esterno, fare riferimento al [riferimento all'assegnazione locale RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Confermare la modalità RBAC di Azure

Per usare il controllo degli accessi in base al ruolo di Azure, l'API di Azure per FHIR deve essere configurata per usare il tenant della sottoscrizione di Azure per il piano dati e non devono essere assegnati ID oggetto identità È possibile verificare le impostazioni controllando il pannello **autenticazione** dell'API di Azure per FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Confermare la modalità RBAC di Azure":::

L' **autorità** deve essere impostata sul tenant di Azure Active Directory associato alla sottoscrizione e non devono essere presenti GUID nella casella denominata **ID oggetto consentiti**. Si noterà anche che la casella è disabilitata e un'etichetta indica che è necessario usare il controllo degli accessi in base al ruolo di Azure per assegnare i ruoli del piano dati.

## <a name="assign-roles"></a>Assegnare ruoli

Per concedere a utenti, entità servizio o gruppi l'accesso al piano dati FHIR, fare clic su **controllo di accesso (IAM)**, quindi fare clic su **assegnazioni di ruolo** e su **+ Aggiungi**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Aggiungere l'assegnazione di ruolo di Azure":::

Nella selezione del **ruolo** cercare uno dei ruoli predefiniti per il piano dati FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Ruoli dati FHIR predefiniti":::

È possibile scegliere tra:

* Lettore di dati FHIR: è in grado di leggere (e cercare) i dati FHIR.
* FHIR Data writer: è in grado di leggere, scrivere ed eliminare temporaneamente i dati FHIR.
* FHIR data Exporter: può leggere ed esportare `$export` dati (operatore).
* FHIR data Contributor: può eseguire tutte le operazioni del piano dati.

Se questi ruoli non sono sufficienti per le proprie esigenze, è anche possibile [creare ruoli personalizzati](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

Nella casella **Seleziona** cercare un utente, un'entità servizio o un gruppo a cui si vuole assegnare il ruolo.

## <a name="caching-behavior"></a>Comportamento di memorizzazione nella cache

L'API di Azure per FHIR memorizza nella cache le decisioni per un massimo di 5 minuti. Se si concede a un utente l'accesso al server FHIR aggiungendoli all'elenco degli ID oggetto consentiti o se vengono rimossi dall'elenco, è necessario attendere fino a cinque minuti per la propagazione delle modifiche nelle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come assegnare i ruoli di Azure per il piano dati FHIR. Ulteriori informazioni sulle impostazioni aggiuntive per l'API di Azure per FHIR:
 
>[!div class="nextstepaction"]
>[Impostazioni aggiuntive API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)
