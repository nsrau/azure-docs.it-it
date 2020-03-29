---
title: Messaggi RosettaNet per l'integrazione B2B
description: Messaggi di Exchange RosettaNet in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792426"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet messages for B2B enterprise integration in Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) è un consorzio senza scopo di lucro che ha stabilito processi standard per la condivisione delle informazioni aziendali. Questi standard sono comunemente utilizzati per i processi della catena di approvvigionamento e sono diffusi nei settori dei semiconduttori, dell'elettronica e della logistica. Il consorzio RosettaNet crea e gestisce i processi PIP (Partner Interface Process), che forniscono definizioni comuni dei processi aziendali per tutti gli scambi di messaggi di RosettaNet. RosettaNet si basa su XML e definisce le linee guida per i messaggi, le interfacce per i processi aziendali e i framework di implementazione per la comunicazione tra le aziende.

In [App per la logica](../logic-apps/logic-apps-overview.md)di Azure il connettore RosettaNet consente di creare soluzioni di integrazione che supportano gli standard RosettaNet.In Azure Logic Apps , the RosettaNet connector helps you create integration solutions that support RosettaNet standards. Il connettore è basato su RosettaNet Implementation Framework (RNIF) versione 2.0.01. RNIF è un framework di applicazioni di rete aperto che consente ai partner commerciali di eseguire in modo collaborativo i PIP RosettaNet. Questo framework definisce la struttura dei messaggi, la necessità di riconoscimenti, la codifica MIME (Multipurpose Internet Mail Extensions) e la firma digitale.

In particolare, il connettore fornisce le seguenti funzionalità:

* Codificare o ricevere messaggi RosettaNet.
* Decodificare o inviare messaggi RosettaNet.
* Attendere la risposta e la generazione della notifica dell'errore.

Per queste funzionalità, il connettore supporta tutti i PIP definiti da RNIF 2.0.01. La comunicazione con il partner può essere sincrona o asincrona.

## <a name="rosettanet-concepts"></a>Concetti di RosettaNet

Ecco alcuni concetti e termini che sono specifici per la specifica RosettaNet e sono importanti quando si creano integrazioni basate su RosettaNet:

* **Pip**

  L'organizzazione RosettaNet crea e gestisce i processi PIP (Partner Interface Process), che forniscono definizioni comuni dei processi aziendali per tutti gli scambi di messaggi di RosettaNet. Ogni specifica PIP fornisce un file DTD (Document Type Definition) e un documento di linee guida per i messaggi. Il file DTD definisce la struttura dei messaggi del contenuto del servizio. Il documento della guida dei messaggi, che è un file HTML leggibile, specifica i vincoli a livello di elemento. Insieme, questi file forniscono una definizione completa del processo di business.

   I PIP sono classificati in base a una funzione aziendale di alto livello, o cluster, e da una sottofunzione o segmento. Ad esempio, "3A4" è il PIP per l'ordine di acquisto, mentre "3" è la funzione Gestione ordini e "3A" è la sottofunzione Paese & Inserimento ordine. Per ulteriori informazioni, vedere il [sito RosettaNet](https://resources.gs1us.org).

* **Azione**

  Parte di un PIP, i messaggi di azione sono messaggi aziendali che vengono scambiati tra i partner.

* **Segnale**

   Parte di un PIP, i messaggi di segnale sono riconoscimenti che vengono inviati in risposta ai messaggi di azione.

* **Azione singola e doppia azione**

  Per un PIP ad azione singola, l'unica risposta è un messaggio di segnale di riconoscimento. Per un PIP a doppia azione, l'intestatore riceve un messaggio di risposta e risponde con un riconoscimento oltre al flusso di messaggi a azione singola.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione del contratto e di altri elementi B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.This integration account must be associated with your Azure subscription.

* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) definiti nell'account di integrazione e configurati con il qualificatore "DUNS" in **Business Identities**

