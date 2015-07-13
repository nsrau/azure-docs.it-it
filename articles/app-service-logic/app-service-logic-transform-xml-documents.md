<properties 
	pageTitle="Trasformare i documenti XML" 
	description="Informazioni su come trasformare i documenti XML da uno schema a un altro." 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2015"
	ms.author="anuragdalmia"/>

#Trasformare i documenti XML


## Panoramica dell'app per le API BizTalk Transform
In parole semplici, l'app per le API BizTalk Transform converte i dati da un formato a un altro. Ad esempio, è possibile ricavare gli indirizzi di spedizione e fatturazione da un ordine di acquisto e inserirli in un documento di fatturazione. Se invece si è ricevuto un messaggio contenente la data corrente nel formato *YearMonthDay*, può essere opportuno riformattarla nel formato *MonthDayYear*.

A tale scopo, è possibile usare l'app per le API Transform nel servizio app di Microsoft Azure. Un'app per le API Transform o Map consiste in uno schema XML di origine (input) e in uno schema XML di destinazione (output). È possibile usare diverse funzioni predefinite per contribuire a gestire o controllare i dati, incluse la modifica di stringhe, le operazioni Conditional Assignment, le espressioni aritmetiche, i formattatori di data/ora e persino i costrutti a ciclo continuo.

Le mappe vengono create in Visual Studio usando i [SDK di Servizi BizTalk di Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087). Al termine della creazione e del test della mappa, caricare il relativo file con estensione trfm nell'app per le API BizTalk Transform.

Le altre funzionalità includono:

- La trasformazione creata in una mappa può essere semplice, come ad esempio la copia di un nome e di un indirizzo da un documento a un altro. In alternativa, è possibile creare trasformazioni più complesse usando operazioni di mapping predefinite.
- Sono già disponibili più operazioni o funzioni di mapping, incluse stringhe, funzioni data/ora e così via.
- È possibile eseguire una copia diretta dei dati tra gli schemi. In BizTalk Mapper, questa operazione è semplice quando tracciare una linea che colleghi gli elementi nello schema di origine con le relative controparti nello schema di destinazione.
- Durante la creazione di una mappa viene visualizzata la relativa rappresentazione grafica, incluse tutte le relazioni e i collegamenti creati dall'utente.
- Usare la funzione **Mappa di test** per aggiungere un messaggio XML di esempio. Con un semplice clic, è possibile testare la mappa creata e visualizzare l'output generato.
- Caricare le mappe dei Servizi BizTalk di Azure esistenti (trfm) e sfruttare tutti i vantaggi dell'app per le API Transform.
- Include il supporto per il formato XML.


## Creare una nuova app per le API BizTalk Transform

1.	Accedere al portale di Azure e passare alla Schermata iniziale (la home page).

2.	Selezionare **Nuovo** > **Web e dispositivi mobili** > **Azure Marketplace** > **App per le API**:

	   ![][1]
 
	In alternativa, selezionare **Marketplace** nella schermata iniziale e selezionare **App per le API** dall'elenco disponibile:

	   ![][2]
 
3.	Cercare BizTalk Transform digitando **Transform** e selezionare **Servizio BizTalk Transform**:

	   ![][4]
 
4.	Nel pannello **Servizio BizTalk Transform** selezionare **Crea**:

       ![][5]
 
5.	Nel pannello **Nuova app per le API** immettere le informazioni seguenti e selezionare **Crea**:

	- Nome: assegnare un nome all'app per le API Transform 
	- Piano di servizio app: selezionare o creare un nuovo piano di servizio app 
	- Piano tariffario: scegliere il piano tariffario desiderato per l'app 
	- Gruppo di risorse: selezionare o creare il gruppo di risorse desiderato per questa app 
	- Località: scegliere la località geografica in cui si vuole distribuire il connettore
	
	   ![][6]

6.	Selezionare **Crea**. Entro pochi minuti verranno create le app per le API BizTalk Transform.


## Scaricare schemi dalle app per le API per i connettori
È possibile scaricare gli schemi XML per i connettori come SQL, SAP e SharePoint dalla pagina di riepilogo dell'app per le API. Ad esempio, se si vogliono scaricare gli schemi XML per una specifica app per le API per i connettori SAP, cercare l'app per le API e aprire la pagina di riepilogo. Selezionare **Download degli schemi**: un file zip con tutti gli schemi corrispondenti alle azioni SAP verrà scaricato sul computer. È possibile usare gli schemi per creare una mappa (.trfm) in Visual Studio.

   ![][14]


## Creare e aggiungere la mappa
Le app per le API Transform o Map vengono create in Visual Studio usando il download gratuito dell'[SDK Servizi BizTalk di Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=39087).

Per assistenza nella creazione di una mappa, vedere [Creare una mappa in Visual Studio](http://aka.ms/createamapinvs). Dopo aver creato la mappa pronta per la produzione, è possibile aggiungere il file trfm all'app per le API BizTalk Transform creata nel portale di gestione di Azure.

Se la mappa cambia o subisce modifiche dopo il caricamento, è possibile caricare la mappa aggiornata che sostituirà quella esistente nell'app per le API Transform.

1.	Selezionare **Sfoglia** nel portale di gestione di Azure (a sinistra dello schermo), quindi **App per le API**. Se l'opzione **App per le API** non è visualizzata, selezionare **Tutto**, quindi **App per le API** dall'elenco disponibile:

	![][7]

2.	Viene visualizzato l'elenco di tutte le **App per le API** creare nella sottoscrizione di Azure:

	![][8]

3.	Selezionare l'app per le API BizTalk Transform creata nella sezione precedente.

4.	Viene visualizzato il pannello di configurazione per l'app delle API. Nella sezione Componenti è visibile l'opzione **Mapping**:

	![][9]

5.	Selezionare **Mapping** per aprire il nuovo pannello con l'elenco di mappe.

6.	Selezionare l'icona **Aggiungi mapping** in alto per aprire il pannello **Aggiungi mapping**:

	![][10]

7.	Selezionare l'icona File e cercare file di mapping (.trfm) nel computer locale.

8.  Selezionare **OK** per creare una nuova mappa, che verrà visualizzata nel relativo elenco.


## Usare un'app per le API BizTalk Transform in un'app per la logica
Una volta che la mappa è stata creata e verificata, è pronta per l'uso. Gli utenti possono creare una nuova app per la logica (Nuovo -> **App per la logica**).

1. All'interno dell'app per la logica, BizTalk Transform è disponibile nella raccolta a destra. Selezionare **Servizio BizTalk Transform** dalla raccolta. L'API Transform viene aggiunta al flusso:

	![][11]

2. Selezionare l'azione **Transform**. Vengono visualizzati i parametri di input:

	![][12]

3. Immettere i parametri seguenti per completare la configurazione dell'azione **Transform**:
		 
	- XML di input
		- Immettere il contenuto XML valido che sia conforme allo schema di origine di una mappa nell'app per le API Transform. Può trattarsi di un output di un'azione precedente nell'app per la logica, come ad esempio ‘Call RFC – SAP’ o ‘Insert Into Table – SQL’.
		
	- Nome mappa (facoltativo)
		- Immettere un nome valido per la mappa già caricata nell'app per le API Transform. Se non si inserisce alcuna mappa, questa verrà automaticamente selezionata in base allo schema di origine a cui l'XML di input è conforme.

	![][13]

4. L'output dell'azione 'Output XML' può essere usato nelle azioni successive nelle app per la logica.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=62-->