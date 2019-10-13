---
title: Aggiornare con app per la logica per i modelli di Azure Analysis Services | Microsoft Docs
description: Informazioni su come scrivere codice per l'aggiornamento asincrono usando app per la logica di Azure.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: acf31bf3e7e8c3a0835640dee36f8435a1eba625
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294610"
---
# <a name="refresh-with-logic-apps"></a>Eseguire l'aggiornamento con App per la logica

Usando le app per la logica e le chiamate REST, è possibile eseguire operazioni automatiche di aggiornamento dei dati nei modelli tabulari di Azure Analysis, inclusa la sincronizzazione delle repliche di sola lettura per la scalabilità orizzontale delle query.

Per altre informazioni sull'uso delle API REST con Azure Analysis Services, vedere [aggiornamento asincrono con l'API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido.  Gli esempi in questo articolo utilizzeranno un'entità servizio (SPN) per l'autenticazione Azure Analysis Services. Per altre informazioni, vedere [creare un'entità servizio usando portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Progettare l'app per la logica

> [!IMPORTANT]
> Gli esempi seguenti presuppongono che il firewall Azure Analysis Services sia disabilitato.  Se il firewall è abilitato, l'indirizzo IP pubblico dell'iniziatore della richiesta deve essere inserito nell'elenco elementi consentiti nel firewall Azure Analysis Services. Per ulteriori informazioni sugli intervalli IP delle app per la logica per area, vedere [limiti e informazioni di configurazione per app per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Prerequisiti

#### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)

Per informazioni sulla creazione di un'entità servizio, vedere [creare un'entità servizio usando portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis Services
 
L'entità servizio creata deve avere le autorizzazioni di amministratore del server nel server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurare l'app per la logica

In questo esempio, l'app per la logica è progettata per essere attivata quando viene ricevuta una richiesta HTTP. Ciò consentirà di utilizzare uno strumento di orchestrazione, ad esempio Azure Data Factory, per attivare l'aggiornamento del modello di Azure Analysis Services.

Dopo aver creato un'app per la logica:

1. Nella finestra di progettazione dell'app per la logica scegliere la prima azione come **quando viene ricevuta una richiesta http**.

   ![Aggiungi attività ricezione HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Questo passaggio verrà popolato con l'URL HTTP POST dopo il salvataggio dell'app per la logica.

2. Aggiungere un nuovo passaggio e cercare **http**.  

   ![Aggiungi attività HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Aggiungi attività HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selezionare **http** per aggiungere l'azione.

   ![Aggiungi attività HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configurare l'attività HTTP come indicato di seguito:

|Proprietà  |Value  |
|---------|---------|
|**Metodo**     |INSERISCI         |
|**URI**     | https://*l'area Server*/Servers/*AAS nome server*/models/*il nome del database*/refreshes <br /> <br /> Ad esempio: https: \//westus. asazure. Windows. NET/Servers/MyServer/Models/AdventureWorks/refreshs|
|**Intestazioni**     |   Content-Type, Application/JSON <br /> <br />  ![Headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Per altre informazioni sulla forma del corpo della richiesta, vedere [aggiornamento asincrono con l'API REST-post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticazione**     |Autenticazione OAuth Active Directory         |
|**Inquilino**     |Compilare il Azure Active Directory TenantId         |
|**Destinatari**     |https://*.asazure.windows.net         |
|**ID client**     |Immettere il nome dell'entità servizio ClientID         |
|**Tipo di credenziale**     |`Secret`         |
|**Segreto**     |Immettere il segreto del nome dell'entità servizio         |

Esempio:

![Attività HTTP completata](./media/analysis-services-async-refresh-logic-app/7.png)

A questo punto, testare l'app per la logica.  Nella finestra di progettazione dell'app per la logica fare clic su **Esegui**.

![Testare l'app per la logica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Utilizzare l'app per la logica con Azure Data Factory

Dopo aver salvato l'app per la logica, esaminare l'attività **quando viene ricevuta una richiesta http** e quindi copiare l' **URL http post** che è ora generato.  Si tratta dell'URL che può essere usato da Azure Data Factory per eseguire la chiamata asincrona per attivare l'app per la logica.

Ecco un esempio Azure Data Factory attività Web che esegue questa azione.

![Attività Web di Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Usare un'app per la logica autosufficiente

Se non si prevede di usare uno strumento di orchestrazione, ad esempio Data Factory per attivare l'aggiornamento del modello, è possibile impostare l'app per la logica in modo da attivare l'aggiornamento in base a una pianificazione.

Utilizzando l'esempio precedente, eliminare la prima attività e sostituirla con un'attività di **pianificazione** .

![Attività pianifica](./media/analysis-services-async-refresh-logic-app/12.png)

![Attività pianifica](./media/analysis-services-async-refresh-logic-app/13.png)

In questo esempio verrà usata la **ricorrenza**.

Una volta aggiunta l'attività, configurare l'intervallo e la frequenza, quindi aggiungere un nuovo parametro e scegliere **in queste ore**.

![Attività pianifica](./media/analysis-services-async-refresh-logic-app/16.png)

Selezionare le ore desiderate.

![Attività pianifica](./media/analysis-services-async-refresh-logic-app/15.png)

Salvare l'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

[Esempi](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
