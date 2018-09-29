---
title: Risolvere i problemi delle visualizzazioni dei costi aziendali di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi con le visualizzazioni dei costi aziendali nel portale di Azure.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: cwatson
ms.openlocfilehash: 853307f24574e6cea5841bcace815fea2bbcf2c8
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47390665"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Risolvere i problemi delle visualizzazioni dei costi aziendali 

Nelle registrazioni Enterprise sono disponibili varie impostazioni che potrebbero impedire agli utenti di visualizzare i costi all'interno della registrazione.  Queste impostazioni sono gestite dall'amministratore delle registrazioni o dal partner nel caso di registrazioni non acquistate direttamente da Microsoft.  Questo articolo illustra queste impostazioni e l'impatto che hanno sulla registrazione. Queste impostazioni sono indipendenti dai [ruoli di Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Abilitazione dell'accesso ai costi

Viene visualizzato un messaggio Non autorizzato o in cui è indicato che *le visualizzazioni dei costi sono disabilitate nella registrazione* quando si cercano le informazioni sui costi? ![non autorizzato](media/billing-enterprise-mgmt-groups/unauthorized.png)

Il problema può dipendere da una delle cause seguenti:

1. Azure è stato acquistato tramite un partner Enterprise, il quale non ha ancora rilasciato i prezzi. Per rilasciare i prezzi, contattare il partner per eseguire l'aggiornamento dell'impostazione in [Enterprise Portal](https://ea.azure.com).
2. In alternativa, i clienti diretti con contratto Enterprise hanno altre due possibilità:
    * Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per i proprietari degli account.  
    * Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per gli amministratori di reparto.
    * Contattare l'amministratore delle registrazioni per ottenere l'accesso. L'amministratore delle registrazioni può accedere a [Enterprise Portal](https://ea.azure.com/manage/enrollment) e aggiornare l'impostazione, come illustrato di seguito:

![Impostazioni Enterprise Portal](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>La risorsa non è disponibile? 
Se si riceve un messaggio di errore del tipo "Questa risorsa non è disponibile" quando si tenta di accedere a una sottoscrizione o a un gruppo di gestione, significa che non si dispone del ruolo necessario per visualizzare questo elemento.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contattare l'amministratore della sottoscrizione o dei gruppi di gestione per ottenere l'accesso.  
* Per le sottoscrizioni, fare riferimento al documento [Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) per informazioni sul ruolo necessario.
