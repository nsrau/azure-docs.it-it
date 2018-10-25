---
title: Come abilitare applicazioni multi-tenant con Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come registrare i tenant di Azure Active Directory dei clienti con Gemelli digitali di Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323888"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Come abilitare applicazioni multi-tenant con Gemelli digitali di Azure

Gli sviluppatori che usano la piattaforma Gemelli digitali di Azure potrebbero voler creare applicazioni multi-tenant. Un'*applicazione multi-tenant* è una singola istanza con provisioning usata per supportare più clienti, ciascuno con i propri dati e privilegi indipendenti.

Questo documento descrive in dettaglio come creare un'app multi-tenant di Gemelli digitali di Azure che supporta diversi clienti e tenant di Azure Active Directory (AD).

## <a name="scenario-summary"></a>Riepilogo dello scenario

In questo scenario immaginare lo sviluppatore S e il cliente C:

- Lo sviluppatore S ha una sottoscrizione di Azure con un tenant di Azure AD.
- Lo sviluppatore S ha distribuito un'istanza di Gemelli digitali nella propria sottoscrizione di Azure.
- Gli utenti nel tenant di Azure AD dello sviluppatore S possono ottenere token per il servizio Gemelli digitali perché Azure AD ha creato un'entità servizio nel tenant di Azure AD dello sviluppatore S.
- Lo sviluppatore S crea ora un'app per dispositivi mobili che si integra direttamente con l'API di gestione di Gemelli digitali.
- Lo sviluppatore S consente quindi al cliente C di usare l'applicazione per dispositivi mobili.
- A questo punto, il cliente C dovrà essere autorizzato a usare l'API di gestione di Gemelli digitali all'interno dell'applicazione dello sviluppatore S.

  > [!IMPORTANT]
  > - Quando il cliente C accede all'applicazione dello sviluppatore S, l'app non è in grado di acquisire i token perché gli utenti del cliente C possano comunicare con l'API di gestione.
  > - Azure AD genera quindi un errore indicante che Gemelli digitali non viene riconosciuto all'interno della directory del cliente C.

## <a name="solution"></a>Soluzione

Per risolvere il problema nello scenario precedente, è necessario eseguire le operazioni seguenti per creare un'entità servizio di Gemelli digitali all'interno del tenant di Azure AD del cliente C:

- Se il cliente C non ha già una sottoscrizione di Azure con un tenant di Azure AD:

  - L'amministratore del tenant di Azure AD del cliente C deve acquistare una [sottoscrizione di Azure con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - L'amministratore del tenant di Azure AD del cliente C deve quindi [collegare il tenant alla nuova sottoscrizione](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Dal [portale di Azure](https://portal.azure.com) l'amministratore del tenant di Azure AD del cliente C deve quindi procedere in questo modo:
  1. Aprire **Sottoscrizioni**.
  1. Selezionare la sottoscrizione con il tenant di Azure AD da usare nell'applicazione dello sviluppatore S.
  1. Selezionare **Provider di risorse**.
  1. Cercare **Microsoft.IoTSpaces**.
  1. Fare clic su **Register**.
  
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di funzioni definite dall'utente con Gemelli digitali di Azure, vedere [Azure Digital Twins UDFs](how-to-user-defined-functions.md) (Funzioni definite dall'utente di Gemelli digitali di Azure).

Per informazioni su come usare il controllo degli accessi in base al ruolo per proteggere ulteriormente l'applicazione con assegnazioni di ruolo, vedere [Controllo degli accessi in base al ruolo in Gemelli digitali](security-create-manage-role-assignments.md).
