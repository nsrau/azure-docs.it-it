---
title: Trovare un'offerta di sottoscrizione o un gruppo di gestione di Azure |Microsoft Docs
description: "Come esplorare più directory per vedere i gruppi di gestione e le sottoscrizioni"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="find-an-azure-subscription-or-management-group"></a>Trovare una sottoscrizione o un gruppo di gestione di Azure

Se non si riesce a trovare una sottoscrizione o un gruppo di gestione in Azure, è possibile che si stia cercando nella directory sbagliata. Questo scenario potrebbe verificarsi se l'account è presente in più istanze di Azure Active Directory. Ogni [directory attiva è indipendente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence) e l'accesso non viene ereditato tra le directory.      

![Cambiare menu delle directory](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Cambiare directory 
È possibile cambiare facilmente directory nel portale di Azure.
1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Selezionare il nome nella parte in alto a destra della schermata. 
3.  Nella parte inferiore del menu sono elencate tutte le directory a cui si ha accesso.
4.  Selezionare il nome della directory a cui si vuole accedere. 

![Cambiare menu delle directory](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>La risorsa non è disponibile? 
Se si riceve un messaggio di errore del tipo "Questa risorsa non è disponibile" quando si tenta di accedere a una sottoscrizione o a un gruppo di gestione, significa che non si dispone del ruolo necessario per visualizzare questo elemento.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contattare l'amministratore della sottoscrizione o dei gruppi di gestione per ottenere l'accesso.  
* Per le sottoscrizioni, fare riferimento al documento [Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) per informazioni sul ruolo necessario.
* Per i gruppi di gestione, il Controllo degli accessi in base al ruolo di Azure sarà presto disponibile. Contattare l'amministratore di Enterprise Portal per ottenere l'accesso.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Migliorare l'utilizzo dei gruppi di gestione e delle sottoscrizioni nella stessa directory 
È possibile trasferire le sottoscrizioni o i gruppi di gestione nella directory scelta in modo che tutto sia nella stessa posizione.  Il consolidamento delle sottoscrizioni e dei gruppi di gestione nella stessa directory consente di ridurre la necessità di cambiare directory e di ereditare i criteri.  


### <a name="transfer-your-subscriptions"></a>Trasferire le sottoscrizioni 
È possibile spostare una sottoscrizione nella directory associata ai gruppi di gestione. Per ottenere questo risultato, è necessario che l'amministratore delle registrazioni trasferisca la sottoscrizione in un account nella directory di destinazione. Per altre informazioni, accedere a [Enterprise Portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 







