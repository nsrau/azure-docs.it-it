---
title: Azure AD Connect la configurazione del nuovo agente per il provisioning cloud
description: Questo argomento descrive come installare il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794302"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect nuova configurazione del provisioning cloud

Dopo aver installato l'agente, è necessario accedere al portale di Azure e configurare il provisioning.  Utilizzare la procedura seguente per abilitare l'agente.

## <a name="configure-provisioning"></a>Configurare il provisioning
Per configurare il provisioning, attenersi alla procedura seguente:

1.  Nel portale di Azure AD fare clic su **Azure Active Directory**
2.  Fare clic su **Azure ad Connect**
3.  Selezionare **gestione del provisioning (anteprima)** 
![](media/how-to-configure/manage1.png)

4.  Fare clic su **nuova configurazione**.
5.  Nella schermata di configurazione il dominio locale è già popolato
6. Immettere un **messaggio di posta elettronica di notifica**. Questo messaggio di posta elettronica verrà notificato 
7. Quando il provisioning non è integro.  
8. Spostare il selettore in **Abilita** e fare clic su **Salva**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Provisioning dell'ambito per utenti e gruppi specifici
Se si desidera definire l'ambito dell'agente per sincronizzare solo utenti e gruppi specifici, è possibile eseguire questa operazione. È possibile definire l'ambito usando gruppi AD locali o unità organizzative. Non è possibile configurare gruppi e unità organizzative all'interno di una configurazione. 

1.  Nel portale di Azure AD fare clic su **Azure Active Directory**
2.  Fare clic su **Azure ad Connect**
3.  Selezionare **Gestisci provisioning (anteprima)**
4.  In **configurazione** fare clic sulla configurazione.  
![](media/how-to-configure/scope1.png)

5.  In **Configura**selezionare **tutti gli utenti** per modificare l'ambito della regola di configurazione.
![](media/how-to-configure/scope2.png)

6. A destra, è possibile modificare l'ambito in modo da includere solo i gruppi di sicurezza immettendo il nome distinto del gruppo e facendo clic su **Aggiungi**.
![](media/how-to-configure/scope3.png)

7. In alternativa, modificarlo in modo da includere solo unità organizzative specifiche. Fare clic su **fine** e quindi su **Salva**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Riavviare il provisioning 
Se non si vuole attendere la successiva esecuzione pianificata, è possibile attivare l'esecuzione del provisioning usando il pulsante Riavvia provisioning. 
1.  Nel portale di Azure AD fare clic su **Azure Active Directory**
2.  Fare clic su **Azure ad Connect**
3.  Selezionare **Gestisci provisioning (anteprima)**
4.  In **configurazione** fare clic sulla configurazione.  
![](media/how-to-configure/scope1.png)

5.  Nella parte superiore fare clic su **Riavvia provisioning**.

## <a name="removing-a-configuration"></a>Rimozione di una configurazione
Se si desidera eliminare una configurazione, è possibile eseguire questa operazione attenendosi alla procedura riportata di seguito.

1.  Nel portale di Azure AD fare clic su **Azure Active Directory**
2.  Fare clic su **Azure ad Connect**
3.  Selezionare **Gestisci provisioning (anteprima)**
4.  In **configurazione** fare clic sulla configurazione.  
![](media/how-to-configure/scope1.png)

5.  Nella parte superiore fare clic su **Elimina**.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Non vi è alcuna conferma prima di eliminare una configurazione, quindi assicurarsi che questa sia l'azione che si vuole eseguire prima di fare clic su **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect provisioning cloud?](what-is-cloud-provisioning.md)
