---
title: "Località denominate in Azure Active Directory | Microsoft Docs"
description: "Informazioni sulle località denominate e le modalità di configurazione."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b6f80cde24edcbec68309ba033d4da16ee97b731
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="named-locations-in-azure-active-directory"></a>Località denominate in Azure Active Directory

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



## <a name="what-you-should-know"></a>Informazioni utili

**Aggiornamenti in blocco**: quando si creano o si aggiornano le località denominate, per aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli IP. Un caricamento aggiunge gli intervalli IP nel file all'elenco invece di sovrascrivere l'elenco.

![Collegamenti Carica e Scarica](./media/active-directory-named-locations/09.png)


**Limitazioni**: è possibile definire un massimo di 60 località denominate, ognuna con un intervallo IP assegnato. Se si configura una sola località denominata, è possibile definire fino a 500 intervalli IP per tale località.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su:

- **Eventi di rischio**, vedere [Eventi di rischio di Azure Active Directory](active-directory-reporting-risk-events.md).

- **Accesso condizionale**, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- **Report degli accessi a rischio**, vedere [Report degli accessi a rischio nel portale di Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  
