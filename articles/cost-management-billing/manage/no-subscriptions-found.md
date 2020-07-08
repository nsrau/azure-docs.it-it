---
title: Errore Non sono state trovate sottoscrizioni - Accesso al portale di Azure
description: Fornisce la soluzione al problema per cui si verifica l'errore Non sono state trovate sottoscrizioni quando si accede al portale di Azure o al Centro account di Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: b0a8b30120b53a02402b3f55b893ac7f5d097ac2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102770"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Errore di accesso Non sono state trovate sottoscrizioni per il portale di Azure o il Centro account di Azure

Potrebbe essere visualizzato il messaggio di errore "Non sono state trovate sottoscrizioni" quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions). Questo articolo offre una soluzione al problema.

## <a name="symptom"></a>Sintomo

Quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions), viene visualizzato il messaggio di errore seguente: "Non sono state trovate sottoscrizioni".

## <a name="cause"></a>Causa

Questo problema si verifica se è stata effettuata una selezione nella directory errata o se l'account non ha autorizzazioni sufficienti.

## <a name="solution"></a>Soluzione

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Scenario 1: Il messaggio di errore appare nel [portale di Azure](https://portal.azure.com)

Per risolvere il problema:

* Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.

  ![Selezionare la directory in alto a destra nel portale di Azure](./media/no-subscriptions-found/directory-switch.png)
* Se è selezionata la directory di Azure corretta, ma viene comunque visualizzato il messaggio di errore, [assegnare il ruolo di proprietario all'account](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Scenario 2: Il messaggio di errore appare nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .  

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
