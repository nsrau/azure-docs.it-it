---
title: Configurare località denominate in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare località denominate.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b7bd6f4bea111815f647af09ebaa868696b25bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Configurare località denominate in Azure Active Directory

Con le località denominate è possibile etichettare gli intervalli di indirizzi IP attendibili all'interno dell'organizzazione. Azure Active Directory usa le località denominate nel contesto seguente:

- Il rilevamento di [eventi di rischio](active-directory-reporting-risk-events.md) per ridurre il numero di falsi positivi segnalati.  

- [Accesso condizionale basato sulla località](active-directory-conditional-access-locations.md).


Questo articolo descrive come configurare le località denominate nell'ambiente in uso.


## <a name="entry-points"></a>Punti di ingresso

È possibile accedere alla pagina di configurazione delle località denominate nella sezione **Sicurezza** di Azure Active Directory, facendo clic su:

![Punti di ingresso](./media/active-directory-named-locations/34.png)

- **Accesso condizionale:**

    - Nella sezione **Gestisci** fare clic su **Località denominate**.
    
        ![Comando Località denominate](./media/active-directory-named-locations/06.png)

- **Accessi a rischio:**

    - Nella barra degli strumenti in alto fare clic su **Aggiungi intervalli di indirizzi IP noti**.

       ![Comando Località denominate](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Esempio di configurazione

**Per configurare una località denominata:**

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Nel riquadro sinistro fare clic su **Azure Active Directory**.

    ![Collegamento ad Azure Active Directory nel riquadro sinistro](./media/active-directory-named-locations/01.png)

3. Nella sezione **Sicurezza** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Comando Accesso condizionale](./media/active-directory-named-locations/05.png)


4. Nella sezione **Gestisci** della pagina **Accesso condizionale** fare clic su **Località denominate**.

    ![Comando Località denominate](./media/active-directory-named-locations/06.png)


5. Nella pagina **Località denominate** fare clic su **Nuova località**.

    ![Comando Nuovo percorso](./media/active-directory-named-locations/07.png)


6. Nella pagina **Nuovo** eseguire la procedura seguente:

    ![Pannello Nuovo](./media/active-directory-named-locations/56.png)

    a. Nella casella **Nome** digitare un nome per la località denominata.

    b. Nella casella **Intervalli IP** digitare un intervallo IP. L'intervallo IP deve essere in formato *CIDR (Classless Inter-Domain Routing)*.  

    c. Fare clic su **Crea**.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)

- [Condizioni della posizione nell'accesso condizionale di Azure Active Directory](active-directory-conditional-access-locations.md)

- [Eventi di rischio di Azure Active Directory](active-directory-reporting-risk-events.md)

- [Report degli accessi a rischio nel portale di Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)  
