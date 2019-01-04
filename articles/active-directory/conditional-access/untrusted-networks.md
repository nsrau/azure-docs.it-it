---
title: Come richiedere l'autenticazione a più fattori (MFA) per app specifiche per l'accesso da reti non attendibili con l'accesso condizionale di Azure Active Directory (Azure AD) | Microsoft Docs
description: Informazioni su come configurare un criterio di accesso condizionale in Azure Active Directory (Azure AD) per i tentativi di accesso da reti non attendibili.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c40db6c253899d7aab21d277e93b23dd0c6feb97
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314007"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Procedura: Richiedere l'autenticazione a più fattori (MFA) per l'accesso da reti non attendibili con l'accesso condizionale   

Azure Active Directory (Azure AD) consente l'accesso Single Sign-On ai dispositivi, alle app e ai servizi da qualsiasi posizione. Gli utenti possono accedere alle app cloud non solo dalla rete dell'organizzazione, ma anche da qualsiasi posizione Internet non attendibile. Per l'accesso da reti non attendibili una procedura consigliata comune è richiedere l'autenticazione MFA.

Questo articolo fornisce le informazioni necessarie per configurare criteri di accesso condizionale che richiedono l'autenticazione MFA per l'accesso da reti non attendibili. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- I [concetti di base](overview.md) dell'accesso condizionale di Azure AD 
- Le [procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nel portale di Azure



## <a name="scenario-description"></a>Descrizione dello scenario

Per bilanciare in modo efficiente sicurezza e produttività, potrebbe essere sufficiente richiedere solo una password per gli accessi dalla rete aziendale. Tuttavia, l'accesso da un percorso di rete non attendibile comporta un rischio maggiore che gli accessi non vengano eseguiti da utenti legittimi. Per risolvere questo problema, è possibile bloccare gli accessi da reti non attendibili. In alternativa, è possibile anche richiedere l'autenticazione a più fattori (MFA) per ottenere un'ulteriore sicurezza del fatto che è stato effettuato il tentativo dal legittimo proprietario dell'account. 

Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito con un criterio singolo che garantisce l'accesso: 

- Alle app cloud selezionato

- Per utenti e gruppi selezionati  

- Richiesta dell'autenticazione a più fattori 

- Quando l'accesso viene eseguito da: 

    - Un percorso che non è attendibile


## <a name="implementation"></a>Implementazione

La richiesta di verifica di questo scenario consiste nel traslare l'*accesso da un percorso di rete non attendibile* a una condizione di accesso condizionale. In un criterio di accesso condizionale, è possibile configurare la [condizione dei percorsi](location-condition.md) per indirizzare scenari che riguardano i percorsi di rete. La condizione dei percorsi consente di selezionare posizioni specifiche, che rappresentano raggruppamenti logici di intervalli di indirizzi IP, paesi e aree geografiche.  

In genere, l'azienda dispone di uno o più intervalli di indirizzi, ad esempio, 199.30.16.0 - 199.30.16.24.
È possibile configurare una località denominata da:

- Specificare questo intervallo (199.30.16.0/24) 

- Assegnare un nome descrittivo, ad esempio **Rete aziendale** 


Anziché tentare di definire quali sono tutte le posizioni che non vengono considerati attendibili, è possibile:

- Includere qualsiasi posizione 

    ![Accesso condizionale](./media/untrusted-networks/02.png)

- Escludere tutte le località attendibili 

    ![Accesso condizionale](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Distribuzione dei criteri

Con l'approccio descritto in questo articolo, è ora possibile configurare un criterio di accesso condizionale per i percorsi non attendibili. La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere [Come distribuire nuovi criteri](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'argomento, vedere [Cos'è l'accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).