---
title: Prerequisiti dell'offerta Dynamics 365 for Customer Engagement - Azure Marketplace | Microsoft Docs
description: Prerequisiti per la pubblicazione di un'offerta di applicazione Azure in Azure Marketplace.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: b08312040f9702b8a9100886c198138431012e3f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007371"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Prerequisiti di Dynamics 365 for Customer Engagement

Questo articolo descrive i prerequisiti tecnici e aziendali per la pubblicazione di un'applicazione Dynamics 365 for Customer Engagement nel marketplace AppSource.  Se non è già stato fatto, consultare il [Office 365, Dynamics 365, PowerApps e Power BI offrono Guida alla pubblicazione](../../appsource-offer-publishing-guide.md).


## <a name="technical-requirements"></a>Requisiti tecnici

L'applicazione Dynamics 365 for Customer Engagement deve rispettare le [linee guida per la revisione delle app di Microsoft AppSource](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), che include i requisiti seguenti:


|              Requisito             |        DESCRIZIONE           |
|            ---------------           |      ---------------         |
| Integrazione di Azure Active Directory   | L'app deve consentire l'accesso Single Sign-On federato di Azure Active Directory (SSO federato di AAD) con il consenso abilitato. Per altre informazioni, vedere [Come ottenere la certificazione AppSource per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integrazione con servizi Microsoft Cloud (facoltativa) | Se è necessaria questa funzionalità, l'app deve integrarsi con altri servizi Microsoft Cloud come Microsoft Power BI o Microsoft Flow oppure con servizi di Microsoft Azure come apprendimento automatico o Servizi cognitivi. |
| Tipologia line-of-business            |  L'app deve essere incentrata su un problema o processo di business ben definito, essere principalmente destinata a clienti aziendali e consentire agli utenti di accedere con le credenziali di lavoro (nome utente e password).  |
| Periodo di valutazione gratuita ed esperienza di valutazione |  Un cliente deve essere in grado di usare l'app gratuitamente per un periodo di tempo limitato: "Scarica adesso" per le app gratuite, "Versione di valutazione gratuita" per un periodo specificato, demo "Test drive" o opzione "Contattami" per la richiesta.  |
| Nessuna/bassa configurazione                 | L'app deve essere semplice e rapida da configurare e installare senza richiedere sviluppo o personalizzazioni.  |
| Assistenza clienti                     | Il supporto per l'app deve includere un collegamento al supporto tramite cui i clienti possano ottenere assistenza.  |
| Disponibilità/tempo di attività                  | L'app deve avere un tempo di attività pari ad almeno il 99,9%. |
|  |  |


## <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono gli obblighi procedurali, legali e contrattuali riportati di seguito:

* È necessario essere registrati in [Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) o essere un editore del Marketplace cloud registrato. Se non si è ancora effettuata la registrazione, seguire la procedura in [Diventare un editore del Marketplace cloud](../../become-publisher.md).  (Per il terzo passaggio, usare invece il [modulo di candidatura a partner di AppSource](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >Per l'accesso al portale Cloud Partner è consigliabile usare lo stesso account usato per la registrazione a Microsoft Developer Center. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico di servizi o offerte singole.

* Poiché AppSource non offre un'opzione di pubblicazione abilitata per la commercializzazione, è necessario usare la propri infrastruttura per gli ordini e la fatturazione attuale, senza che siano necessari altri investimenti o modifiche.
* L'utente ha la responsabilità di mettere a disposizione dei clienti il team di supporto in modo ragionevole da un punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite la community.
* L'utente ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
* Occorre aver creato il materiale di marketing associati, ad esempio un nome dell'app ufficiale, la descrizione (in formato HTML), immagini del logo in formato PNG (40x40, 90x90, 115x115 e 255x115 pixel), le condizioni per l'utilizzo e un'informativa sulla privacy.  


## <a name="next-steps"></a>Passaggi successivi

Dopo aver soddisfatto questi requisiti, è possibile [Creare un'offerta Dynamics 365 Customer Engagement](./cpp-create-offer.md) 
