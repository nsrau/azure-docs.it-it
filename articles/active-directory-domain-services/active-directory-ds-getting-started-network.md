---
title: 'Azure Active Directory Domain Services: Introduzione | Documentazione Microsoft'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure (anteprima)
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 5e08c37ea63ca45efb4098ca032e1e9e9ffd3c78
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure (anteprima)

> [!div class="op_single_selector"]
> * [ANTEPRIMA - Abilitare Azure AD Domain Services tramite il portale di Azure](active-directory-ds-getting-started-network.md)
> * [Abilitare Azure AD Domain Services tramite il portale di Azure classico](active-directory-ds-getting-started-create-group.md)


## <a name="before-you-begin"></a>Prima di iniziare
Fare riferimento a [Considerazioni sulla rete per Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Attività 2: configurare le impostazioni di rete
L'attività di configurazione successiva consiste nel creare una rete virtuale di Azure e la relativa subnet dedicata. Abilitare Azure Active Directory Domain Services in questa subnet all'interno della rete virtuale. È anche possibile selezionare una rete virtuale esistente e creare la subnet dedicata all'interno di essa.

1. Fare clic su **Rete virtuale** per selezionare una rete virtuale.
2. Nel pannello **Scegli rete virtuale** vengono visualizzate tutte le reti virtuali esistenti. Vengono visualizzate solamente le reti virtuali che appartengono al gruppo di risorse e la posizione di Azure selezionata nella pagina di procedura guidata **Nozioni di base**.

3. Scegliere la rete virtuale esistente in cui Azure Active Directory Domain Services deve essere abilitata. Fare clic su **Crea nuovo** se si preferisce creare una nuova rete virtuale.

    ![Selezionare una rete virtuale](./media/getting-started/domain-services-blade-network-pick-vnet.png)

4. Fare clic su **Subnet** per selezionare una subnet dedicata in questa rete virtuale, all'interno della quale è possibile attivare il nuovo dominio gestito. Nel pannello **Crea subnet**, specificare un nome per la subnet e per concludere fare clic su **OK**. Ad esempio, se si crea una subnet con il nome 'DomainServices', è più semplice per gli altri amministratori comprendere cosa viene implementato all'interno della subnet.

    ![Selezionare una subnet all’interno della rete virtuale](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Linee guida per la selezione di una subnet**
  > 1. Non selezionare la subnet del Gateway per l’implementazione di Azure AD Domain Services poiché non si tratta di una configurazione supportata.
  2. Verificare che la subnet selezionata disponga di sufficiente spazio per gli indirizzi (almeno 3-5 indirizzi IP disponibili).
  >

5. Al termine, fare clic su **OK** per passare alla pagina **Gruppo amministratore** della procedura guidata.


## <a name="next-step"></a>Passaggio successivo
[Attività 3: configurare un gruppo amministrativo e abilitare Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)

