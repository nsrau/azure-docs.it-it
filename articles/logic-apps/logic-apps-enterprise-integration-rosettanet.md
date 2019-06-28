---
title: Messaggi di RosettaNet per l'integrazione aziendale B2B - App per la logica di Azure
description: Lo scambio di messaggi di RosettaNet nelle App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332601"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Lo scambio di messaggi di RosettaNet per l'integrazione aziendale B2B nelle App per la logica di Azure 

[RosettaNet](https://resources.gs1us.org) è consorzio no profit che ha stabilito i processi standard per la condivisione delle informazioni aziendali. Questi standard usati comunemente per i processi di supply chain e sono diffusi in settori semiconductor electronics e logistica. Il consorzio di RosettaNet crea e gestisce Partner Interface Process (PIP), che forniscono definizioni di processo di business comuni per tutti gli scambi di messaggio di RosettaNet. RosettaNet basato su XML e definisce le linee guida messaggio, le interfacce per i processi aziendali e Framework di implementazione per la comunicazione tra le aziende.

Nelle [Azure Logic Apps](../logic-apps/logic-apps-overview.md), il connettore di RosettaNet consente di creare soluzioni di integrazione che supportano gli standard RosettaNet. Il connettore si basa su RosettaNet Implementation Framework (RNIF) versione 2.0.01. RNIF è un framework di applicazioni di rete aperta che consente ai partner di business per l'esecuzione in modalità collaborativa PIP di RosettaNet. Questo framework definisce la struttura del messaggio, la necessità di riconoscimenti, la codifica MIME Multipurpose Internet Mail Extensions () e la firma digitale.

In particolare, il connettore fornisce queste funzionalità:

* Codificare o ricevere messaggi di RosettaNet.
* Decodificare o inviare messaggi di RosettaNet.
* Attendere la risposta e la generazione della notifica di errore.

Per queste funzionalità, il connettore supporta PIP tutti definiti da RNIF 2.0.01. Comunicazione con il partner può essere sincrono o asincrono.

## <a name="rosettanet-concepts"></a>Concetti di RosettaNet

Ecco alcuni concetti e termini che sono univoche per la specifica di RosettaNet e sono importanti durante la creazione di integrazioni basato su RosettaNet:

* **PIP**

  L'organizzazione RosettaNet crea e gestisce Partner Interface Process (PIP), che forniscono definizioni di processo di business comuni per tutti gli scambi di messaggio di RosettaNet. Ogni specifica PIP fornisce un file document type definition (DTD) e un documento delle linee guida di messaggio. Il file DTD definisce la struttura del messaggio: contenuto del servizio. Il documento linee guida di messaggio, che è un file HTML leggibile dall'utente, specifica i vincoli a livello di elemento. Insieme, questi file forniscono una definizione completa del processo di business.

   PIP vengono classificati in base una funzione di business di alto livello, o cluster e subfunction un segmento. Ad esempio, "3A4" è l'indirizzo PIP per ordine di acquisto, mentre "3" è la funzione di gestione degli ordini e "3A" è il subfunction offerta e registrazione degli ordini. Per altre informazioni, vedere la [RosettaNet sito](https://resources.gs1us.org).

* **Azione**

  Parte di un indirizzo PIP, i messaggi di azione sono i messaggi di business che vengono scambiati tra i partner.

* **Signal**

   Parte di un indirizzo PIP, i messaggi dei segnali sono i riconoscimenti che vengono inviati in risposta ai messaggi di azione.

* **Azione singola e doppia azione**

  Per un PIP di singola azione, l'unica risposta è un messaggio di segnale di riconoscimento. Per un indirizzo PIP a doppia azione, l'iniziatore riceve un messaggio di risposta e risponde con un riconoscimento oltre al flusso di messaggio ad azione singola.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) per l'archiviazione del contratto e altri artefatti B2B. Questo account di integrazione deve essere associato alla sottoscrizione di Azure.

* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) che vengono definiti nell'account di integrazione e configurati con il qualificatore "DUNS" sotto **identità di Business**

