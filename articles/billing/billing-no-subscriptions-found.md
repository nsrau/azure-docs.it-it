---
title: Errore Non sono state trovate sottoscrizioni quando si prova ad accedere al portale di Azure o al Centro account di Azure | Microsoft Docs
description: Fornisce la soluzione al problema per cui si verifica l'errore Non sono state trovate sottoscrizioni quando si accede al portale di Azure o al Centro account di Azure.
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Errore Non sono state trovate sottoscrizioni nel portale di Azure o nel Centro account di Azure
È possibile che venga visualizzato il messaggio di errore "Non sono state trovate sottoscrizioni" quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions). Questo articolo offre una soluzione al problema.

## <a name="symptom"></a>Sintomo

Quando si prova ad accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.windowsazure.com/Subscriptions), viene visualizzato il messaggio di errore seguente: "Non sono state trovate sottoscrizioni".

## <a name="cause"></a>Causa

Questo problema si verifica se l'account non ha autorizzazioni sufficienti. 

## <a name="solution"></a>Soluzione

Assicurarsi di accedere con l'account amministratore corretto. Un amministratore account può accedere solo al Centro account. Gli amministratori del servizio e i co-amministratori hanno l'autorizzazione di accesso solo per il portale di Azure o il portale di Azure classico.

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Il messaggio di errore viene visualizzato nel [portale di Azure](https://portal.azure.com)

Per risolvere il problema:

* Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.

  ![Selezionare la directory in alto a destra nel portale di Azure](./media/billing-no-subscriptions-found/directory-switch.png)

* Se è selezionata la directory di Azure corretta, ma viene comunque visualizzato il messaggio di errore, [richiedere che il proprio account venga aggiunto come proprietario](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Il messaggio di errore viene visualizzato nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu Hub, selezionare **Sottoscrizione**.
3. Scegliere la sottoscrizione da controllare, quindi selezionare **Impostazioni**.
4. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) per ottenere una rapida risoluzione del problema. 

