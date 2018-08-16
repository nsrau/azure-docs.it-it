---
title: Informazioni su come configurare criteri di accesso condizionale di Azure Active Directory per i tentativi di accesso da reti non attendibili | Microsoft Docs
description: Informazioni su come configurare un criterio di accesso condizionale in Azure Active Directory (Azure AD) per i tentativi di accesso da reti non attendibili.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2dea5686add93d93f35e82445f411035a2451e33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525978"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Informazioni su come configurare criteri di accesso condizionale per i tentativi di accesso da reti non attendibili   

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory (Azure AD) consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. In seguito a questa operazione, gli utenti possono accedere alle app cloud non solo dalla rete dell'organizzazione, ma anche da qualsiasi posizione Internet non attendibile. Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati possono accedere alle app cloud. Un requisito comune in questo contesto consiste nel controllare i tentativi di accesso avviati da reti non attendibili. Questo articolo fornisce le informazioni necessarie per configurare un criterio di accesso condizionale che gestisce questo requisito. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- I concetti di base di accesso condizionale di Azure AD 
- Configurare i criteri di accesso condizionale nel portale di Azure

Vedere:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md) - per una panoramica dell'accesso condizionale 

- [Guida introduttiva: Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](conditional-access/app-based-mfa.md) - per acquisire familiarità con la configurazione dei criteri di accesso condizionale. 


## <a name="scenario-description"></a>Descrizione dello scenario

Per controllare l'equilibrio tra sicurezza e produttività, potrebbe essere sufficiente richiedere all'utente di essere autenticato tramite una password. Tuttavia, quando viene fatto un tentativo di accesso da un percorso di rete non attendibile, c'è un rischio maggiore che gli accessi non siano eseguiti dagli utenti legittimi. Per risolvere questo problema, è possibile bloccare i tentativi di accesso da reti non attendibili. In alternativa, è possibile anche richiedere l'autenticazione a più fattori (MFA) per ottenere un'ulteriore sicurezza del fatto che è stato effettuato il tentativo dal legittimo proprietario dell'account. 

Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito con un criterio singolo che garantisce l'accesso: 

- Alle app cloud selezionato

- Per utenti e gruppi selezionati  

- Richiesta dell'autenticazione a più fattori 

- Quando viene eseguito un tentativo di accesso da: 

    - Un percorso che non è attendibile


## <a name="considerations"></a>Considerazioni

La richiesta di verifica di questo scenario consiste nel tradurre *quando viene eseguito un tentativo di accesso da un percorso che non è attendibile* in una condizione di accesso condizionale. In un criterio di accesso condizionale, è possibile configurare la [condizione dei percorsi](conditional-access/location-condition.md) per indirizzare scenari che riguardano i percorsi di rete. La condizione dei percorsi consente di selezionare posizioni specifiche, che rappresentano raggruppamenti logici di intervalli di indirizzi IP, paesi e aree geografiche.  

In genere, l'azienda dispone di uno o più intervalli di indirizzi, ad esempio, 199.30.16.0 - 199.30.16.24.
È possibile configurare una località denominata da:

- Specificare questo intervallo (199.30.16.0/24) 

- Assegnare un nome descrittivo, ad esempio **Rete aziendale** 


Anziché tentare di definire quali sono tutte le posizioni che non vengono considerati attendibili, è possibile:

- Includi 

    ![Accesso condizionale](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Escludere tutte le località attendibili 

    ![Accesso condizionale](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Implementazione

Con l'approccio descritto in questo articolo, è ora possibile configurare un criterio di accesso condizionale per i percorsi non attendibili. È sempre consigliabile testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino come previsto. Idealmente, è necessario eseguire i test iniziali in un tenant di test se possibile. Per altre informazioni, vedere [Come distribuire un nuovo criterio](conditional-access/best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'argomento, vedere [Cos'è l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).