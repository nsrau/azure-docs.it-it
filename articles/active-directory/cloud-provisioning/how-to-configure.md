---
title: Azure AD Connect la configurazione del nuovo agente per il provisioning cloud
description: Questo articolo descrive come installare il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77620979"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Creare una nuova configurazione per il provisioning Azure AD Connect basato sul cloud

Dopo aver installato l'agente, è necessario accedere al portale di Azure e configurare Azure Active Directory (Azure AD) connettere il provisioning cloud. Per abilitare l'agente, attenersi alla seguente procedura.

## <a name="configure-provisioning"></a>Configurare il provisioning
Per configurare il provisioning, attenersi alla seguente procedura.

1.  Nella portale di Azure selezionare **Azure Active Directory**.
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.

    ![Gestire il provisioning (anteprima)](media/how-to-configure/manage1.png)

1.  Selezionare **nuova configurazione**.
1.  Nella schermata di configurazione, il dominio locale viene prepopolato.
1.  Immettere un **messaggio di posta elettronica di notifica**. Questo messaggio di posta elettronica riceverà una notifica quando il provisioning non è integro.
1.  Spostare il selettore per **abilitare**e selezionare **Salva**.

    ![Provisioning di Azure AD (anteprima)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisioning dell'ambito per utenti e gruppi specifici
È possibile definire l'ambito dell'agente per sincronizzare utenti e gruppi specifici usando gruppi di Active Directory locali o unità organizzative. Non è possibile configurare gruppi e unità organizzative all'interno di una configurazione. 

1.  Nella portale di Azure selezionare **Azure Active Directory**.
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **configurazione**selezionare la configurazione.

    ![Sezione Configurazione](media/how-to-configure/scope1.png)

1.  In **Configura** selezionare **Tutti gli utenti** per cambiare l'ambito della regola di configurazione.

    ![Opzione tutti gli utenti](media/how-to-configure/scope2.png)

1. A destra, è possibile modificare l'ambito in modo da includere solo i gruppi di sicurezza. Immettere il nome distinto del gruppo e selezionare **Aggiungi**.

    ![Opzione gruppi di sicurezza selezionati](media/how-to-configure/scope3.png)

1.  In alternativa, è possibile modificare l'ambito in modo da includere solo unità organizzative specifiche. Selezionare **fatto** e **Salva**.  
2.  Dopo aver modificato l'ambito, è necessario [riavviare il provisioning](#restart-provisioning) per avviare una sincronizzazione immediata delle modifiche.

    ![Opzione unità organizzative selezionate](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Riavviare il provisioning 
Se non si vuole attendere la successiva esecuzione pianificata, attivare l'esecuzione del provisioning usando il pulsante **Riavvia provisioning** . 
1.  Nella portale di Azure selezionare **Azure Active Directory**.
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **configurazione**selezionare la configurazione.

    ![Selezione della configurazione per riavviare il provisioning](media/how-to-configure/scope1.png)

1.  Nella parte superiore selezionare **Riavvia provisioning**.

## <a name="remove-a-configuration"></a>Rimuovere una configurazione
Per eliminare una configurazione, attenersi alla seguente procedura.

1.  Nella portale di Azure selezionare **Azure Active Directory**.
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **configurazione**selezionare la configurazione.

    ![Selezione della configurazione per rimuovere la configurazione](media/how-to-configure/scope1.png)

1.  Nella parte superiore della schermata di configurazione selezionare **Elimina**.

    ![Pulsante Elimina](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Non vi è alcuna conferma prima di eliminare una configurazione. Assicurarsi che si tratta dell'azione che si desidera eseguire prima di selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
