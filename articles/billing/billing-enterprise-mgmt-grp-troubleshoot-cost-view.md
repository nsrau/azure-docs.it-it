---
title: Risolvere i problemi delle visualizzazioni dei costi aziendali di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi con le visualizzazioni dei costi aziendali nel portale di Azure.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615736"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Risolvere i problemi delle visualizzazioni dei costi aziendali

Nelle registrazioni Enterprise sono disponibili varie impostazioni che potrebbero impedire agli utenti di visualizzare i costi all'interno della registrazione.  Queste impostazioni vengono gestite dall'amministratore delle registrazioni. In alternativa, se la registrazione non è stata acquistata direttamente tramite Microsoft, le impostazioni vengono gestite dal partner.  Questo articolo illustra queste impostazioni e l'impatto che hanno sulla registrazione. Queste impostazioni sono indipendenti dai ruoli di controllo degli accessi in base al ruolo di Azure.

## <a name="enabling-access-to-costs"></a>Abilitazione dell'accesso ai costi

Viene visualizzato un messaggio Non autorizzato o in cui è indicato che *le visualizzazioni dei costi sono disabilitate nella registrazione* quando si cercano le informazioni sui costi?
![Screenshot che mostra il messaggio "non autorizzato" nel campo del costo corrente per la sottoscrizione.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Il problema può dipendere da una delle cause seguenti:

1. Azure è stato acquistato tramite un partner Enterprise, il quale non ha ancora rilasciato i prezzi. Contattare il partner per eseguire l'aggiornamento dell'impostazione dei prezzi in [Enterprise Portal](https://ea.azure.com).
2. I clienti diretti con Contratto Enterprise hanno altre due possibilità:
    * Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di **visualizzazione dei costi per i proprietari degli account**.  
    * Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di **visualizzazione dei costi per gli amministratori di reparto**.
    * Contattare l'amministratore delle registrazioni per ottenere l'accesso. L'amministratore delle registrazioni può aggiornare le impostazioni nell'[Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Screenshot che mostra le impostazioni dell'Enterprise Portal per la visualizzazione degli addebiti.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>La risorsa non è disponibile.

Se si riceve un messaggio di errore del tipo "Questa risorsa non è disponibile" quando si tenta di accedere a una sottoscrizione o a un gruppo di gestione, significa che non si dispone del ruolo necessario per visualizzare questo elemento.  

![Screenshot con il messaggio indicante che la risorsa non è disponibile.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Chiedere l'accesso all'amministratore del gruppo di gestione o della sottoscrizione di Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
