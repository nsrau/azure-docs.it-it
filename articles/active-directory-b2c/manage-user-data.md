---
title: Gestire i dati utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come eliminare o esportare dati utente in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 4f79fe2219ee16430c83feab727c034bd7ab4041
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482208"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gestire i dati utente in Azure Active Directory B2C

 Questo articolo illustra come è possibile gestire i dati utente in Azure Active Directory B2C (Azure AD B2C) usando le operazioni fornite dall' [API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api). La gestione dei dati utente include l'eliminazione o esportazione di dati dai log di controllo.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminare dati utente

I dati utente vengono archiviati nella directory di Azure Active Directory B2C e nei log di controllo. Tutti i dati di controllo utente vengono conservati per 7 giorni in Azure AD B2C. Se si desidera eliminare i dati utente entro un periodo di 7 giorni, è possibile utilizzare l'operazione [Elimina un utente](https://docs.microsoft.com/graph/api/user-delete) . È necessaria un'operazione di eliminazione per ognuno dei tenant di Azure Active Directory B2C in cui possono trovarsi i dati.

A ogni utente in Azure Active Directory B2C viene assegnato un ID oggetto. L'ID oggetto fornisce un identificatore univoco, da usare per eliminare i dati utente in Azure Active Directory B2C. A seconda dell'architettura in uso, l'ID oggetto può essere un utile identificatore di correlazione in altri servizi, ad esempio tra database finanziari, di marketing e di gestione delle relazioni con i clienti.

Il modo più accurato per ottenere l'ID oggetto è attraverso un percorso di autenticazione con Azure Active Directory B2C. Se si riceve una richiesta dati valida da un utente che usa altri metodi o un processo offline, ad esempio una ricerca da parte di un agente del supporto tecnico per i clienti, può essere necessario trovare l'utente e annotarne l'ID oggetto associato.

L'esempio seguente illustra un possibile flusso di eliminazione di dati:

1. L'utente accede e seleziona **Delete my data** (Elimina i miei dati).
2. L'applicazione offre un'opzione per eliminare i dati in una sezione amministrativa al suo interno.
3. L'applicazione forza un'autenticazione in Azure Active Directory B2C. Azure Active Directory B2C restituisce un token con l'ID oggetto dell'utente all'applicazione.
4. Il token viene ricevuto dall'applicazione e l'ID oggetto viene usato per eliminare i dati utente tramite una chiamata all'API Microsoft Graph. L'API Microsoft Graph Elimina i dati dell'utente e restituisce un codice di stato 200 OK.
5. L'applicazione orchestra l'eliminazione dei dati utente negli altri sistemi dell'organizzazione secondo le esigenze usando l'ID oggetto o altri identificatori.
6. L'applicazione conferma l'eliminazione dei dati e fornisce i passaggi successivi all'utente.

## <a name="export-customer-data"></a>Esportare dati dei clienti

Il processo di esportazione dei dati dei clienti da Azure Active Directory B2C è simile al processo di eliminazione.

I dati utente in Azure Active Directory B2C sono limitati ai seguenti:

- **Dati archiviati in Azure Active Directory**: è possibile recuperare i dati attraverso un percorso di autenticazione di Azure Active Directory B2C usando l'ID oggetto o qualsiasi nome di accesso, ad esempio un indirizzo di posta elettronica o un nome utente.
- **Report degli eventi di controllo specifici dell'utente**: i dati possono essere indicizzati usando l'ID oggetto.

Nell'esempio di flusso di esportazione di dati seguente i passaggi descritti come eseguiti dall'applicazione possono essere eseguiti anche da un processo back-end o da un utente con ruolo di amministratore nella directory:

1. L'utente accede all'applicazione. Azure Active Directory B2C applica l'autenticazione con Azure Multi-Factor Authentication, se necessario.
2. L'applicazione usa le credenziali utente per chiamare un'operazione API Microsoft Graph per recuperare gli attributi utente. L'API Microsoft Graph fornisce i dati dell'attributo in formato JSON. In base allo schema, è possibile impostare il contenuto del token ID in modo da includere tutti i dati personali su un utente.
3. L'applicazione recupera l'attività di controllo dell'utente. L'API Microsoft Graph fornisce i dati dell'evento all'applicazione.
4. L'applicazione aggrega i dati e li rende disponibili all'utente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire il modo in cui gli utenti accedono all'applicazione, vedere [Gestire l'accesso utente](manage-user-access.md).
