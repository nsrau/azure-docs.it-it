---
title: Aggiornamento con app per la logica per i modelli di Azure Analysis Services Documenti Microsoft
description: Questo articolo descrive come codificare l'aggiornamento asincrono per Azure Analysis Services usando app per la logica di Azure.This article describes how to code asynchronous refresh for Azure Analysis Services by using Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126946"
---
# <a name="refresh-with-logic-apps"></a>Eseguire l'aggiornamento con App per la logica

Usando le app per la logica e le chiamate REST, è possibile eseguire operazioni di aggiornamento automatico dei dati nei modelli tabulari di Azure Analysis, inclusa la sincronizzazione delle repliche di sola lettura per la scalabilità orizzontale delle query.

Per altre informazioni sull'uso delle API REST con Azure Analysis Services, vedere [Aggiornamento asincrono con l'API REST.](analysis-services-async-refresh.md)

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token Azure Active Directory (OAuth 2) valido.  Gli esempi in questo articolo useranno un'entità servizio (SPN) per l'autenticazione in Azure Analysis Services.The examples in this article will use a Service Principal (SPN) to authenticate to Azure Analysis Services. Per altre informazioni, vedere [Creare un'entità servizio tramite](../active-directory/develop/howto-create-service-principal-portal.md)il portale di Azure.To learn more, see Create a service principal by using Azure portal.

## <a name="design-the-logic-app"></a>Progettare l'app per la logica

> [!IMPORTANT]
> Gli esempi seguenti presuppongono che il firewall di Azure Analysis Services sia disabilitato. Se il firewall è abilitato, l'indirizzo IP pubblico dell'initiator della richiesta deve essere inserito nella whitelist nel firewall di Azure Analysis Services. Per altre informazioni sugli intervalli IP delle app per la logica di Azure per ogni area, vedere Limiti e informazioni di configurazione per le app per la logica di Azure.To learn more about Azure Logic Apps IP ranges per region, see [Limits and configuration information for Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Prerequisiti

#### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)Create a Service Principal (SPN)

Per altre informazioni sulla creazione di un'entità servizio, vedere [Creare un'entità servizio tramite](../active-directory/develop/howto-create-service-principal-portal.md)il portale di Azure.To learn about creating a Service Principal, see Create a service principal by using Azure portal.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis ServicesConfigure permissions in Azure Analysis Services
 
L'entità servizio creata deve disporre delle autorizzazioni di amministratore del server sul server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurare l'app per la logica

In questo esempio, l'app per la logica è progettata per attivare quando viene ricevuta una richiesta HTTP. Ciò consentirà l'uso di uno strumento di orchestrazione, ad esempio Azure Data Factory, per attivare l'aggiornamento del modello di Azure Analysis Services.This will enable the use of an orchestration tool, such as Azure Data Factory, to trigger the Azure Analysis Services model refresh.

Dopo aver creato un'app per la logica:

1. Nella finestra di progettazione dell'app per la logica scegliere la prima azione **come Quando viene ricevuta una richiesta HTTP.**

   ![Aggiungere attività ricevute HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Questo passaggio verrà popolato con l'URL HTTP POST una volta salvata l'app per la logica.

2. Aggiungere un nuovo passaggio e cercare **HTTP**.  

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selezionare **HTTP** per aggiungere questa azione.

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configurare l'attività HTTP come segue:

|Proprietà  |valore  |
|---------|---------|
|**Metodo**     |POST         |
|**URI**     | https://*l'area del server*/servers/*aas nome server*/models/ il nome del*database*/refreshes <br /> <br /> Ad esempio:\/https: /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Intestazioni**     |   Tipo di contenuto, applicazione/json <br /> <br />  ![Headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Per altre informazioni sulla formazione del corpo della richiesta, vedere [Aggiornamento asincrono con l'API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticazione**     |Autenticazione OAuth Active Directory         |
|**Inquilino**     |Compilare il TenantId di Azure Active Directory         |
|**Pubblico**     |all'indirizzo https://.asazure.windows.net         |
|**Client ID**     |Immettere il nome dell'entità servizio ClientID         |
|**Tipo di credenziali**     |Segreto         |
|**Segreto**     |Immettere il segreto del nome dell'entità servizio         |

Esempio:

![Attività HTTP completata](./media/analysis-services-async-refresh-logic-app/7.png)

Ora testare l'app per la logica.  Nella finestra di progettazione dell'app per la logica fare clic su **Esegui**.

![Testare l'app per la logica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Usare l'app per la logica con Azure Data FactoryConsume the Logic App with Azure Data Factory

Dopo aver salvato l'app per la logica, esaminare l'attività **Quando viene ricevuta una richiesta HTTP** e quindi copiare l'URL HTTP **POST** generato.  Si tratta dell'URL che può essere usato da Azure Data Factory per effettuare la chiamata asincrona per attivare l'app per la logica.

Ecco un esempio di attività Web di Azure Data Factory che esegue questa azione.

![Attività Web di Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Usare un'app per la logica autonomaUse a self-contained Logic App

Se non si prevede di usare uno strumento di orchestrazione come Data Factory per attivare l'aggiornamento del modello, è possibile impostare l'app per la logica per attivare l'aggiornamento in base a una pianificazione.

Usando l'esempio precedente, eliminare la prima attività e sostituirla con un'attività **di pianificazione.**

![Pianifica attività](./media/analysis-services-async-refresh-logic-app/12.png)

![Pianifica attività](./media/analysis-services-async-refresh-logic-app/13.png)

In questo esempio viene utilizzata **Recurrence**.

Una volta aggiunta l'attività, configurare Intervallo e Frequenza, quindi aggiungere un nuovo parametro e scegliere **In queste ore**.

![Pianifica attività](./media/analysis-services-async-refresh-logic-app/16.png)

Selezionare le ore desiderate.

![Pianifica attività](./media/analysis-services-async-refresh-logic-app/15.png)

Salvare l'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

[Esempi](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
