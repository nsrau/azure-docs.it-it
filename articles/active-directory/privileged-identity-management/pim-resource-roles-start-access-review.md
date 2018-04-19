---
title: Avviare una verifica di accesso in PIM per le risorse di Azure | Microsoft Docs
description: Spiega come avviare una verifica di accesso in Privileged Identity Management per le risorse di Azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - Ruolo per le risorse - Avviare una verifica di accesso
Le assegnazioni dei ruoli diventano "obsolete" quando gli utenti hanno accessi con privilegi di cui non necessitano più. Per ridurre il rischio associato alle assegnazioni dei ruoli obsolete, gli amministratori dei ruoli con privilegi devono esaminare periodicamente i ruoli assegnati agli utenti. Questo documento illustra i passaggi per l'avvio di una verifica di accesso in Privileged Identity Management (PIM) per risorse di Azure.

Dalla pagina principale dell'applicazione PIM, passare a:

* **Verifiche di accesso** > **Aggiungi**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Quando si fa clic sul pulsante **Aggiungi** viene visualizzato il pannello **Crea una verifica di accesso**. In questo pannello configurare la verifica assegnando un nome e un limite temporale, scegliere un ruolo da verificare e decidere chi eseguirà la verifica.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configurare la verifica
Per creare una verifica di accesso, è necessario assegnare un nome e impostare le date di inizio e di fine.

![Schermata di configurazione della verifica](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Definire una durata della verifica che consenta agli utenti di completare l'operazione. Se la verifica termina prima della data di fine, è sempre possibile arrestare la verifica in anticipo.

### <a name="choose-a-role-to-review"></a>Scegliere un ruolo da verificare
Ogni verifica è incentrata su un solo ruolo. A meno che non si abbia avviato la verifica di accesso dal pannello di un ruolo specifico, è ora necessario scegliere un ruolo.

1. Passare a **Verifica l'appartenenza ai ruoli**
   
    ![Schermata Verifica l'appartenenza ai ruoli](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Scegliere un ruolo dall'elenco.

### <a name="decide-who-will-perform-the-review"></a>Decidere chi eseguirà la verifica
Sono disponibili tre opzioni per l'esecuzione di una verifica. La revisione può essere assegnata a un altro utente, essere eseguita personalmente oppure ogni utente può verificare il proprio accesso.

1. Scegliere una delle opzioni disponibili:
   
   * **Utenti selezionati**: usare questa opzione quando non è noto chi abbia bisogno dell'accesso. Con questa opzione è possibile assegnare l'esecuzione della revisione a un proprietario delle risorse o a un gestore del gruppo.
   * **Assegnato (autonomo)**: usare questa opzione per fare in modo che gli utenti verifichino le proprie assegnazioni di ruolo.
   
2. Passare a **Selezionare i revisori**
   
    ![Schermata Selezionare i revisori](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Avviare la verifica
Infine è disponibile l'opzione per richiedere agli utenti di fornire un motivo se approvano l'accesso. Se si vuole, aggiungere una descrizione della verifica e selezionare **Start**(Avvia).

È necessario informare gli utenti che è presente una verifica dell'accesso in attesa e illustrare [come eseguire una verifica dell'accesso](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso
È possibile tenere traccia dello stato di avanzamento delle verifiche eseguite dai revisori nella sezione Verifiche di accesso del dashboard delle risorse di Azure in PIM. Nessun diritto di accesso verrà modificato nella directory fino al [completamento della verifica](pim-resource-roles-complete-access-review.md).

Fino al termine del periodo di verifica, è possibile ricordare agli utenti di completare la verifica o arrestare la verifica in anticipo nella sezione Verifiche di accesso.

