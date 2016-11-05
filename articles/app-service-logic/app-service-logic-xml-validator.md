---
title: Uso di BizTalk XML Validator nelle app per la logica in Servizio app di Azure | Microsoft Docs
description: Convalidare schemi usando BizTalk XML Validator in un'app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# BizTalk XML Validator
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Utilizzare il connettore BizTalk XML Validator nell'app per convalidare i dati XML in base a schemi XML predefiniti. È possibile usare gli schemi esistenti o generare schemi basati su un'istanza di file flat, su un'istanza JSON o su connettori esistenti.

## Uso di BizTalk XML Validator
Per usare BizTalk XML Validator, prima di tutto creare un'istanza dell'app per le API BizTalk XML Validator. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk XML Validator da Azure Marketplace.

### Configurare BizTalk XML Validator
BizTalk XML Validator accetta schemi come parte della configurazione. È possibile avviare il pannello di configurazione dell'app per le API avviando l'app per le API direttamente dal portale di Azure oppure facendo doppio clic sull'app per le API nell'area di progettazione.

![Configurazione di BizTalk XML Validator][1]

Nel pannello dell'app per le API è possibile configurare gli schemi selezionando l'opzione *Schemi*.

![Parte Schemi di BizTalk XML Validator][2]

È possibile caricare uno schema dal disco o generarne uno da un'istanza di file flat o un'istanza JSON.

![Schemi di BizTalk XML Validator][3]

### Uso di BizTalk Flat File Encoder nell'area di progettazione
Dopo la configurazione, è possibile selezionare *->* e scegliere un'azione da un elenco di azioni.

![Elenco di azioni di BizTalk XML Validator][4]

#### Validate Xml
L'azione di convalida Xml consente di convalidare uno specifico input XML in base a schemi preconfigurati.

![XML Validate di BizTalk XML Validator][5]

| Parametro | Tipo | Descrizione del parametro |
| --- | --- | --- |
| Input Xml |stringa |XML di input da convalidare. |

L'azione restituisce l'output sotto forma di oggetto. L'output contiene il modello che rappresenta la risposta ottenuta da XML Validator, costituita dal risultato, il nome dello schema, il nodo radice e la descrizione dell'errore.

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0803_2016-->