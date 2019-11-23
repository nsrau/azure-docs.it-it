---
title: Require MFA from untrusted networks - Azure Active Directory
description: Learn how to configure a Conditional Access policy in Azure Active Directory (Azure AD) to for access attempts from untrusted networks.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380001"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to: Require MFA for access from untrusted networks with Conditional Access   

Azure Active Directory (Azure AD) consente l'accesso Single Sign-On ai dispositivi, alle app e ai servizi da qualsiasi posizione. Gli utenti possono accedere alle app cloud non solo dalla rete dell'organizzazione, ma anche da qualsiasi posizione Internet non attendibile. Per l'accesso da reti non attendibili una procedura consigliata comune è richiedere l'autenticazione MFA.

This article gives you the information you need to configure a Conditional Access policy that requires MFA for access from untrusted networks. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>Descrizione dello scenario

Per bilanciare in modo efficiente sicurezza e produttività, potrebbe essere sufficiente richiedere solo una password per gli accessi dalla rete aziendale. Tuttavia, l'accesso da un percorso di rete non attendibile comporta un rischio maggiore che gli accessi non vengano eseguiti da utenti legittimi. Per risolvere questo problema, è possibile bloccare gli accessi da reti non attendibili. In alternativa, è possibile anche richiedere l'autenticazione a più fattori (MFA) per ottenere un'ulteriore sicurezza del fatto che è stato effettuato il tentativo dal legittimo proprietario dell'account. 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access: 

- Alle app cloud selezionato
- Per utenti e gruppi selezionati  
- Richiesta dell'autenticazione a più fattori 
- Quando l'accesso viene eseguito da: 
   - Un percorso che non è attendibile

## <a name="implementation"></a>Implementazione

The challenge of this scenario is to translate *access from an untrusted network location* into a Conditional Access condition. In a Conditional Access policy, you can configure the [locations condition](location-condition.md) to address scenarios that are related to network locations. La condizione dei percorsi consente di selezionare posizioni specifiche, che rappresentano raggruppamenti logici di intervalli di indirizzi IP, paesi e aree geografiche.  

Typically, your organization owns one or more address ranges, for example, 199.30.16.0 - 199.30.16.15.
È possibile configurare una località denominata da:

- Specifying this range (199.30.16.0/28) 
- Assegnare un nome descrittivo, ad esempio **Rete aziendale** 

Anziché tentare di definire quali sono tutte le posizioni che non vengono considerati attendibili, è possibile:

- Includere qualsiasi posizione 

   ![Accesso condizionale](./media/untrusted-networks/02.png)

- Escludere tutte le località attendibili 

   ![Accesso condizionale](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

With the approach outlined in this article, you can now configure a Conditional Access policy for untrusted locations. La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere [Come distribuire nuovi criteri](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Passaggi successivi

If you would like to learn more about Conditional Access, see [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
