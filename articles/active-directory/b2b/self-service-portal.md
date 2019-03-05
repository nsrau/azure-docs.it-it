---
title: Portale di iscrizione self-service per Collaborazione B2B - Azure Active Directory | Microsoft Docs
description: La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3ad424e8cab444b2405715eaa468411166ebd9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674277"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portale self-service per l'iscrizione a Collaborazione B2B di Azure AD

I clienti possono eseguire molte operazioni con le funzionalità predefinite esposte tramite il [portale di Azure](https://portal.azure.com) e il [pannello di accesso alle applicazioni](https://myapps.microsoft.com) per gli utenti finali. Tuttavia, potrebbe essere necessario personalizzare il flusso di lavoro di onboarding per gli utenti B2B in base alle esigenze della propria organizzazione. È possibile eseguire questa operazione con [l'API di invito](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Come organizzazione che emette inviti, si potrebbe non sapere in anticipo quali singoli collaboratori esterni avranno bisogno di accedere alle risorse. È necessario un modo che consenta agli utenti delle aziende partner di effettuare l'iscrizione con un set di criteri controllati dall'organizzazione che emette gli inviti. Questo scenario è possibile tramite le API. È disponibile un [progetto di esempio su GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) che esegue questo tipo di operazioni.

Questo progetto GitHub illustra come le organizzazioni possono usare le API per offrire ai partner attendibili una funzionalità di iscrizione self-service basata su criteri, con regole che determinano le app a cui potranno accedere. Gli utenti partner possono ottenere l'accesso alle risorse quando necessario. È possibile farlo in modo sicuro, senza necessità da parte dell'organizzazione che emette gli inviti di eseguire manualmente l'onboarding. È possibile distribuire facilmente il progetto in una sottoscrizione di Azure di propria scelta.

## <a name="as-is-code"></a>Codice cosi com’è

Questo codice viene reso disponibile come esempio per illustrare l'utilizzo dell'API di invito B2B di Azure Active Directory. Deve essere personalizzato dal team di sviluppo o da un partner ed esaminato prima di poter essere distribuito in uno scenario di produzione.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Licenze per la Collaborazione B2B di Azure AD](licensing-guidance.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
