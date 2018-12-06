---
title: Errore Non sono state trovate sottoscrizioni quando si prova ad accedere al portale di Azure o al Centro account di Azure | Microsoft Docs
description: Fornisce la soluzione al problema per cui si verifica l'errore Non sono state trovate sottoscrizioni quando si accede al portale di Azure o al Centro account di Azure.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 584342b3dd223c45495db36ad49d83dece858137
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581783"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Errore Non sono state trovate sottoscrizioni nel portale di Azure o nel Centro account di Azure

Potrebbe essere visualizzato il messaggio di errore "Non sono state trovate sottoscrizioni" quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions). Questo articolo offre una soluzione al problema.

## <a name="symptom"></a>Sintomo

Quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions), viene visualizzato il messaggio di errore seguente: "Non sono state trovate sottoscrizioni".

## <a name="cause"></a>Causa

Questo problema si verifica se è stata effettuata una selezione nella directory errata o se l'account non ha autorizzazioni sufficienti. 

## <a name="solution"></a>Soluzione

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Il messaggio di errore viene visualizzato nel [portale di Azure](https://portal.azure.com)

Per risolvere il problema:

* Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.

  ![Selezionare la directory in alto a destra nel portale di Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Se è selezionata la directory di Azure corretta, ma viene comunque visualizzato il messaggio di errore, [assegnare il ruolo di proprietario all'account](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Il messaggio di errore viene visualizzato nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .  

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