* Una configurazione del processo PIP, necessaria per inviare o ricevere messaggi RosettaNet, nell'account di integrazione. La configurazione del processo memorizza tutte le caratteristiche di configurazione PIP. È quindi possibile fare riferimento a questa configurazione quando si crea un accordo con il partner. Per creare una configurazione del processo PIP nell'account di integrazione, vedere [Aggiungere la configurazione](#add-pip)del processo PIP .

* [Certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md) facoltativi per crittografare, decrittografare o firmare i messaggi caricati nell'account di integrazione. I certificati sono necessari solo se si utilizza la firma o la crittografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Aggiungere la configurazione del processo PIP

Per aggiungere una configurazione di processo PIP all'account di integrazione, attenersi alla seguente procedura:

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'account di integrazione.

1. Nel riquadro **Panoramica** selezionare il riquadro **PIP RosettaNet.On** the Overview pane, select the RosettaNet PIP tile.

   ![Scegli il riquadro RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. In **Pip di RosettaNet**scegliere **Aggiungi**. Fornisci i tuoi dati PIP.

   ![Aggiungere i dettagli di RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Il tuo nome PIP |
   | **Codice PIP** | Sì | Il codice PIP a tre cifre. Per ulteriori informazioni, vedere [RosettaNet PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versione PIP** | Sì | Il numero di versione PIP, disponibile in base al codice PIP selezionato |
   ||||

   Per ulteriori informazioni su queste proprietà PIP, visitare il [sito Web RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Al termine, scegliere **OK**, che crea la configurazione PIP.

1. Per visualizzare o modificare la configurazione del processo, selezionare il PIP e scegliere **Modifica come JSON**.

   Tutte le impostazioni di configurazione del processo provengono dalle specifiche del PIP. App per la logica popola la maggior parte delle impostazioni con i valori predefiniti che sono i valori più in genere utilizzati per queste proprietà.

   ![Modificare la configurazione PIP di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Verificare che le impostazioni corrispondano ai valori nella specifica PIP appropriata e soddisfino le esigenze aziendali. Se necessario, aggiornare i valori in JSON e salvare le modifiche.

## <a name="create-rosettanet-agreement"></a>Creare un accordo RosettaNet

1. Nel [portale di Azure](https://portal.azure.com) trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel riquadro **Panoramica** selezionare il riquadro **Contratti.On** the Overview pane, select the Agreements tile.

   ![Scegli il riquadro Contratti](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. In **Contratti** scegliere **Aggiungi**. Fornire i dettagli del contratto.

   ![Aggiungere i dettagli dell'accordo](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Sì | Il nome dell'accordo |
   | **Tipo di accordo** | Sì | Selezionare **RosettaNet**. |
   | **Host Partner (Partner host)** | Sì | Un contratto richiede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
   | **Host Identity (Identità host)** | Sì | Un identificatore per il partner host |
   | **Guest Partner (Partner guest)** | Sì | Un contratto richiede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
   | **identità guest** | Sì | Un identificatore per il partner guest |
   | **Receive Settings (Impostazioni di ricezione)** | Variabile | Queste proprietà si applicano a tutti i messaggi ricevuti dal partner host |
   | **Send Settings (Impostazioni di invio)** | Variabile | Queste proprietà si applicano a tutti i messaggi inviati dal partner host |  
   | **Riferimenti PIP di RosettaNet** | Sì | Il PIP fa riferimento all'accordo. Tutti i messaggi RosettaNet richiedono configurazioni PIP. |
   ||||

1. Per impostare il contratto per la ricezione dei messaggi in arrivo dal partner guest, selezionare **Impostazioni di ricezione**.

   ![Impostazioni di ricezione](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Per abilitare la firma o la crittografia per i messaggi in arrivo, in **Messaggi**selezionare **Messaggio deve essere firmato** o Messaggio deve essere **crittografato** rispettivamente.

      | Proprietà | Obbligatoria | Descrizione |
      |----------|----------|-------------|
      | **Il messaggio deve essere firmato** | No | Firmare i messaggi in arrivo con il certificato selezionato. |
      | **Certificato** | Sì, se la firma è abilitata | Certificato da utilizzare per la firma |
      | **Enable message encryption (Abilita la crittografia dei messaggi)** | No | Crittografare i messaggi in arrivo con il certificato selezionato. |
      | **Certificato** | Sì, se la crittografia è abilitata | Certificato da utilizzare per la crittografia |
      ||||

   1. In ogni selezione selezionare il rispettivo [certificato](./logic-apps-enterprise-integration-certificates.md), aggiunto in precedenza all'account di integrazione, da utilizzare per la firma o la crittografia.

1. Per impostare il contratto per l'invio di messaggi al partner guest, selezionare **Impostazioni di invio**.

   ![Impostazioni di invio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Per abilitare la firma o la crittografia per i messaggi in uscita, in **Messaggi**selezionare **rispettivamente Abilita firma messaggi** o Abilita crittografia **messaggi.** In ogni selezione selezionare il rispettivo algoritmo e [certificato](./logic-apps-enterprise-integration-certificates.md), precedentemente aggiunti all'account di integrazione, da utilizzare per la firma o la crittografia.

      | Proprietà | Obbligatoria | Descrizione |
      |----------|----------|-------------|
      | **Enable message signing (Abilita la firma dei messaggi)** | No | Firmare i messaggi in uscita con l'algoritmo di firma e il certificato selezionati. |
      | **Algoritmo di firma** | Sì, se la firma è abilitata | Algoritmo di firma da utilizzare, in base al certificato selezionato |
      | **Certificato** | Sì, se la firma è abilitata | Certificato da utilizzare per la firma |
      | **Enable message encryption (Abilita la crittografia dei messaggi)** | No | Crittografare la crittografia in uscita con l'algoritmo di crittografia e il certificato selezionati. |
      | **Algoritmo di crittografia** | Sì, se la crittografia è abilitata | Algoritmo di crittografia da utilizzare, in base al certificato selezionato |
      | **Certificato** | Sì, se la crittografia è abilitata | Certificato da utilizzare per la crittografia |
      ||||

   1. In **Endpoint**specificare gli URL necessari da utilizzare per l'invio di messaggi di azione e riconoscimenti.

      | Proprietà | Obbligatoria | Descrizione |
      |----------|----------|-------------|
      | **Action URL** |  Sì | URL da utilizzare per l'invio di messaggi di azione. L'URL è un campo obbligatorio per i messaggi sincroni e asincroni. |
      | **URL di riconoscimento** | Sì | URL da utilizzare per l'invio di messaggi di riconoscimento. L'URL è un campo obbligatorio per i messaggi asincroni. |
      ||||

1. Per impostare il contratto con i riferimenti PIP RosettaNet per i partner, selezionare **RosettaNet PIP references**. In **Nome PIP**, selezionare il nome del PIP creato in precedenza.

   ![Riferimenti PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   La selezione popola le proprietà rimanenti, che si basano sul PIP impostato nell'account di integrazione. Se necessario, è possibile modificare il **ruolo PIP**.

   ![PIP selezionato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Dopo aver completato questi passaggi, sei pronto per inviare o ricevere messaggi RosettaNet.

## <a name="rosettanet-templates"></a>Modelli RosettaNet

Per accelerare lo sviluppo e consigliare i modelli di integrazione, puoi usare i modelli di app per la logica per decodificare e codificare i messaggi RosettaNet.To accelerate development and recommend integration patterns, you can use logic app app templates for decoding and encoding RosettaNet messages. Quando si crea un'app per la logica, è possibile effettuare una selezione dalla raccolta di modelli in Progettazione app per la logica. È inoltre possibile trovare questi modelli nel [repository GitHub per le app per la logica](https://github.com/Azure/logicapps)di Azure .

![Modelli RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ricevere o decodificare messaggi RosettaNet

1. [Creare un'app per la logica vuota.](quickstart-create-first-logic-app-workflow.md)

1. [Collegare l'account](logic-apps-enterprise-integration-create-integration-account.md#link-account) di integrazione all'app per la logica.

1. Prima di poter aggiungere un'azione per decodificare il messaggio RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **Nuovo passaggio**.

   ![Aggiungi trigger richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca, immettere "rosettanet" e selezionare questa azione: **RosettaNet Decode**

   ![Trova e seleziona l'azione "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Fornire i dettagli dell'azioneProvide action details](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Messaggio** | Sì | Il messaggio RosettaNet da decodificare  |
   | **Intestazioni** | Sì | Le intestazioni HTTP che forniscono i valori per la versione, ovvero la versione RNIF, e il tipo di risposta, che indica il tipo di comunicazione tra i partner e possono essere sincrone o asincrone |
   | **Ruolo** | Sì | Il ruolo del partner host nel PIP |
   ||||

   Dall'azione Decodifica RosettaNet, l'output, insieme ad altre proprietà, include **il segnale in uscita**, che è possibile scegliere di codificare e restituire al partner o di eseguire qualsiasi altra azione su tale output.

## <a name="send-or-encode-rosettanet-messages"></a>Inviare o codificare i messaggi RosettaNet

1. [Creare un'app per la logica vuota.](quickstart-create-first-logic-app-workflow.md)

1. [Collegare l'account](logic-apps-enterprise-integration-create-integration-account.md#link-account) di integrazione all'app per la logica.

1. Prima di poter aggiungere un'azione per codificare il messaggio RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **Nuovo passaggio**.

   ![Aggiungi trigger richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca, immettere "rosettanet" e selezionare questa azione: **RosettaNet Encode**

   ![Trova e seleziona l'azione "Codifica di RosettaNet"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Fornire i dettagli dell'azioneProvide action details](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Messaggio** | Sì | Il messaggio RosettaNet da codificare  |
   | **Partner ospitante** | Sì | Il nome del partner host |
   | **Partner ospite** | Sì | Il nome del partner ospite |
   | **Codice PIP** | Sì | Il codice PIP |
   | **Versione PIP** | Sì | La versione PIP |  
   | **Identità dell'istanza PIP** | Sì | Identificatore univoco per questo messaggio PIP |  
   | **Tipo di messaggio** | Sì | Il tipo di messaggio da codificare |  
   | **Ruolo** | Sì | Il ruolo del partner ospitante |
   ||||

   Il messaggio codificato è ora pronto per l'invio al partner.

1. Per inviare il messaggio codificato, in questo esempio viene utilizzata l'azione **HTTP,** che viene rinominata "HTTP - Invia messaggio codificato a partner".

   ![Azione HTTP per l'invio del messaggio RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Per gli standard RosettaNet, le transazioni commerciali sono considerate complete solo quando tutti i passaggi definiti dal PIP sono completi.

1. Dopo che l'host invia il messaggio codificato al partner, l'host attende il segnale e il riconoscimento. Per eseguire questa attività, aggiungere l'azione di **attesa RosettaNet per la risposta.**

   ![Aggiungere l'azione "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   La durata da utilizzare per l'attesa e il numero di tentativi si basano sulla configurazione PIP nell'account di integrazione. Se la risposta non viene ricevuta, questa azione genera una notifica di errore. Per gestire i tentativi, inserire sempre le azioni Codifica e **Attendi risposta** in un ciclo **Until.To** handle retries, always put the **Encode** and Wait for response actions in an Until loop.

   ![Fino al loop con le azioni di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come convalidare, trasformare e altre operazioni messaggi con [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
