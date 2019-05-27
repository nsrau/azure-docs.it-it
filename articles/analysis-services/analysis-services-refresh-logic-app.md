---
title: Aggiornare con le App per la logica per i modelli di Azure Analysis Services | Microsoft Docs
description: Informazioni su come codificare l'aggiornamento asincrono tramite App per la logica di Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142365"
---
# <a name="refresh-with-logic-apps"></a>Eseguire l'aggiornamento con App per la logica

Con App per la logica e le chiamate REST, è possibile eseguire operazioni di aggiornamento dati automatico di analisi di Azure nei modelli tabulari, tra cui sincronizzazione delle repliche di sola lettura per la query di scalabilità orizzontale.

Per altre informazioni sull'uso delle API REST con Azure Analysis Services, vedere [asincrono di aggiornamento con l'API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido.  Gli esempi in questo articolo utilizzerà dell'entità servizio (SPN) per l'autenticazione ad Azure Analysis Services. Per altre informazioni, vedere [creare un'entità servizio usando il portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Progettazione App per la logica

> [!IMPORTANT]
> Gli esempi seguenti presuppongono che il firewall di Azure Analysis Services è disabilitato.  Se il firewall è abilitato, l'indirizzo IP pubblico dell'iniziatore richiesta deve essere nell'elenco elementi consentiti del firewall di Azure Analysis Services. Per altre informazioni sugli intervalli IP dell'App per la logica per ogni area, vedere [le informazioni di configurazione per le App per la logica di Azure e limiti](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Prerequisiti

#### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)

Per altre informazioni sulla creazione di un'entità servizio, vedere [creare un'entità servizio usando il portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis Services
 
L'entità servizio si crea deve disporre delle autorizzazioni di amministratore di server nel server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurare l'App per la logica

In questo esempio è progettato l'App per la logica da attivare quando viene ricevuta una richiesta HTTP. Abilita l'uso di uno strumento di orchestrazione, ad esempio Azure Data Factory, per attivare l'aggiornamento del modello di Azure Analysis Services.

Dopo aver creato un'App per la logica:

1. Nella finestra di progettazione App per la logica, scegliere la prima azione come **quando una richiesta HTTP viene ricevuta**.

   ![Aggiungere attività di ricezione HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Questo passaggio verrà popolato con l'URL POST HTTP dopo aver salvato l'App per la logica.

2. Aggiungere un nuovo passaggio e cercare **HTTP**.  

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selezionare **HTTP** per aggiungere questa azione.

   ![Aggiungere attività HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configurare l'attività HTTP come segue:

|Proprietà  |Value  |
|---------|---------|
|**Metodo**     |POST         |
|**URI**     | https://*l'area del server*/servers/*nome server aas*/models/*il nome del database*/ <br /> <br /> Ad esempio: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Intestazioni**     |   Content-Type, application/json <br /> <br />  ![Intestazioni](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Per altre informazioni su che costituiscono il corpo della richiesta, vedere [asincrono di aggiornamento con l'API REST - POST /refreshes.](analysis-services-async-refresh.md#post-refreshes). |
|**autenticazione**     |Autenticazione OAuth Active Directory         |
|**Tenant**     |Immettere l'ID tenant di Azure Active Directory         |
|**Destinatari**     |https://*.asazure.windows.net         |
|**ID client**     |Immettere l'ID client nome entità servizio         |
|**Tipo di credenziale**     |`Secret`         |
|**Segreto**     |Immettere il segreto di nome dell'entità servizio         |

Esempio:

![Attività HTTP completata](./media/analysis-services-async-refresh-logic-app/7.png)

Ora testare l'App per la logica.  Nella finestra di progettazione App per la logica, fare clic su **eseguiti**.

![Testare l'App per la logica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Usare l'App per la logica con Azure Data Factory

Dopo avere salvato l'App per la logica, esaminare i **quando una richiesta HTTP viene ricevuta** attività e quindi copiare la **URL POST HTTP** che ora viene generato.  Si tratta dell'URL che può essere utilizzato da Azure Data Factory per eseguire la chiamata asincrona per attivare l'App per la logica.

Di seguito è riportato un esempio di Azure Data Factory Web attività che esegue questa azione.

![Attività Web di Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Usare un'App autonoma per la logica

Se si non prevede di usare uno strumento di orchestrazione, ad esempio Data Factory per attivare l'aggiornamento dei modelli, è possibile impostare l'app per la logica di avviare l'aggiornamento in base a una pianificazione.

Usando l'esempio precedente, eliminare la prima attività e sostituirlo con un **pianificazione** attività.

![Attività di pianificazione](./media/analysis-services-async-refresh-logic-app/12.png)

![Attività di pianificazione](./media/analysis-services-async-refresh-logic-app/13.png)

Questo esempio viene usata **ricorrenza**.

Dopo aver aggiunto l'attività, configurare l'intervallo e frequenza, quindi aggiungere un nuovo parametro e scegliere **a queste ore**.

![Attività di pianificazione](./media/analysis-services-async-refresh-logic-app/16.png)

Selezionare le ore desiderate.

![Attività di pianificazione](./media/analysis-services-async-refresh-logic-app/15.png)

Salvare l'App per la logica.

## <a name="next-steps"></a>Passaggi successivi

[Esempi](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
