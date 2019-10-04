---
title: Integrare le operazioni di sicurezza con Microsoft Graph Security - App per la logica di Azure
description: Migliorare le capacità di protezione, rilevamento e risposta alle minacce per le app attraverso la gestione delle operazioni di sicurezza con Microsoft Graph Security e le app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448049"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Migliorare la protezione dalle minacce integrando le operazioni di sicurezza con Microsoft Graph Security e le app per la logica di Azure

Con le [app per la logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore di [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) è possibile migliorare le modalità di rilevamento, protezione e risposta alle minacce mediante la creazione di flussi di lavoro automatizzati per l'integrazione di prodotti, servizi e partner Microsoft per la sicurezza. È ad esempio possibile creare [playbook per Centro sicurezza di Azure](../security-center/security-center-playbooks.md) che monitorano e gestiscono entità di Microsoft Graph Security come gli avvisi. Di seguito sono riportati alcuni degli scenari supportati dal connettore di Microsoft Graph Security:

* Ottenere avvisi in base a query o all'ID avviso. È ad esempio possibile ottenere un elenco degli avvisi con gravità alta.
* Aggiornare gli avvisi. È ad esempio possibile aggiornare le assegnazioni degli avvisi, aggiungere commenti agli avvisi o contrassegnarli.
* Monitorare la creazione o la modifica degli avvisi creando [sottoscrizioni per gli avvisi (webhook)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Gestire le sottoscrizioni per gli avvisi. È ad esempio possibile ottenere le sottoscrizioni attive, estendere la scadenza per una sottoscrizione o eliminare le sottoscrizioni.

Il flusso di lavoro dell'app per la logica può usare azioni per ottenere risposte dal connettore di Microsoft Graph Security e rendere disponibile l'output per altre azioni nel flusso di lavoro. L'output delle azioni del connettore di Microsoft Graph Security può anche essere usato da altre azioni nel flusso di lavoro. Se ad esempio si ricevono avvisi con gravità alta tramite il connettore di Microsoft Graph Security, è possibile inviare tali avvisi in un messaggio di posta elettronica mediante il connettore di Outlook. 

Per altre informazioni su Microsoft Graph Security, vedere [Microsoft Graph Security API overview](https://aka.ms/graphsecuritydocs) (Panoramica dell'API di Microsoft Graph Security). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md). Se si è interessati a Microsoft Flow o PowerApps, vedere le [informazioni su Flow](https://flow.microsoft.com/) o le [informazioni su PowerApps](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Per usare il connettore di Microsoft Graph Security, è necessario *fornire in modo esplicito* il consenso dell'amministratore del tenant di Azure Active Directory (AD), come previsto dai [requisiti di autenticazione di Microsoft Graph Security](https://aka.ms/graphsecurityauth). Questo consenso richiede l'ID applicazione e il nome del connettore di Microsoft Graph Security, che è anche possibile trovare nel [portale di Azure](https://portal.azure.com):

   | Proprietà | Value |
   |----------|-------|
   | **Nome dell'applicazione** | `MicrosoftGraphSecurityConnector` |
   | **ID applicazione** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Per fornire il consenso per il connettore, l'amministratore del tenant di Azure AD può attenersi a una delle procedure seguenti:

   * [Fornire il consenso dell'amministratore del tenant per le applicazioni di AD Azure](../active-directory/develop/v2-permissions-and-consent.md).

   * Durante la prima esecuzione dell'app per la logica, l'app può richiedere il consenso dell'amministratore del tenant di Azure AD tramite l'[esperienza di consenso per le applicazioni](../active-directory/develop/application-consent-experience.md).
   
* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere alle entità di Microsoft Graph Security, ad esempio gli avvisi. Attualmente questo connettore non dispone di alcun trigger. Di conseguenza, per usare un'azione di Microsoft Graph Security, avviare l'app per la logica con un trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-microsoft-graph-security"></a>Connettersi a Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, aggiungere il trigger ed eventuali altre azioni prima di aggiungere un'azione di Microsoft Graph Security.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione di Microsoft Graph Security, scegliere **Nuovo passaggio**.

   -oppure-

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (+) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "microsoft graph security" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

1. Accedere con le credenziali di Microsoft Graph Security.

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="add-actions"></a>Aggiunta di azioni

Di seguito sono riportate informazioni più dettagliate sull'uso delle varie azioni disponibili con il connettore di Microsoft Graph Security.

### <a name="manage-alerts"></a>Gestisci avvisi

Per filtrare, ordinare oppure ottenere i risultati più recenti, fornire *solo* i [parametri di query ODATA supportati da Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Non specificare* l'URL di base completo o l'azione HTTP, ad esempio `https://graph.microsoft.com/v1.0/security/alerts` o l'operazione `GET` o `PATCH`. Ecco un esempio specifico che illustra i parametri per un'azione **Ottieni avvisi** quando si vuole un elenco degli avvisi con gravità alta:

`Filter alerts value as Severity eq 'high'`

Per altre informazioni sulle query che è possibile usare con questo connettore, vedere la [documentazione di riferimento sugli avvisi di Microsoft Graph Security](https://docs.microsoft.com/graph/api/alert-list). Per creare esperienze ottimizzate con questo connettore, consultare altre informazioni sugli [avvisi delle proprietà dello schema](https://docs.microsoft.com/graph/api/resources/alert) supportati dal connettore.

| Azione | Descrizione |
|--------|-------------|
| **Ottieni avvisi** | Ottenere gli avvisi filtrati in base a una o più [proprietà degli avvisi](https://docs.microsoft.com/graph/api/resources/alert), ad esempio: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Get alert by ID** (Ottieni avviso in base all'ID) | Ottenere un avviso specifico in base all'ID dell'avviso. | 
| **Aggiorna avviso** | Aggiornare un avviso specifico in base all'ID dell'avviso. <p>Per assicurarsi di passare le proprietà obbligatorie e modificabili nella richiesta, vedere le [proprietà modificabili per gli avvisi](https://docs.microsoft.com/graph/api/alert-update). Ad esempio, per assegnare un avviso a un analista della sicurezza in modo che possa svolgere indagini, è possibile aggiornare la proprietà **Assegnato a** dell'avviso. |
|||

### <a name="manage-alert-subscriptions"></a>Gestire le sottoscrizioni per gli avvisi

Microsoft Graph supporta le [*sottoscrizioni*](https://docs.microsoft.com/graph/api/resources/subscription), o [*webhook*](https://docs.microsoft.com/graph/api/resources/webhooks). Per ottenere, aggiornare o eliminare le sottoscrizioni, fornire i [parametri di query ODATA supportati da Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) al costrutto dell'entità di Microsoft Graph e includere `security/alerts` seguito dalla query ODATA. 
*Non includere* l'URL di base, ad esempio `https://graph.microsoft.com/v1.0`. Usare il formato illustrato in questo esempio:

`security/alerts?$filter=status eq 'New'`

| Azione | Descrizione |
|--------|-------------|
| **Create subscriptions** (Crea sottoscrizioni) | [Creare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) per ricevere notifiche in caso di modifiche. È possibile filtrare la sottoscrizione in base agli specifici tipi di avviso a cui si è interessati. È ad esempio creare una sottoscrizione che invia una notifica in caso di avvisi con gravità alta. |
| **Get active subscriptions** (Ottieni sottoscrizioni attive) | [Ottenere le sottoscrizioni non scadute](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Update subscription** (Aggiorna sottoscrizione) | [Aggiornare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-update) specificando l'ID sottoscrizione. Ad esempio, per estendere la sottoscrizione, è possibile aggiornare la proprietà `expirationDateTime` della sottoscrizione. | 
| **Delete subscription** (Elimina sottoscrizione) | [Eliminare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-delete) specificando l'ID sottoscrizione. | 
||| 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](https://aka.ms/graphsecurityconnectorreference) del connettore.

## <a name="get-support"></a>Supporto

In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
