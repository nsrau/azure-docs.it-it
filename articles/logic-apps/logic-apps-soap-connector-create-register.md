---
title: Creare e registrare connettori SOAP - App per la logica di Azure | Microsoft Docs
description: Configurare connettori SOAP da usare in App per la logica di Azure
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 031762e5639fc52e0b0a6a5bf8d12db25da25e12
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Creare e registrare connettori SOAP in App per la logica di Azure

Per integrare i servizi SOAP nei flussi di lavoro di un'app per la logica, è possibile creare e registrare un connettore SOAP (Simple Object Access Protocol) personalizzato usando WSDL (Web Services Description Language) per descrivere il servizio SOAP. I connettori SOAP funzionano come i connettori predefiniti, quindi è possibile usarli nello stesso modo degli altri connettori nelle app per la logica.


## <a name="prerequisites"></a>prerequisiti

Per registrare un connettore SOAP, è necessario quanto segue:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, iscriversi per ottenere una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Qualsiasi elemento tra i seguenti:
  * Un URL di un file WSDL che definisce il servizio SOAP e le API
  * Un file WSDL che definisce il servizio SOAP e le API

  Per questa esercitazione, è possibile usare il [servizio SOAP Orders](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl) di esempio.

* Facoltativo: un'immagine da usare come icona per il connettore personalizzato


## <a name="1-create-your-connector"></a>1. Creare il connettore

