---
title: Come risolvere gli errori di accesso usando i report di Azure Active Directory | Microsoft Docs
description: Informazioni su come risolvere gli errori di accesso usando i report di Azure Active Directory nel portale di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: db68ad2a29dcaa53d219b679b9e0f24a50a6f576
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877058"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Procedura: Risolvere gli errori di accesso usando i report di Azure Active Directory

Il [report degli accessi](concept-sign-ins.md) in Azure Active Directory (Azure AD) consente di trovare le risposte alle domande sulla gestione dell'accesso alle applicazioni nell'organizzazione, tra cui:

- Qual è il modello di accesso di un utente?
- Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
- Qual è lo stato di questi accessi?


Il report degli accessi può anche essere utile per risolvere gli errori di accesso per gli utenti dell'organizzazione. In questa guida viene descritto come isolare un errore di accesso nel report degli accessi e usarlo per comprendere la causa radice dell'errore.

## <a name="prerequisites"></a>Prerequisiti

È necessario:

* Un tenant di Azure AD con una licenza Premium (P1/P2). vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso.
* Un utente, che è nel **amministratore globale**, **amministratore della sicurezza**, **lettura per la sicurezza**, oppure **lettore del report** ruolo per il tenant. Qualsiasi utente, inoltre, può visualizzare i propri accessi. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Risolvere gli errori di accesso usando il report degli accessi

1. Passare al [portale di Azure](https://portal.azure.com) e selezionare la directory.
2. Selezionare **Azure Active Directory** e quindi scegliere **Accessi** dalla sezione **Monitoraggio**. 
3. Usare i filtri forniti per circoscrivere l'errore, in base a nome utente o identificatore di oggetto, nome dell'applicazione o data. Inoltre, selezionare **errore** dalle **stato** elenco a discesa per visualizzare solo gli accessi non riusciti. 

    ![Filtrare i risultati](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identificare il sign-in non riuscito da esaminare. Selezionarlo per aprire la finestra Dettagli aggiuntivi con altre informazioni sull'accesso non riuscito. Annotare quanto indicato in **Codice errore di accesso** e **Motivo dell'errore**. 

    ![Selezionare il record](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. È anche possibile trovare queste informazioni nella scheda **Risoluzione dei problemi e supporto tecnico** nella finestra dei dettagli.

    ![Risoluzione dei problemi e supporto](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Il motivo dell'errore descrive l'errore. Nello scenario precedente, ad esempio, il motivo dell'errore è **username non valido o password o nome utente locale non è valido o password**. La correzione consiste semplicemente nell'eseguire nuovamente l'accesso con nome utente e password corretti.

7. È possibile ottenere altre informazioni, compresi suggerimenti per la correzione, eseguendo una ricerca del codice di errore, in questo esempio **50126**, nelle [informazioni di riferimento sui codici di errore di accesso](reference-sign-ins-error-codes.md). 

8. Se il problema persiste anche dopo avere eseguito le operazioni consigliate, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) seguendo i passaggi nella scheda **Risoluzione dei problemi e supporto tecnico**. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sui codici di errore di accesso](reference-sign-ins-error-codes.md)
* [Panoramica del report delle attività di accesso](concept-sign-ins.md)
