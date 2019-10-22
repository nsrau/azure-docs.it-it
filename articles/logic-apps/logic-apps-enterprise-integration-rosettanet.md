---
title: Messaggi di RosettaNet per l'integrazione B2B-app per la logica di Azure
description: Scambiare messaggi RosettaNet in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 570c7907f320b881e2db0bd45cdce311490f4f45
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680325"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Scambiare messaggi RosettaNet per l'integrazione aziendale B2B in app per la logica di Azure

[RosettaNet](https://resources.gs1us.org) è un consorzio non profit che ha stabilito processi standard per la condivisione di informazioni aziendali. Questi standard sono comunemente usati per i processi della supply chain e sono diffusi nei settori di semiconduttori, di elettronica e logistici. Il Consorzio RosettaNet crea e gestisce i PIP (Partner Interface Process), che forniscono definizioni dei processi aziendali comuni per tutti gli scambi di messaggi di RosettaNet. RosettaNet si basa su XML e definisce le linee guida per i messaggi, le interfacce per i processi aziendali e i Framework di implementazione per la comunicazione tra le aziende.

In [app](../logic-apps/logic-apps-overview.md)per la logica di Azure il connettore RosettaNet consente di creare soluzioni di integrazione che supportano gli standard RosettaNet. Il connettore è basato sul Framework di implementazione di RosettaNet (RNIF) versione 2.0.01. RNIF è un Framework di applicazioni di rete aperto che consente ai partner aziendali di eseguire in modo collaborativo RosettaNet PIPs. Questo Framework definisce la struttura del messaggio, la necessità di riconoscimenti, la codifica Multipurpose Internet Mail Extensions (MIME) e la firma digitale.

In particolare, il connettore fornisce le seguenti funzionalità:

* Codificare o ricevere messaggi RosettaNet.
* Decodificare o inviare messaggi RosettaNet.
* Attendere la risposta e la generazione della notifica di errore.

Per queste funzionalità, il connettore supporta tutti i PIP definiti da RNIF 2.0.01. La comunicazione con il partner può essere sincrona o asincrona.

## <a name="rosettanet-concepts"></a>Concetti di RosettaNet

Di seguito sono riportati alcuni concetti e termini che sono univoci per la specifica RosettaNet e sono importanti quando si compilano integrazioni basate su RosettaNet:

* **PIP**

  L'organizzazione RosettaNet crea e gestisce i PIP (Partner Interface Process), che forniscono definizioni comuni dei processi di business per tutti gli scambi di messaggi di RosettaNet. Ogni specifica PIP fornisce un file di Document Type Definition (DTD) e un documento di riferimento per il messaggio. Il file DTD definisce la struttura del messaggio del contenuto del servizio. Il documento relativo alla guida del messaggio, che è un file HTML leggibile, specifica i vincoli a livello di elemento. Insieme, questi file forniscono una definizione completa del processo di business.

   I PIP sono suddivisi in categorie in base a una funzione di business di alto livello, un cluster, una funzione o un segmento. Ad esempio, "3A4" è il PIP per l'ordine di acquisto, mentre "3" è la funzione di gestione degli ordini e "3A" è la sottofunzione & Order entry. Per ulteriori informazioni, vedere il [sito di RosettaNet](https://resources.gs1us.org).

* **Azione**

  Parte di un PIP, i messaggi di azione sono messaggi aziendali scambiati tra partner.

* **Segnale**

   Parte di un PIP, i messaggi di segnale sono riconoscimenti inviati in risposta ai messaggi di azione.

* **Azione singola e doppia azione**

  Per un PIP a azione singola, l'unica risposta è un messaggio di segnale di riconoscimento. Per un PIP a doppia azione, l'initiator riceve un messaggio di risposta e risponde con un riconoscimento oltre al flusso di messaggi a azione singola.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per archiviare il contratto e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) definiti nell'account di integrazione e configurati con il qualificatore "DUNS" in **identità di business**