* Una configurazione del processo PIP, che è necessaria per inviare o ricevere messaggi di RosettaNet, nell'account di integrazione. La configurazione del processo archivia tutte le caratteristiche di configurazione di PIP. È quindi possibile fare riferimento a questa configurazione quando si crea un contratto con il partner. Per creare una configurazione del processo PIP nell'account di integrazione, vedere [configurazione del processo PIP aggiungere](#add-pip).

* Facoltativo [certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md) per la crittografia, decrittografia o firmare i messaggi caricati nell'account di integrazione. I certificati sono necessari solo se si usa la firma o crittografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Aggiungere la configurazione del processo PIP

Per aggiungere una configurazione del processo PIP all'account di integrazione, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com), trovare e aprire l'account di integrazione.

1. Nel **Overview** riquadro, selezionare la **PIP di RosettaNet** riquadro.

   ![Scegliere il riquadro di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Sotto **PIP di RosettaNet**, scegliere **Add**. Fornire i dettagli PIP.

   ![Aggiungere i dettagli di PIP di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Yes | Il nome del PIP |
   | **Codice PIP** | Yes | Il codice di tre cifre PIP. Per altre informazioni, vedere [PIP di RosettaNet](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versione PIP** | Yes | Il numero di versione PIP, che è disponibile in base al codice PIP selezionato |
   ||||

   Per altre informazioni su queste proprietà PIP, visitare il [sito Web di RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Al termine, scegliere **OK**, che consente di creare la configurazione di PIP.

1. Per visualizzare o modificare la configurazione del processo, selezionare il PIP e scegliere **modifica come JSON**.

   Tutte le impostazioni provengono dalle specifiche del processo PIP di configurazione del processo. App per la logica consente di popolare la maggior parte delle impostazioni con i valori predefiniti che sono i valori più comuni per queste proprietà.

   ![Modificare la configurazione di PIP di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Verificare che le impostazioni corrispondono ai valori nella specifica PIP appropriato e soddisfano le esigenze aziendali. Se necessario, aggiornare i valori in JSON e salvare le modifiche.

## <a name="create-rosettanet-agreement"></a>Creare il contratto di RosettaNet

1. Nel [portale di Azure](https://portal.azure.com) trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel **Overview** riquadro, selezionare la **contratti** riquadro.

   ![Scegliere il che riquadro contratti](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. In **Contratti** scegliere **Aggiungi**. Fornire i dettagli dell'accordo.

   ![Aggiungi dettagli contratto](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Nome** | Yes | Il nome dell'accordo |
   | **Tipo di contratto** | Yes | Selezionare **RosettaNet**. |
   | **Partner host** | Yes | Un contratto richiede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
   | **Identità host** | Yes | Un identificatore per il partner host |
   | **Partner guest** | Yes | Un contratto richiede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
   | **Identità guest** | Yes | Un identificatore per il partner guest |
   | **Impostazioni di ricezione** | Variabile | Queste proprietà si applicano a tutti i messaggi ricevuti dal partner host |
   | **Impostazioni di invio** | Variabile | Queste proprietà si applicano a tutti i messaggi inviati dal partner host |  
   | **Riferimenti a PIP di RosettaNet** | Yes | I riferimenti PIP per l'accordo. Tutti i messaggi di RosettaNet richiedono configurazioni PIP. |
   ||||

1. Per configurare il contratto per la ricezione di messaggi in ingresso dal partner guest, selezionare **impostazioni di ricezione**.

   ![Impostazioni di ricezione](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Per abilitare la firma o crittografia per i messaggi in ingresso, in **messaggi**, selezionare **messaggio deve essere firmato** oppure **messaggio deve essere crittografato** rispettivamente.

      | Proprietà | Obbligatorio | Descrizione |
      |----------|----------|-------------|
      | **Il messaggio deve essere firmato** | No | Firmare i messaggi in ingresso con il certificato selezionato. |
      | **Certificate** | Sì, se la firma è abilitata | Il certificato da usare per la firma |
      | **Abilita crittografia messaggio** | No | Crittografare i messaggi in ingresso con il certificato selezionato. |
      | **Certificate** | Sì, se è abilitata la crittografia | Il certificato da usare per la crittografia |
      ||||

   1. In ogni selezione, selezionare la rispettiva [certificato](./logic-apps-enterprise-integration-certificates.md), che in precedenza aggiunto all'account di integrazione, da usare per la firma o crittografia.

1. Per configurare l'accordo per l'invio di messaggi al partner guest, selezionare **impostazioni di invio**.

   ![Impostazioni di invio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Per abilitare la firma o crittografia per i messaggi in uscita, in **messaggi**, selezionare **Abilita firma del messaggio** oppure **Abilita crittografia messaggio** rispettivamente. In ogni selezione, selezionare l'algoritmo corrispondente e [certificato](./logic-apps-enterprise-integration-certificates.md), che in precedenza aggiunto all'account di integrazione, da usare per la firma o crittografia.

      | Proprietà | Obbligatorio | Descrizione |
      |----------|----------|-------------|
      | **Abilita firma del messaggio** | No | Firmare i messaggi in uscita con l'algoritmo di firma selezionato e il certificato. |
      | **Algoritmo di firma** | Sì, se la firma è abilitata | L'algoritmo di firma da usare, basato sul certificato selezionato |
      | **Certificate** | Sì, se la firma è abilitata | Il certificato da usare per la firma |
      | **Abilita crittografia messaggio** | No | La crittografia in uscita con l'algoritmo di crittografia selezionato e il certificato. |
      | **Algoritmo di crittografia** | Sì, se è abilitata la crittografia | L'algoritmo di crittografia da usare, basato sul certificato selezionato |
      | **Certificate** | Sì, se è abilitata la crittografia | Il certificato da usare per la crittografia |
      ||||

   1. Sotto **endpoint**, specificare gli URL richiesti da usare per inviare i messaggi di azione e i riconoscimenti.

      | Proprietà | Obbligatorio | Descrizione |
      |----------|----------|-------------|
      | **URL di azione** |  Yes | L'URL da utilizzare per l'invio di messaggi di azione. L'URL è un campo obbligatorio per i messaggi sincroni e asincroni. |
      | **URL di riconoscimento** | Yes | L'URL da utilizzare per l'invio di messaggi di riconoscimento. L'URL è un campo obbligatorio per i messaggi asincroni. |
      ||||

1. Per configurare l'accordo con i riferimenti di PIP di RosettaNet per i partner, selezionare **fa riferimento a PIP di RosettaNet**. Sotto **PIP nome**, selezionare il nome per i PIP creati in precedenza.

   ![Riferimenti PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   La selezione popola le proprietà restanti, che si basano su PIP impostata nell'account di integrazione. Se necessario, è possibile modificare il **ruolo PIP**.

   ![PIP selezionato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Dopo aver completato questi passaggi, si è pronti per inviare o ricevere messaggi di RosettaNet.

## <a name="rosettanet-templates"></a>Modelli di RosettaNet

Per accelerare lo sviluppo e consiglia di modelli di integrazione, è possibile usare modelli di app per la logica per la decodifica e codifica di messaggi di RosettaNet. Quando si crea un'app per la logica, è possibile selezionare dalla raccolta di modelli in Progettazione App per la logica. È anche possibile trovare questi modelli nel [repository GitHub per le App per la logica di Azure](https://github.com/Azure/logicapps).

![Modelli di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Ricezione o decodificare i messaggi di RosettaNet

1. [Creare un'app per la logica vuota](quickstart-create-first-logic-app-workflow.md).

1. [Collegare l'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-account) all'App per la logica.

1. Prima di poter aggiungere un'azione per decodificare il messaggio di RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **nuovo passaggio**.

   ![Aggiungere trigger di richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca immettere "rosettanet" e selezionare l'azione: **Decodifica di RosettaNet**

   ![Trovare e selezionare l'azione "RosettaNet decodificare"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Fornire informazioni dettagliate sulle azioni](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Messaggio** | Yes | Per decodificare il messaggio di RosettaNet  |
   | **Intestazioni** | Yes | Le intestazioni HTTP che forniscono i valori per la versione, vale a dire la versione RNIF, e il tipo di risposta, che indica il tipo di comunicazione tra i partner e possa essere sincrone o asincrone |
   | **Ruolo** | Yes | Il ruolo del partner host nel processo PIP |
   ||||

   Dall'azione di decodifica di RosettaNet, l'output, insieme ad altre proprietà, include **segnali in uscita**, che è possibile scegliere di codificare e tornare indietro al partner o eseguire altre azioni su tale output.

## <a name="send-or-encode-rosettanet-messages"></a>Inviare o codificare i messaggi di RosettaNet

1. [Creare un'app per la logica vuota](quickstart-create-first-logic-app-workflow.md).

1. [Collegare l'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-account) all'App per la logica.

1. Prima di poter aggiungere un'azione per codificare il messaggio di RosettaNet, è necessario aggiungere un trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.

1. Dopo aver aggiunto il trigger, scegliere **nuovo passaggio**.

   ![Aggiungere trigger di richiesta](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Nella casella di ricerca immettere "rosettanet" e selezionare l'azione: **Codifica di RosettaNet**

   ![Trovare e selezionare l'azione "Codifica di RosettaNet"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Fornire le informazioni per le proprietà dell'azione:

   ![Fornire informazioni dettagliate sulle azioni](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Messaggio** | Yes | Per codificare il messaggio di RosettaNet  |
   | **Partner host** | Yes | Il nome del partner host |
   | **Partner guest** | Yes | Il nome del partner guest |
   | **Codice PIP** | Yes | Il codice PIP |
   | **Versione PIP** | Yes | La versione PIP |  
   | **Identità dell'istanza PIP** | Yes | L'identificatore univoco per questo messaggio PIP |  
   | **tipo di messaggio** | Yes | Il tipo del messaggio da codificare |  
   | **Ruolo** | Yes | Il ruolo del partner host |
   ||||

   Il messaggio codificato è ora pronto per l'invio al partner.

1. Per inviare il messaggio codificato, questo esempio Usa la **HTTP** azione, che viene rinominato "HTTP - messaggi con codificata di trasmissione al partner".

   ![Azione HTTP per l'invio messaggio di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Per gli standard di RosettaNet, le transazioni aziendali vengono considerate completate solo dopo aver completato tutti i passaggi definiti da PIP.

1. Dopo che l'host ha inviato il messaggio codificato al partner, l'host attende che il segnale e il riconoscimento. A questo scopo, aggiungere il **RosettaNet attesa di risposta** azione.

   ![Aggiungere un'azione "RosettaNet attesa di risposta"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   La durata da utilizzare per il numero di tentativi e in attesa sono basati sulla configurazione di PIP nell'account di integrazione. Se non viene ricevuta la risposta, questa azione genera una notifica di errore. Per gestire i tentativi, inserire sempre il **Encode** e **attesa di risposta** azioni in un **fino a quando non** ciclo.

   ![Fino al ciclo con azioni di RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come convalidare, trasformare e altre operazioni messaggi con [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
