<properties
	pageTitle="Esercitazione: Creare un'API Web personalizzata usando un'app Web ASP.Net in PowerApps e nei flussi logici | Microsoft Azure"
	description="Esercitazione sulle app Web ASP.Net per la creazione di un'API personalizzata in PowerApps e nei flussi logici"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>

# Esercitazione: Creare un'API Web personalizzata e protetta di AAD per PowerApps e i flussi logici

Questa esercitazione illustra come creare un'API Web ASP.Net, ospitarla in app Web di Azure, abilitare l'autenticazione di AAD tramite l'autenticazione semplice, quindi registrare l'API Web in PowerApps e nei flussi logici.

## Elementi necessari per iniziare

* Una sottoscrizione di Azure.
* Un account PowerApps
* Visual Studio 2013 o versione successiva

## Passaggio 1: Creare un'API Web e distribuirla in Azure
1. Aprire Visual Studio e creare una nuova applicazione Web ASP.NET C#:
![](./media/powerapps-web-api-tutorial/newwebapp.png "Nuova app Web")

2. Nella schermata successiva selezionare il modello di API Web e selezionare **Nessuna autenticazione**:
![](./media/powerapps-web-api-tutorial/noauth.png "Nessuna autorizzazione")

	>[AZURE.IMPORTANT] Assicurarsi di impostare l'autenticazione su "Nessuna autenticazione".

3. Quando il progetto viene creato, è necessario creare l'API Web per le risorse. Per questa esercitazione non vengono esaminati gli aspetti specifici della creazione di un'API Web.

4. Generare quindi un file di Swagger per l'API Web. È possibile eseguire facilmente questa operazione aprendo la __Console di Gestione pacchetti__ e installando __Swashbuckle__:
![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Console di Swashbuckle")

5. Dopo l'installazione e l'abilitazione, esaminare i documenti seguenti su Swagger e sugli endpoint dell'interfaccia utente, rispettivamente: **<URL-della-radice>/swagger/docs/v1**

 	**<URL-della-radice>/swagger**

6. Quando l'API Web è pronta, pubblicarla in Azure. Per pubblicarla in Visual Studio, passare a **COMPILAZIONE** e selezionare **PUBBLICA**.

7. Estrarre il file JSON di Swagger passando a ***https://\<URL-appweb-azure>/swagger/docs/v1***.

	> [AZURE.IMPORTANT] Un documento Swagger con valore operationid duplicato non è valido. Se si usa il modello C# di esempio, il valore operation-id "Values\_Get" viene ripetuto due volte. Cambiare un'istanza in "Value\_Get".


È possibile scaricare il file Swagger usato in questa esercitazione [qui][6]. Assicurarsi di sostituire/rimuovere i commenti prima di usarlo. I commenti iniziano con `//`.

## Passaggio 2: Configurare l'autenticazione di AAD

Per questa esercitazione si presuppone che si sia in grado di creare un'applicazione di AAD in Azure. Per altre informazioni su come creare un'applicazione di AAD, vedere l'[esercitazione relativa ad Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md). Per questa esercitazione sono necessarie due applicazioni di AAD.

1. La prima applicazione di AAD viene usata per proteggere l'API Web. Assegnare all'applicazione il nome **webAPI**.
2. La seconda applicazione di AAD viene usata per proteggere la registrazione dell'API personalizzata e acquisire l'accesso delegato all'API Web protetta dalla prima app. Assegnare a questa applicazione il nome **webAPI-customAPI**.
3. Per **webAPI** usare la configurazione seguente:  

  1. URL di accesso: ***https://login.windows.net***
  2. URI ID app: ***https://\<URL-della-radice>*** (in genere l'URL del sito Web distribuito in Azure)
  3. URL di risposta: ***https://\<URL-della-radice>/.auth/login/aad/callback***  
  
	>[AZURE.IMPORTANT] L'ID client di questa app sarà necessario in seguito, quindi occorre annotarlo.

4. Per **webAPI-customAPI** usare la configurazione seguente:
  
  1. URL di accesso: **https://login.windows.net**
  2. URI ID app: ***può essere un URL univoco***
  3. URL di risposta: ***https://msmanaged-na.consent.azure-apim.net/redirect***
  4. Aggiungere le autorizzazioni in modo da avere l'accesso delegato a webAPI.
  5. Anche l'ID client di questa app sarà necessario in seguito, quindi occorre annotarlo.
  6. Generare una chiave e archiviarla in una posizione sicura. La chiave sarà necessaria in seguito.

>[AZURE.IMPORTANT] Entrambe le app devono trovarsi nella stessa directory.

## Passaggio 3: Configurare l'autorizzazione semplice nell'app Web

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'app Web distribuita nel **Passaggio 1** in questo argomento.
2. In **Impostazioni** selezionare **"Autenticazione / Autorizzazione"**.
3. Attivare l'**Autenticazione servizio app** e selezionare **Azure Active Directory**. Nel pannello successivo selezionare **Rapida**.  
4. Fare clic su **Seleziona app AD esistente**, quindi selezionare la prima applicazione di AAD creata come parte del Passaggio 2. In questo caso, selezionare **webAPI**.

In questo modo dovrebbe essere configurata l'autenticazione di AAD per l'app Web.

## Passaggio 4: Configurare l'API personalizzata 

1. È necessario modificare il file Swagger per immettere l'oggetto `securityDefintions` e l'autenticazione di AAD usata per l'app Web. Aggiungere le righe di codice seguenti: 

	```javascript
  "host": "<your-root-url>",
  "schemes": [
    "https"						//Change scheme to https 
  ],
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
	```

2. Passare al [portale Web][1] di PowerApps e aggiungere un'API personalizzata. La procedura è illustrata in [Usare le API personalizzate nei flussi logici e in PowerApps](powerapps-register-custom-api.md).

3. Dopo il caricamento del file Swagger, la procedura guidata rileva automaticamente che si sta usando l'autenticazione di AAD per webAPI.

4. Configurare l'autenticazione di AAD per l'API personalizzata:

  1. ID client: ***ID client di webAPI-CustomAPI*** dal punto 4.5 nel **Passaggio 2** in questo argomento
  2. Segreto: ***chiave da webPI-CustomAPI*** dal punto 4.6 nel **Passaggio 2** in questo argomento
  3. URL di accesso: ***https://login.windows.net***
  4. URI risorsa: ***ID client di webAPI*** dal punto 3.4 nel **Passaggio 2** in questo argomento

5. Selezionare **Crea** e provare a creare una connessione sull'API personalizzata. Se la configurazione è corretta, dovrebbe essere possibile accedere senza problemi.

Per un'esperienza più dettagliata sulla creazione di PowerApp e di flussi logici, vedere i riferimenti seguenti:

- [Connettersi a Office 365, Twitter e Microsoft Translator][5]
- [Visualizzare i dati da Office 365][4]
- [Creare un'app dai dati][3]
- [Introduzione ai flussi logici][2]

Per domande o commenti, inviare un messaggio di posta elettronica all'indirizzo [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0413_2016-->