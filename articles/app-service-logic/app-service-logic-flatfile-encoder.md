<properties 
   pageTitle="BizTalk Flat File Encoder" 
   description="BizTalk Flat File Encoder" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/01/2015"
   ms.author="rajram"/>

# BizTalk Flat File Encoder

Il connettore BizTalk Flat File Encode Decode semplifica l'interoperabilità dell'app tra i dati dei file flat (ad esempio di Excel, csv) e i dati XML. Può convertire un'istanza specifica di file flat in XML e viceversa.

##Uso di BizTalk Flat File Encoder
1. Per usare BizTalk Flat File Encoder, è prima di tutto necessario creare un'istanza dell'app per le API BizTalk Flat File Encoder. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk Flat File Encoder da Azure Marketplace.

###Configurare BizTalk Flat File Encoder
BizTalk Flat File Encoder accetta schemi come parte della configurazione. Gli utenti possono avviare il pannello di configurazione dell'app per le API avviando l'app per le API direttamente dal portale di Azure oppure facendo doppio clic sull'app per le API nell'area di progettazione.

![Configurazione di BizTalk Flat File Encoder][1]

Nel pannello dell'app per le API gli utenti possono configurare gli schemi facendo clic sulla parte *Schemi*.

![Parte relativa agli schemi di BizTalk Flat File Encoder][2]

Gli utenti possono caricare gli schemi dal disco o generarne uno da un'istanza di file flat o un'istanza JSON.

![Parte relativa agli schemi di BizTalk Flat File Encoder][3]


###Uso di BizTalk Flat File Encoder nell'area di progettazione
Dopo la configurazione, gli utenti possono fare clic su *->* e scegliere un'azione da un elenco di azioni.

![Elenco di azioni di BizTalk Flat File Encoder][4]

####Da file flat a XML

![Elenco di azioni di BizTalk Flat File Encoder][5]

Parametro|Tipo|Descrizione del parametro
---|---|---
File flat|stringa|Contenuto del file flat di input
Nome schema|stringa|Nome dello schema che rappresenta il file flat di input
Nome radice|stringa|Nome del nodo radice dello schema di file flat


L'azione restituisce l'output sotto forma di stringa - XML di output. XML di output contiene la rappresentazione XML del contenuto del file flat di input

####Da XML a file flat

![Elenco di azioni di BizTalk Flat File Encoder][6]

Parametro|Tipo|Descrizione del parametro
---|---|---
XML di input|stringa|Contenuto XML di input

L'azione restituisce l'output sotto forma di stringa - File flat. XML di output contiene la rappresentazione file flat del contenuto XML di input

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=August15_HO6-->