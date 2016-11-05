---
title: Creazione di un accordo tra partner commerciali in Servizio app di Azure | Microsoft Docs
description: Creare accordi tra partner commerciali
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/23/2016
ms.author: rajram

---
# Creazione di un accordo tra partner commerciali
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

I partner commerciali sono le entità coinvolte nelle comunicazioni B2B. Quando due partner stabiliscono una relazione, questa è definita un *Contratto*. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto. I diversi protocolli e trasporti B2B supportati da Azure App Service includono:

* AS2 (Applicability Statement 2)
* EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
* X12 (ASC X12)

### App per le API BizTalk che supportano scenari B2B
Le app per le API seguenti consentono di usare queste funzionalità, caratterizzate da un'esperienza avanzata e intuitiva, nel portale di Azure:

## BizTalk Trading Partner Management (TPM)
* Creazione e gestione di partner, profili e identità
* Archiviazione e gestione di schemi EDI
* Archiviazione e gestione di certificati (usata nel protocollo AS2)
* Creazione e gestione di contratti AS2
* Creazione e gestione di contratti EDIFACT (include l'invio in batch sul lato trasmissione)
* Creazione e gestione di contratti X12 (include l'invio in batch sul lato trasmissione)

![][1]

## AS2 Connector
* Esegue i contratti AS2 definiti nell'istanza dell'app per le API TPM correlata
* Presenta le informazioni di elaborazione/rilevamento AS2 per la risoluzione dei problemi

## BizTalk EDIFACT
* Esegue i contratti EDIFACT definiti nell'istanza dell'app per le API TPM correlata
* Presenta le informazioni di elaborazione/rilevamento EDIFACT per la risoluzione dei problemi
* Fornisce la gestione dello stato dei batch (avvio e arresto) definita nei contratti EDIFACT nell'istanza dell'app per le API TPM correlata

## BizTalk X12
* Esegue i contratti X12 definiti nell'istanza dell'app per le API TPM correlata
* Presenta le informazioni di elaborazione/rilevamento X12 per la risoluzione dei problemi
* Fornisce la gestione dello stato dei batch (avvio e arresto) definita nei contratti X12 nell'istanza dell'app per le API TPM correlata

Come indicato in precedenza, per le app per le API AS2, X12 ed EDIFACT è necessaria un'app per le API TPM che funzioni come previsto.

## Introduzione
Per creare accordi tra partner commerciali:

1. Creare un'istanza del connettore **BizTalk Trading Partner Management**. Per il funzionamento dell'app per le API è necessario un database SQL vuoto. Prima di iniziare, assicurarsi che sia disponibile e pronto per l'uso un database vuoto.
2. Caricare gli schemi e i certificati richiesti dai contratti. Eseguire questa operazione passando all'istanza TPM creata e quindi alla sezione "Schemi" e/o "Certificati"
3. Passare all'istanza TPM creata e quindi alla sezione **Partner**
4. Creare i partner desiderati. Modificare anche i profili in base alle proprie esigenze e aggiungere le identità necessarie
5. Usare quindi la sezione **Contratti** per creare i contratti. Quando si crea un contratto, è necessario selezionare il protocollo che verrà utilizzato. Le opzioni di configurazione rimanenti sono basate sul protocollo selezionato.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png


<!---HONumber=AcomDC_0824_2016-->