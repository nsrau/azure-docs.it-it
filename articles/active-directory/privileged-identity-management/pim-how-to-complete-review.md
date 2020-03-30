---
title: Completare la revisione dell'accesso dei ruoli di Azure AD in PIM - Azure AD Documenti Microsoft
description: Informazioni su come completare una revisione di accesso dei ruoli di Azure AD in Azure AD Privileged Identity Management (PIM) e visualizzare i risultati
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022271"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Completare una verifica di accesso dei ruoli di Azure AD in Gestione delle identità con privilegiComplete an access review of Azure AD roles in Privileged Identity Management

Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica di accesso](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) invierà automaticamente un messaggio di posta elettronica agli utenti dell'organizzazione di Azure Active Directory (Azure AD) richiedendo loro di esaminare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica è possibile inviare le istruzioni descritte in [Come eseguire una verifica di accesso](pim-how-to-perform-security-review.md).

Trascorso il periodo della verifica di accesso o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

1. Passare al portale di [Azure](https://portal.azure.com/) e selezionare il servizio di gestione **delle identità con privilegi** di Azure AD nel dashboard.
1. Selezionare la sezione **Verifiche dell'accesso** del dashboard.
1. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nel pannello dei dettagli della verifica di accesso sono disponibili diverse opzioni per la gestione di tale revisione.

![Pulsanti di verifica dell'accesso di Gestione identità con privilegi - screenshot](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Promemoria

Se una verifica di accesso è stata impostata in modo che gli utenti verifichino se stessi, il pulsante **Promemoria** invia una notifica.

### <a name="stop"></a>Arresto

Tutte le verifiche di accesso hanno una data di fine, ma il pulsante **Interrompi** consente di completare l'operazione in anticipo. Eventuali utenti non sottoposti a verifica fino a questo momento, non potranno essere controllati dopo l'interruzione della verifica. Non è possibile riavviare una verifica dopo che è stata interrotta.

### <a name="apply"></a>Applica

Al termine di una verifica di accesso in corrispondenza della data di fine o in caso di interruzione manuale, il pulsante **Applica** implementa il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere l'assegnazione di ruolo.  

### <a name="export"></a>Esportazione

Per applicare manualmente i risultati della verifica di accesso, è possibile esportare la verifica. Il pulsante **Esporta** avvia il download di un file con estensione csv. È possibile gestire i risultati in Excel o in altri programmi in grado di aprire i file con estensione csv.

### <a name="delete"></a>Delete

Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la revisione dal servizio Gestione identità con privilegi.

> [!IMPORTANT]
> Non verrà richiesto di confermare questa modifica distruttiva, quindi verificare che si desidera eliminare la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Avviare una verifica di accesso per i ruoli di Azure AD in Gestione delle identità con privilegiStart an access review for Azure AD roles in Privileged Identity Management](pim-how-to-start-security-review.md)
- [Eseguire una verifica di accesso dei ruoli di Azure AD in Gestione delle identità con privilegiPerform an access review of my Azure AD roles in Privileged Identity Management](pim-how-to-perform-security-review.md)
