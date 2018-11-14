---
title: Abilitare le applicazioni multi-tenant con Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come registrare i tenant di Azure Active Directory dei clienti con Gemelli digitali di Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259888"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Abilitare le applicazioni multi-tenant con Gemelli digitali di Azure

Gli sviluppatori che usano la piattaforma Gemelli digitali di Azure in genere vogliono creare applicazioni multi-tenant. Un'*applicazione multi-tenant* è una singola istanza sottoposta a provisioning che supporta più clienti. Ogni cliente dispone di privilegi e dati indipendenti propri.

Questo documento descrive in dettaglio come creare un'app multi-tenant con Gemelli digitali di Azure che supporta molti clienti e tenant di Azure Active Directory (Azure AD).

## <a name="scenario-summary"></a>Riepilogo dello scenario

In questo scenario immaginare lo sviluppatore S e il cliente C:

- Lo sviluppatore S ha una sottoscrizione di Azure con un tenant di Azure AD.
- Lo sviluppatore S distribuisce un'istanza di Gemelli digitali di Azure nella propria sottoscrizione di Azure.
- Gli utenti nel tenant di Azure AD dello sviluppatore S possono ottenere i token per il servizio Gemelli digitali di Azure perché Azure AD ha creato un'entità servizio nel tenant di Azure AD dello sviluppatore S.
- Lo sviluppatore S crea ora un'app per dispositivi mobili che si integra direttamente con l'API di gestione di Gemelli digitali di Azure.
- Lo sviluppatore S consente al cliente C di usare l'applicazione per dispositivi mobili.
- Il cliente C deve essere autorizzato a usare l'API di gestione di Gemelli digitali di Azure all'interno dell'applicazione dello sviluppatore S.

  > [!IMPORTANT]
  > - Quando il cliente C accede all'applicazione dello sviluppatore S, l'app non riesce ad acquisire i token perché gli utenti del cliente C possano comunicare con l'API di gestione.
  > - Azure AD genera un errore indicante che Gemelli digitali di Azure non viene riconosciuto all'interno della directory del cliente C.

## <a name="solution"></a>Soluzione

Per risolvere il problema dello scenario precedente, è necessario eseguire le azioni seguenti per creare un'entità servizio di Gemelli digitali di Azure all'interno del tenant di Azure AD del cliente C:

- Se il cliente C non dispone già di una sottoscrizione di Azure con un tenant di Azure AD:

  - L'amministratore del tenant di Azure AD del cliente C deve acquistare una [sottoscrizione di Azure con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - L'amministratore del tenant di Azure AD del cliente C deve quindi [collegare il tenant alla nuova sottoscrizione](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Nel [portale di Azure](https://portal.azure.com) l'amministratore del tenant di Azure AD del cliente C deve quindi seguire questa procedura:

  1. Aprire **Sottoscrizioni**.
  1. Selezionare la sottoscrizione con il tenant di Azure AD da usare nell'applicazione dello sviluppatore S.
  1. Selezionare **Provider di risorse**.
  1. Cercare **Microsoft.IoTSpaces**.
  1. Selezionare **Registra**.
  
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare le funzioni definite dall'utente con Gemelli digitali di Azure, vedere [Azure Digital Twins UDFs](how-to-user-defined-functions.md) (Funzioni definite dall'utente di Gemelli digitali di Azure).

Per informazioni su come usare il controllo degli accessi in base al ruolo per proteggere ulteriormente l'applicazione con assegnazioni di ruolo, vedere [Azure Digital Twins role-based access control](security-create-manage-role-assignments.md) (Controllo degli accessi in base al ruolo in Gemelli digitali di Azure).
