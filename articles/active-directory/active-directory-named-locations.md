---
title: "Località denominate in Azure Active Directory | Microsoft Docs"
description: "Configurando località denominate, è possibile evitare che indirizzi IP di proprietà dell'organizzazione generino falsi positivi per eventi di rischio di tipo Trasferimento impossibile a posizioni atipiche."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e11f19d518b22a7be4f8daf93304821b42e94a9b
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="named-locations-in-azure-active-directory"></a>Località denominate in Azure Active Directory

La funzionalità delle località denominate di Azure Active Directory consente di etichettare intervalli di indirizzi IP attendibili nelle organizzazioni. Nell'ambiente in uso si possono usare le località denominate nel contesto del rilevamento degli [eventi di rischio](active-directory-reporting-risk-events.md). Questa funzionalità consente di ridurre il numero di falsi positivi segnalati per gli eventi di rischio di tipo *Trasferimento impossibile a posizioni atipiche*. 

## <a name="configuration"></a>Configurazione

Per configurare una località denominata:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Nel riquadro sinistro fare clic su **Azure Active Directory**.

    ![Collegamento ad Azure Active Directory nel riquadro sinistro](./media/active-directory-named-locations/01.png)

3. Nella sezione **Sicurezza** del pannello **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Comando Accesso condizionale](./media/active-directory-named-locations/05.png)


4. Nella sezione **Gestisci** del pannello **Accesso condizionale** fare clic su **Località denominate**.

    ![Comando Località denominate](./media/active-directory-named-locations/06.png)


5. Nel pannello **Località denominate** fare clic su **Nuovo percorso**.

    ![Comando Nuovo percorso](./media/active-directory-named-locations/07.png)


6. Nel pannello **Nuovo** eseguire queste operazioni:

    ![Pannello Nuovo](./media/active-directory-named-locations/08.png)

    a. Nella casella **Nome** digitare un nome per la località denominata.

    b. Nella casella **Intervalli IP** digitare un intervallo IP. L'intervallo IP deve essere in formato *CIDR (Classless Inter-Domain Routing)*.  

    c. Fare clic su **Crea**.



## <a name="what-you-should-know"></a>Informazioni utili

**Aggiornamenti in blocco**: quando si creano o si aggiornano le località denominate, per aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli IP. Un caricamento aggiunge gli intervalli IP nel file all'elenco invece di sovrascrivere l'elenco.

![Collegamenti Carica e Scarica](./media/active-directory-named-locations/09.png)


**Limitazioni**: è possibile definire un massimo di 60 località denominate, ognuna con un intervallo IP assegnato. Se si configura una sola località denominata, è possibile definire fino a 500 intervalli IP per tale località.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli eventi di rischio, vedere [Eventi di rischio di Azure Active Directory](active-directory-reporting-risk-events.md).


