---
title: Azure AD Connect la configurazione del nuovo agente per il provisioning cloud
description: Questo articolo descrive come installare il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628894"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Creare una nuova configurazione per il provisioning Azure AD Connect basato sul cloud

Dopo aver installato l'agente, è necessario accedere al portale di Azure e configurare Azure Active Directory (Azure AD) connettere il provisioning cloud. Per abilitare l'agente, attenersi alla seguente procedura.

## <a name="configure-provisioning"></a>Configurare il provisioning
Per configurare il provisioning, attenersi alla seguente procedura.

 1. Nella portale di Azure selezionare **Azure Active Directory**
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci provisioning**.

 ![Gestire il provisioning](media/how-to-configure/manage1.png)
 
 4. Selezionare **nuova configurazione**.
 5. Nella schermata Configurazione selezionare il dominio e se abilitare la sincronizzazione dell'hash delle password.  Fare clic su **Crea**.  
 
 ![Creare una nuova configurazione](media/how-to-configure/configure1.png)


 6.  Viene visualizzata la schermata Modifica configurazione di provisioning.

   ![Modifica configurazione](media/how-to-configure/configure2.png)

 7. Immettere un **messaggio di posta elettronica di notifica**. Questo messaggio di posta elettronica riceverà una notifica quando il provisioning non è integro.
 8. Spostare il selettore per abilitare e selezionare Salva.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisioning dell'ambito per utenti e gruppi specifici
È possibile definire l'ambito dell'agente per sincronizzare utenti e gruppi specifici usando gruppi di Active Directory locali o unità organizzative. Non è possibile configurare gruppi e unità organizzative all'interno di una configurazione. 

 1.  Nella portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci provisioning (anteprima)**.
 4. In **configurazione**selezionare la configurazione.

 ![Sezione Configurazione](media/how-to-configure/scope1.png)
 
 5. In **Configura**selezionare **Modifica filtri di ambito** per modificare l'ambito della regola di configurazione.
 ![Modifica ambito](media/how-to-configure/scope3.png)
 7. A destra è possibile modificare l'ambito.  Al termine, fare clic su **fine**  e su **Salva** .
 8. Dopo aver modificato l'ambito, è necessario [riavviare il provisioning](#restart-provisioning) per avviare una sincronizzazione immediata delle modifiche.

## <a name="attribute-mapping"></a>Mapping degli attributi
Azure AD Connect provisioning cloud consente di eseguire facilmente il mapping degli attributi tra gli oggetti utente/gruppo locali e gli oggetti in Azure AD.  È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Quindi è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.  Per ulteriori informazioni, vedere [mapping degli attributi](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Provisioning su richiesta
Azure AD Connect provisioning cloud consente di testare le modifiche alla configurazione, applicando queste modifiche a un singolo utente o gruppo.  È possibile usarlo per convalidare e verificare che le modifiche apportate alla configurazione siano state applicate correttamente e siano sincronizzate correttamente con Azure AD.  Per altre informazioni, vedere [provisioning su richiesta](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Riavviare il provisioning 
Se non si vuole attendere la successiva esecuzione pianificata, attivare l'esecuzione del provisioning usando il pulsante **Riavvia provisioning** . 
 1.  Nella portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3.  Selezionare **Gestisci provisioning (anteprima)**.
 4. In **configurazione**selezionare la configurazione.

   ![Selezione della configurazione per riavviare il provisioning](media/how-to-configure/scope1.png)

 5. Nella parte superiore selezionare **Riavvia provisioning**.

## <a name="remove-a-configuration"></a>Rimuovere una configurazione
Per eliminare una configurazione, attenersi alla seguente procedura.

 1.  Nella portale di Azure selezionare **Azure Active Directory**.
 2. Selezionare **Azure AD Connect**.
 3. Selezionare **Gestisci provisioning (anteprima)**.
 4. In **configurazione**selezionare la configurazione.
   
   ![Selezione della configurazione per rimuovere la configurazione](media/how-to-configure/scope1.png)

 5. Nella parte superiore della schermata di configurazione selezionare **Elimina**.

>[!IMPORTANT]
>Non vi è alcuna conferma prima di eliminare una configurazione. Assicurarsi che si tratta dell'azione che si desidera eseguire prima di selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
