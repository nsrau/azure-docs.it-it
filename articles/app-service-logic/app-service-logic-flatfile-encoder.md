---
title: Utilizzo di BizTalk Flat File Encoder in un app per la logica | Microsoft Docs
description: Connettore o app per le API BizTalk Flat File Encoder
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
# BizTalk Flat File Encoder
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Usare il connettore BizTalk Flat File Encoder per l'interoperabilità tra i dati dei file flat (ad esempio file Excel o con estensione csv) e i dati XML. Può convertire un'istanza specifica di file flat in XML e viceversa.

## Uso di BizTalk Flat File Encoder
Per usare BizTalk Flat File Encoder, è prima di tutto necessario creare un'istanza dell'app per le API BizTalk Flat File Encoder. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk Flat File Encoder da Azure Marketplace. Di seguito sono riportati i passaggi per creare un'app per le API BizTalk Flat File Encoder da Azure Marketplace:

1. Accedere al portale di Azure (http://portal.azure.com)
2. Selezionare Nuovo > Marketplace > Tutto
3. Cercare "BizTalk Flat File Encoder" nella casella di ricerca
4. Selezionare BizTalk Flat File Encoder nell'elenco dei risultati
5. Selezionare "Crea", quindi specificare un nome e gli altri dettagli necessari
6. Selezionare "Crea". Si verrà reindirizzati alla pagina iniziale in cui è possibile visualizzare lo stato di avanzamento della creazione. Questa operazione può richiedere alcuni minuti. Al termine, si riceverà una notifica.

### Configurare BizTalk Flat File Encoder
Dopo aver creato l'app per le API, è possibile avviarla direttamente dalla pagina iniziale del portale di Azure o dall'area di progettazione durante la creazione di un'app per la logica.

Per avviarla dalla pagina iniziale di Azure, è possibile cercarla digitandone il nome assegnato a BizTalk Flat File Encoder al momento della creazione. A questo scopo:

1. Immettere il nome di BizTalk Flat File Encoder nella casella di ricerca nel portale di Azure e avviare la ricerca
2. Successivamente, selezionare BizTalk Flat File Encoder dall'elenco. Viene aperto il pannello dell'app per le API in cui è possibile configurare l'app per le API BizTalk Flat File Encoder. Per avviare la configurazione, è necessario aggiungere uno schema:
3. Selezionare il componente "Schemi" ![Parte relativa agli schemi di BizTalk Flat File Encoder][2]
4. Nel pannello Schemi visualizzato selezionare "Aggiungi nuovo" ![Elenco di azioni di BizTalk Flat File Encoder][7]
5. Selezionare una delle tre opzioni per fornire lo schema. Le opzioni sono CARICA NUOVO SCHEMA, GENERA DA JSON e GENERA DA FILE FLAT ![Elenco di azioni di BizTalk Flat File Encoder][8]
6. Seguire la procedura per fornire lo schema, in base alla selezione nel passaggio precedente. Si noterà che lo schema è stato caricato: ![Elenco di azioni di BizTalk Flat File Encoder][9]

### Uso di BizTalk Flat File Encoder nell'area di progettazione
Dopo aver configurato Biztalk Flat File Encoder, è possibile usarlo in un'app per la logica. Per iniziare, creare una nuova app per la logica o avviarne una creata in precedenza e attenersi alla procedura seguente:

1. Nella scheda 'Start logic' fare clic su 'Run this logic manually'.
2. Selezionare l'app per le API BizTalk Flat File Encoder creata in precedenza nella raccolta (è possibile trovare il connettore BizTalk Flat File Encoder creato nell'elenco delle app per le API a destra della schermata.).
3. Selezionare la freccia destra nera. Vengono presentate le due operazioni disponibili (da file flat a XML e da XML a file flat): ![Elenco di azioni di BizTalk Flat File Encoder][1] ![Elenco di azioni di BizTalk Flat File Encoder][4]

Seguire i passaggi seguenti in base all'operazione selezionata

#### Da file flat a XML
![Elenco di azioni di BizTalk Flat File Encoder][5]

| Parametro | Tipo | Descrizione del parametro |
| --- | --- | --- |
| Flat File |stringa |Contenuto del file flat di input |
| Schema Name |stringa |Nome dello schema che rappresenta il file flat di input |
| Root Name |stringa |Nome del nodo radice dello schema di file flat |

L'azione restituisce l'output sotto forma di stringa - XML di output. XML di output contiene la rappresentazione XML del contenuto del file flat di input

#### Da XML a file flat
![Elenco di azioni di BizTalk Flat File Encoder][6]

| Parametro | Tipo | Descrizione del parametro |
| --- | --- | --- |
| Input Xml |stringa |Contenuto XML di input |

L'azione restituisce l'output sotto forma di stringa - File flat. XML di output contiene la rappresentazione file flat del contenuto XML di input

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG



<!---HONumber=AcomDC_0803_2016-->