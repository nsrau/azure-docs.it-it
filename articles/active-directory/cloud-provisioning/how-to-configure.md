---
title: Configurazione del nuovo agente di provisioning cloud di Azure AD ConnectAzure AD Connect cloud provisioning new agent configuration
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620979"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Creare una nuova configurazione per il provisioning basato su cloud di Azure AD ConnectCreate a new configuration for Azure AD Connect cloud-based provisioning

Dopo aver installato l'agente, è necessario accedere al portale di Azure e configurare il provisioning cloud di Azure Active Directory (Azure AD) Connect.After you've installed the agent, you need to sign in to the Azure portal and configure Azure Active Directory (Azure AD) Connect cloud provisioning. Seguire questi passaggi per abilitare l'agente.

## <a name="configure-provisioning"></a>Configurare il provisioning
Per configurare il provisioning, attenersi alla seguente procedura.

1.  Nel portale di Azure selezionare **Azure Active Directory.**
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.

    ![Gestire il provisioning (anteprima)Manage provisioning (Preview)](media/how-to-configure/manage1.png)

1.  Selezionare **Nuova configurazione**.
1.  Nella schermata di configurazione, il dominio locale è prepopolato.
1.  Inserisci **un'e-mail**di notifica . Questo messaggio di posta elettronica riceverà una notifica quando il provisioning non è integro.
1.  Spostare il selettore su **Abilita**e selezionare **Salva**.

    ![Provisioning di Azure AD (anteprima)Azure AD provisioning (Preview)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Ambito del provisioning per utenti e gruppi specificiScope provisioning to specific users and groups
È possibile definire l'ambito dell'agente per sincronizzare utenti e gruppi specifici utilizzando gruppi di Active Directory locali o unità organizzative. Non è possibile configurare gruppi e unità organizzative all'interno di una configurazione. 

1.  Nel portale di Azure selezionare **Azure Active Directory.**
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **Configurazione**selezionare la configurazione.

    ![Sezione Configurazione](media/how-to-configure/scope1.png)

1.  In **Configura** selezionare **Tutti gli utenti** per cambiare l'ambito della regola di configurazione.

    ![Opzione Tutti gli utenti](media/how-to-configure/scope2.png)

1. A destra, è possibile modificare l'ambito per includere solo i gruppi di sicurezza. Immettere il nome distinto del gruppo e selezionare **Aggiungi**.

    ![Opzione Gruppi di sicurezza selezionati](media/how-to-configure/scope3.png)

1.  In alternativa, è possibile modificare l'ambito per includere solo unità organizzative specifiche. Selezionare **Fatto** e **Salva**.  
2.  Dopo aver modificato l'ambito, è necessario [riavviare](#restart-provisioning) il provisioning per avviare una sincronizzazione immediata delle modifiche.

    ![Opzione unità organizzative selezionate](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Riavviare il provisioning 
Se non si vuole attendere la prossima esecuzione pianificata, attivare l'esecuzione del provisioning utilizzando il pulsante **Riavvia provisioning.** 
1.  Nel portale di Azure selezionare **Azure Active Directory.**
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **Configurazione**selezionare la configurazione.

    ![Selezione della configurazione per riavviare il provisioning](media/how-to-configure/scope1.png)

1.  In alto, seleziona **Riavvia provisioning**.

## <a name="remove-a-configuration"></a>Rimuovere una configurazione
Per eliminare una configurazione, attenersi alla seguente procedura.

1.  Nel portale di Azure selezionare **Azure Active Directory.**
1.  Selezionare **Azure AD Connect**.
1.  Selezionare **Gestisci provisioning (anteprima)**.
1.  In **Configurazione**selezionare la configurazione.

    ![Selezione della configurazione per rimuovere la configurazione](media/how-to-configure/scope1.png)

1.  Nella parte superiore della schermata di configurazione, selezionare **Elimina**.

    ![Pulsante Elimina](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Non viene visualizzata alcuna conferma prima di eliminare una configurazione. Assicurarsi che questa sia l'azione che si desidera eseguire prima di selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