* Una configurazione del processo PIP, necessaria per inviare o ricevere messaggi RosettaNet, nell'account di integrazione. La configurazione del processo archivia tutte le caratteristiche di configurazione PIP. È quindi possibile fare riferimento a questa configurazione quando si crea un contratto con il partner. Per creare una configurazione del processo PIP nell'account di integrazione, vedere [aggiungere la configurazione del processo PIP](#add-pip).

* [Certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md) facoltativi per la crittografia, la decrittografia o la firma dei messaggi caricati nell'account di integrazione. I certificati sono necessari solo se si usa la firma o la crittografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Aggiungere la configurazione del processo PIP

Per aggiungere una configurazione del processo PIP all'account di integrazione, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'account di integrazione.

1. Nel riquadro **Panoramica** selezionare il riquadro **RosettaNet PIP** .

   ![Scegliere il riquadro RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. In **ROSETTANET PIP**scegliere **Aggiungi**. Specificare i dettagli del PIP.

   ![Aggiungi dettagli PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Nome** | SÌ | Nome PIP |
   | **Codice PIP** | SÌ | Codice di tre cifre PIP. Per altre informazioni, vedere [RosettaNet PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versione PIP** | SÌ | Il numero di versione PIP, disponibile in base al codice PIP selezionato |
   ||||

   Per ulteriori informazioni su queste proprietà PIP, visitare il [sito Web RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Al termine, scegliere **OK**per creare la configurazione PIP.

1. Per visualizzare o modificare la configurazione del processo, selezionare il PIP e scegliere **modifica come JSON**.

   Tutte le impostazioni di configurazione dei processi provengono dalle specifiche del PIP. App per la logica popola la maggior parte delle impostazioni con i valori predefiniti che sono i valori usati più di solito per queste proprietà.

   ![Modificare la configurazione di RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Verificare che le impostazioni corrispondano ai valori nella specifica PIP appropriata e soddisfino le esigenze aziendali. Se necessario, aggiornare i valori in JSON e salvare le modifiche.

## <a name="create-rosettanet-agreement"></a>Crea contratto RosettaNet

1. Nel [portale di Azure](https://portal.azure.com) trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel riquadro **Panoramica** selezionare il riquadro **contratti** .

   ![Riquadro Scegli contratti](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. In **Contratti** scegliere **Aggiungi**. Fornire i dettagli del contratto.

   ![Aggiungere i dettagli del contratto](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Nome** | SÌ | Nome del contratto |
   | **Tipo di contratto** | SÌ | Selezionare **RosettaNet**. |
   | **Partner host** | SÌ | Un contratto richiede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
   | **Identità host** | SÌ | Un identificatore per il partner host |
   | **Partner Guest** | SÌ | Un contratto richiede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
   | **Identità Guest** | SÌ | Un identificatore per il partner guest |
   | **Impostazioni di ricezione** | Variabile | Queste proprietà si applicano a tutti i messaggi ricevuti dal partner host |
   | **Impostazioni di invio** | Variabile | Queste proprietà si applicano a tutti i messaggi inviati dal partner host |  
   | **Riferimenti PIP RosettaNet** | SÌ | Riferimenti PIP per l'accordo. Tutti i messaggi RosettaNet richiedono configurazioni PIP. |
   ||||

1. Per configurare il contratto per la ricezione di messaggi in arrivo dal partner Guest, selezionare **impostazioni di ricezione**.

   ![Impostazioni di ricezione](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Per abilitare la firma o la crittografia per i messaggi in arrivo, in **messaggi**selezionare il **messaggio deve essere firmato** o il **messaggio deve essere crittografato** rispettivamente.

      | Proprietà | Obbligatoria | Description |
      |----------|----------|-------------|
      | **Il messaggio deve essere firmato** | No | Firma i messaggi in ingresso con il certificato selezionato. |
      | **Certificate** | Sì, se la firma è abilitata | Certificato da usare per la firma |
      | **Abilitare la crittografia del messaggio** | No | Crittografare i messaggi in ingresso con il certificato selezionato. |
      | **Certificate** | Sì, se la crittografia è abilitata | Certificato da usare per la crittografia |
      ||||

   1. In ogni selezione selezionare il rispettivo [certificato](./logic-apps-enterprise-integration-certificates.md), che è stato precedentemente aggiunto all'account di integrazione, da usare per la firma o la crittografia.

1. Per configurare il contratto per l'invio di messaggi al Partner Guest, selezionare **Invia impostazioni**.

   ![Impostazioni di invio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Per abilitare la firma o la crittografia per i messaggi in uscita, in **messaggi**selezionare **Abilita firma messaggi** o **Abilita crittografia messaggi** rispettivamente. In ogni selezione selezionare il rispettivo algoritmo e [certificato](./logic-apps-enterprise-integration-certificates.md), che è stato precedentemente aggiunto all'account di integrazione, da usare per la firma o la crittografia.

      | Proprietà | Obbligatoria | Description |
      |----------|----------|-------------|
      | **Abilita firma messaggi** | No | Firma i messaggi in uscita con l'algoritmo di firma e il certificato selezionati. |
      | **Algoritmo di firma** | Sì, se la firma è abilitata | Algoritmo di firma da usare, in base al certificato selezionato |
      | **Certificate** | Sì, se la firma è abilitata | Certificato da usare per la firma |
      | **Abilitare la crittografia del messaggio** | No | Crittografare in uscita con l'algoritmo e il certificato di crittografia selezionati. |
      | **Algoritmo di crittografia** | Sì, se la crittografia è abilitata | Algoritmo di crittografia da usare, in base al certificato selezionato |
      | **Certificate** | Sì, se la crittografia è abilitata | Certificato da usare per la crittografia |
      ||||

   1. In **endpoint**specificare gli URL necessari da usare per l'invio di messaggi di azione e riconoscimenti.

      | Proprietà | Obbligatoria | Description |
      |----------|----------|-------------|
      | **URL azione** |  SÌ | URL da utilizzare per l'invio dei messaggi di azione. L'URL è un campo obbligatorio per i messaggi sincroni e asincroni. |
      | **URL di riconoscimento** | SÌ | URL da utilizzare per l'invio di messaggi di riconoscimento. L'URL è un campo obbligatorio per i messaggi asincroni. |
      ||||

1. Per configurare il contratto con i riferimenti PIP di RosettaNet per i partner, selezionare **ROSETTANET PIP References**. In **nome PIP**selezionare il nome del PIP creato in precedenza.

   ![Riferimenti PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   La selezione popola le proprietà rimanenti, che sono basate sul PIP configurato nell'account di integrazione. Se necessario, è possibile modificare il **ruolo PIP**.

   ![PIP selezionato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Dopo aver completato questi passaggi, si è pronti per inviare o ricevere messaggi RosettaNet.

## <a name="rosettanet-templates"></a>Modelli RosettaNet

Per accelerare lo sviluppo e consigliare modelli di integrazione, è possibile usare i modelli di app per la logica per la decodifica e la codifica dei messaggi RosettaNet. Quando si crea un'app per la logica, è possibile selezionare dalla raccolta di modelli in progettazione app per la logica. È anche possibile trovare questi modelli nel [repository GitHub per app per la logica di Azure](https://github.com/Azure/logicapps).

![Modelli RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ricevere o decodificare messaggi RosettaNet

1. [Creare un'app per la logica vuota](quickstart-create-first-logic-app-workflow.md).

1. [Collegare l'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-account) all'app per la logica.

1. Prima di poter aggiungere un'azione per decodificare il messaggio RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **nuovo passaggio**.

   ![Aggiungi trigger di richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca immettere "RosettaNet" e selezionare questa azione: **RosettaNet Decode**

   ![Trovare e selezionare l'azione "decodifica RosettaNet"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Specificare i dettagli dell'azione](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Messaggio** | SÌ | Messaggio RosettaNet da decodificare  |
   | **Intestazioni** | SÌ | Intestazioni HTTP che forniscono i valori per la versione, ovvero la versione RNIF, e il tipo di risposta, che indica il tipo di comunicazione tra i partner e può essere sincrono o asincrono |
   | **Ruolo** | SÌ | Ruolo del partner host nel PIP |
   ||||

   Dall'azione di decodifica RosettaNet, l'output, insieme ad altre proprietà, include il **segnale in uscita**, che è possibile scegliere di codificare e tornare al partner o eseguire qualsiasi altra azione sull'output.

## <a name="send-or-encode-rosettanet-messages"></a>Inviare o codificare messaggi RosettaNet

1. [Creare un'app per la logica vuota](quickstart-create-first-logic-app-workflow.md).

1. [Collegare l'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-account) all'app per la logica.

1. Prima di poter aggiungere un'azione per codificare il messaggio RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **nuovo passaggio**.

   ![Aggiungi trigger di richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca immettere "RosettaNet" e selezionare questa azione: **RosettaNet Encode**

   ![Trovare e selezionare l'azione "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Specificare i dettagli dell'azione](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Messaggio** | SÌ | Messaggio RosettaNet da codificare  |
   | **Partner host** | SÌ | Nome del partner host |
   | **Partner Guest** | SÌ | Nome del partner Guest |
   | **Codice PIP** | SÌ | Codice PIP |
   | **Versione PIP** | SÌ | Versione PIP |  
   | **Identità dell'istanza PIP** | SÌ | Identificatore univoco per questo messaggio PIP |  
   | **Tipo di messaggio** | SÌ | Tipo di messaggio da codificare. |  
   | **Ruolo** | SÌ | Ruolo del partner host |
   ||||

   Il messaggio codificato è ora pronto per l'invio al partner.

1. Per inviare il messaggio codificato, in questo esempio viene utilizzata l'azione **http** , che viene rinominata "http-Invia messaggio codificato al partner".

   ![Azione HTTP per l'invio del messaggio RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Per gli standard RosettaNet, le transazioni aziendali vengono considerate complete solo quando tutti i passaggi definiti dal PIP sono completi.

1. Dopo che l'host ha inviato il messaggio codificato al partner, l'host attende il segnale e il riconoscimento. Per eseguire questa operazione, aggiungere l'azione **RosettaNet Wait for response** .

   ![Aggiungere l'azione "RosettaNet Wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   La durata da usare per l'attesa e il numero di tentativi sono basati sulla configurazione PIP nell'account di integrazione. Se la risposta non viene ricevuta, questa azione genera una notifica di errore. Per gestire i tentativi, inserire sempre la **codifica** e attendere le azioni di **risposta** in un ciclo **until** .

   ![Ciclo until con azioni RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come convalidare, trasformare e altre operazioni messaggi con [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
