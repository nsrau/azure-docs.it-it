---
title: Come risolvere gli errori di accesso usando i report di Azure Active Directory | Microsoft Docs
description: Informazioni su come risolvere gli errori di accesso usando i report di Azure Active Directory nel portale di Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7225ebd91c4c236b5f54574f1657239420c9f541
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165405"
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
* Un utente con ruolo **Amministratore globale**, **Amministratore della sicurezza**, **Ruolo con autorizzazioni di lettura per la sicurezza** o **Ruolo con autorizzazioni di lettura per i report** per il tenant. Qualsiasi utente, inoltre, può visualizzare i propri accessi. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Risolvere gli errori di accesso usando il report degli accessi

1. Passare al [portale di Azure](https://portal.azure.com) e selezionare la directory.
2. Selezionare **Azure Active Directory** e quindi scegliere **Accessi** dalla sezione **Monitoraggio**. 
3. Usare i filtri forniti per circoscrivere l'errore, in base a nome utente o identificatore di oggetto, nome dell'applicazione o data. Selezionare inoltre **Errore** nell'elenco **Stato** per visualizzare solo gli accessi non riusciti. 

    ![Filtrare i risultati](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identificare l'accesso non riuscito da analizzare e selezionarlo. Verrà aperta una finestra di dettagli aggiuntiva con altre informazioni sull'accesso non riuscito. Annotare quanto indicato in **Codice errore di accesso** e **Motivo dell'errore**. 

    ![Selezionare il record](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. È anche possibile trovare queste informazioni nella scheda **Risoluzione dei problemi e supporto tecnico** nella finestra dei dettagli.

    ![Risoluzione dei problemi e supporto](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Il motivo dell'errore descrive l'errore. Ad esempio, nello scenario precedente, il motivo dell'errore è **l'uso di una password o un nome utente non valido oppure di una password o un nome utente locale non valido**. Ciò significa che l'utente ha immesso una password o un nome utente non corretto per accedere al portale di Azure. La correzione consiste semplicemente nell'eseguire nuovamente l'accesso con nome utente e password corretti.

7. È possibile ottenere altre informazioni, compresi suggerimenti per la correzione, eseguendo una ricerca del codice di errore, in questo esempio **50126**, nelle [informazioni di riferimento sui codici di errore di accesso](reference-sign-ins-error-codes.md). 

8. Se il problema persiste anche dopo avere eseguito le operazioni consigliate, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) seguendo i passaggi nella scheda **Risoluzione dei problemi e supporto tecnico**. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sui codici di errore di accesso](reference-sign-ins-error-codes.md)
* [Panoramica del report delle attività di accesso](concept-sign-ins.md)