1. Scegliere **Nuovo** dal menu principale di Azure nel portale di Azure. Nella casella di ricerca immettere "connettore app per la logica" come filtro e premere INVIO.

   ![Nuovo, ricerca di "connettore app per la logica"](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Nell'elenco dei risultati scegliere **Connettore per app per la logica** > **Crea**.

   ![Creare un connettore per App per la logica](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Specificare i dettagli per la registrazione del connettore, come descritto nella tabella. Al termine, scegliere **Aggiungi al dashboard** > **Crea**.

   ![Dettagli di un connettore personalizzato per App per la logica](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Proprietà | Valore consigliato | Descrizione | 
   | -------- | --------------- | ----------- | 
   | **Nome** | *soap-connector-name* | Specificare un nome per il connettore. | 
   | **Sottoscrizione** | *nome-sottoscrizione-Azure* | Selezionare la sottoscrizione di Azure. | 
   | **Gruppo di risorse** | *nome-gruppo-risorse-Azure* | Creare o selezionare un gruppo di Azure per organizzare le risorse di Azure. | 
   | **Posizione** | *area-distribuzione* | Selezionare un'area di distribuzione per il connettore. | 
   |||| 

   Dopo che Azure distribuisce il connettore, viene automaticamente visualizzato il menu del connettore per app per la logica. 
   In caso contrario, scegliere il connettore SOAP nel dashboard di Azure.

## <a name="2-define-your-connector"></a>2. Definire il connettore

Specificare ora il file WSDL o l'URL per creare il connettore, l'autenticazione usata dal connettore, nonché le azioni e i trigger forniti dal connettore


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Specificare il file WSDL o l'URL per il connettore

1. Se non è già selezionato, scegliere **Connettore per app per la logica** dal menu del connettore. Scegliere **Modifica** sulla barra degli strumenti.

   ![Modificare il connettore personalizzato](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Scegliere **Generale** per fornire i dettagli in queste tabelle per la creazione, la protezione e la definizione delle azioni e dei trigger per il connettore SOAP.

   1. Per **Connettori personalizzati** selezionare **SOAP** per **Endpoint API**, in modo da poter fornire il file WSDL che descrive l'API.

      ![Specificare il file WSDL per l'API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Opzione | Format |DESCRIZIONE | 
      | ------ | ------ | ----------- | 
      | **Carica WSDL da file** | *WSDL-file* | Passare al percorso del file WSDL e selezionare il file. | 
      | **Carica WSDL da URL** | http://*percorso-file-wsdl* | Specificare l'URL per il file WSDL del servizio. | 
      | **Da SOAP a REST** |   | Trasformare le API nel servizio SOAP in API REST. | 
      |||| 

   2. Per **Informazioni generali**, caricare un'icona per il connettore. 
   In genere, i campi **Descrizione**, **Host** e **URL di base** vengono popolati automaticamente dal file WSDL. 
   In caso contrario, aggiungere queste informazioni come descritto nella tabella e scegliere **Continua**. 

      ![Dettagli del connettore](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Opzione o impostazione | Format | DESCRIZIONE | 
      | ----------------- | ------ | ----------- | 
      | **Carica icona** | *file-PNG-o-JPG-minore-di-1-MB* | Icona che rappresenta il connettore <p>Colore: preferibilmente un logo bianco su uno sfondo a colori. <p>Dimensioni: logo di circa 160 pixel all'interno di un quadrato di 230 pixel | 
      | **Colore di sfondo dell'icona** | *codice-esadecimale-colore-icona* | <p>Colore di sfondo dell'icona corrispondente al colore di sfondo nel file di icona. <p>Formato: esadecimale. Ad esempio, #007ee5 rappresenta il colore blu. | 
      | **Descrizione** | *descrizione-connettore* | Fornire una breve descrizione del connettore. | 
      | **Host** | *host-connettore* | Specificare il dominio host per il servizio SOAP. | 
      | **URL di base** | *URL-base-connettore* | Specificare l'URL di base per il servizio SOAP. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descrivere l'autenticazione usata dal connettore

1. Scegliere ora **Sicurezza** per esaminare o descrivere l'autenticazione usata dal connettore. L'autenticazione garantisce che le identità degli utenti vengano trasmesse nel modo appropriato tra il servizio e i client.

   Per impostazione predefinita, l'opzione **Tipo di autenticazione** per il connettore è impostata su **Nessuna autenticazione**.
   
   ![Tipo di autenticazione](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Per modificare il tipo di autenticazione, scegliere **Modifica**. È possibile selezionare **Autenticazione di base**. Per usare etichette dei parametri diverse dai valori predefiniti, aggiornarle in **Etichetta parametro**.

   ![Autenticazione di base](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Per salvare il connettore dopo aver immesso le informazioni di sicurezza, nella parte superiore della pagina scegliere **Aggiorna connettore** e quindi **Continua**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Esaminare, aggiornare o definire azioni e trigger per il connettore

1. Scegliere ora **Definizione** per esaminare, modificare o definire nuovi trigger e azioni che gli utenti possono aggiungere ai propri flussi di lavoro.

   Le azioni e i trigger sono basati sulle operazioni definite nel file WSDL, che popola automaticamente la pagina **Definizione** e include i valori di richiesta e risposta. Di conseguenza, se le operazioni necessarie sono già visualizzate, è possibile continuare con il passaggio successivo nel processo di registrazione senza apportare modifiche in questa pagina.

   ![Definizione del connettore](./media/logic-apps-soap-connector-create-register/definition.png)

2. Facoltativamente, per modificare le azioni e i trigger esistenti o aggiungerne di nuovi, [continuare con questi passaggi](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Completare la creazione del connettore

Al termine, scegliere **Aggiorna connettore** per poter distribuire il connettore. 

Congratulazioni! Quando si crea un'app per la logica, il connettore è ora disponibile in Progettazione app per la logica e può essere aggiunto all'app per la logica.

![In Progettazione app per la logica individuare il connettore](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Condividere il connettore con altri utenti di App per la logica

I connettori personalizzati registrati ma non certificati funzionano come i connettori gestiti da Microsoft, ma sono visibili e disponibili *solo* all'utente che ha creato il connettore e a quelli che hanno lo stesso tenant di Azure Active Directory e la stessa sottoscrizione di Azure per le app per la logica nell'area in cui queste app vengono distribuite. Benché facoltativo, in alcuni scenari si potrebbe voler condividere i connettori con altri utenti. 

> [!IMPORTANT]
> Se si condivide un connettore, altri potrebbero iniziare a dipendere dal connettore. 
> ***Se si elimina il connettore, vengono eliminate anche tutte le connessioni al connettore.***
 
Per condividere il connettore con utenti esterni, ad esempio con tutti gli utenti di App per la logica, [inviare il connettore per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>Domande frequenti

**D:** Il connettore SOAP è disponibile a livello generale? </br>
**R:** Il connettore SOAP è in **anteprima** e non è ancora un servizio disponibile a livello generale.

**D:** Esistono limitazioni e problemi noti relativi al connettore SOAP? </br>
**R:** Sì, vedere [Problemi noti e limitazioni del connettore SOAP](../api-management/api-management-api-import-restrictions.md#wsdl).

**D:** esistono limiti per i connettori personalizzati? </br>
**R:** Sì, vedere i [limiti per i connettori personalizzati](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Supporto

* Per ottenere supporto per lo sviluppo e il caricamento o per richiedere funzionalità non ancora disponibili nella registrazione guidata, scrivere all'indirizzo [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). Microsoft monitora questo account per esaminare le domande e i problemi degli sviluppatori, per poi inoltrarli al team appropriato.

* Per porre una domanda, fornire una risposta o ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per contribuire al miglioramento di App per la logica, è possibile votare o inviare idee nel [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passaggi successivi

* [Facoltativo: certificare il connettore](../logic-apps/custom-connector-submit-certification.md)
* [Domande frequenti sui connettori personalizzati](../logic-apps/custom-connector-faq.md)