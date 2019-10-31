---
title: Come richiedere l'autenticazione a più fattori per l'accesso da reti non attendibili con accesso condizionale Azure Active Directory (Azure AD) | Microsoft Docs
description: Informazioni su come configurare un criterio di accesso condizionale in Azure Active Directory (Azure AD) per i tentativi di accesso da reti non attendibili.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec09c1ecb94a5ae189317d89cce4bc8f279b48
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175694"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Procedura: richiedere l'autenticazione a più fattori per l'accesso da reti non attendibili con accesso condizionale   

Azure Active Directory (Azure AD) consente l'accesso Single Sign-On ai dispositivi, alle app e ai servizi da qualsiasi posizione. Gli utenti possono accedere alle app cloud non solo dalla rete dell'organizzazione, ma anche da qualsiasi posizione Internet non attendibile. Per l'accesso da reti non attendibili una procedura consigliata comune è richiedere l'autenticazione MFA.

Questo articolo fornisce le informazioni necessarie per configurare criteri di accesso condizionale che richiedono l'autenticazione a più fattori per l'accesso da reti non attendibili. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- [Concetti di base](overview.md) di Azure ad l'accesso condizionale 
- [Procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nella portale di Azure

## <a name="scenario-description"></a>Descrizione dello scenario

Per bilanciare in modo efficiente sicurezza e produttività, potrebbe essere sufficiente richiedere solo una password per gli accessi dalla rete aziendale. Tuttavia, l'accesso da un percorso di rete non attendibile comporta un rischio maggiore che gli accessi non vengano eseguiti da utenti legittimi. Per risolvere questo problema, è possibile bloccare gli accessi da reti non attendibili. In alternativa, è possibile anche richiedere l'autenticazione a più fattori (MFA) per ottenere un'ulteriore sicurezza del fatto che è stato effettuato il tentativo dal legittimo proprietario dell'account. 

Con l'accesso condizionale Azure AD, è possibile soddisfare questo requisito con un singolo criterio che concede l'accesso: 

- Alle app cloud selezionato
- Per utenti e gruppi selezionati  
- Richiesta dell'autenticazione a più fattori 
- Quando l'accesso viene eseguito da: 
   - Un percorso che non è attendibile

## <a name="implementation"></a>Implementazione

La sfida di questo scenario consiste nel tradurre *l'accesso da un percorso di rete non attendibile* a una condizione di accesso condizionale. In un criterio di accesso condizionale è possibile configurare la [condizione locations](location-condition.md) per risolvere gli scenari correlati ai percorsi di rete. La condizione dei percorsi consente di selezionare posizioni specifiche, che rappresentano raggruppamenti logici di intervalli di indirizzi IP, paesi e aree geografiche.  

In genere, l'organizzazione è proprietaria di uno o più intervalli di indirizzi, ad esempio, 199.30.16.0-199.30.16.15.
È possibile configurare una località denominata da:

- Specifica di questo intervallo (199.30.16.0/28) 
- Assegnare un nome descrittivo, ad esempio **Rete aziendale** 

Anziché tentare di definire quali sono tutte le posizioni che non vengono considerati attendibili, è possibile:

- Includere qualsiasi posizione 

   ![Accesso condizionale](./media/untrusted-networks/02.png)

- Escludere tutte le località attendibili 

   ![Accesso condizionale](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

Con l'approccio descritto in questo articolo, è ora possibile configurare criteri di accesso condizionale per percorsi non attendibili. La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere [Come distribuire nuovi criteri](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'accesso condizionale, vedere [che cos'è l'accesso condizionale in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
