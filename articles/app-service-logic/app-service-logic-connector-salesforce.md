<properties
   pageTitle="Uso del connettore Salesforce nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Salesforce e usarlo in un'app per la logica in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="sameerch"/>


# Uso del connettore Salesforce e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione dello schema 2015-08-01-preview, fare clic su [API di Salesforce](../connectors/create-api-salesforce.md).

Connettersi a Salesforce e creare e modificare entità quali account, clienti potenziali e così via. Di seguito sono riportati gli scenari di integrazione tipici relativi a Salesforce:

- Sincronizzazione degli account tra Salesforce e i sistemi ERP, ad esempio SAP e QuickBooks
- Flusso dall'ordine all'incasso da Salesforce ai sistemi ERP

Tra le impostazioni del pacchetto del connettore, l'utente può specificare le entità che il connettore può gestire e le azioni e i parametri di input e di output vengono popolati in modo dinamico. Di seguito sono elencate le diverse azioni disponibili nel connettore Salesforce:

- Create Entity: usare questa azione per creare una nuova entità Salesforce, ad esempio un account, un case o un oggetto personalizzato
- Aggiorna entità: usare questa azione per aggiornare un'entità Salesforce esistente
- Esegui upsert entità: usare questa azione per aggiornare un'entità Salesforce esistente o per crearne una se non esiste
- Elimina entità: usare questa azione per eliminare un'entità Salesforce esistente
- Esegui query: usare questa azione per eseguire una query SELECT scritta in SOQL (Salesforce Object Query Language)

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore Salesforce al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.


## Creare un'app API connettore di Salesforce
1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Passare a "Web e Mobile > app API " e cercare "Salesforce".
3.	Configurare il connettore di Salesforce fornendo i dettagli per piano di Hosting, il gruppo di risorse e selezionando il nome dell'app API: ![][15]
4. Configurare le entità di Salesforce a cui si è interessati in lettura/scrittura nelle ’Impostazioni del pacchetto' '.

Con questa operazione, è ora possibile creare un'app API Connector di Salesforce.


## Creare un'app per la logica
È meglio creare un'app per la logica semplice che crea un account in Salesforce e aggiorna i dettagli dell'indirizzo di fatturazione dello stesso account.

1.	Accedere al portale di Azure e fare clic su 'Nuovo -> Web e dispositivi mobili -> App per la logica': ![][1]

2.	Nella pagina 'Create logic app' immettere le informazioni richieste, ad esempio nome, piano di servizio app e area: ![][2]

3.	Fare clic su 'Triggers and Actions'. Viene aperto l'editor dell'app per la logica: ![][3]

4.	Selezionare 'Run this logic manually' per fare in modo che questa app per la logica possa essere richiamata solo manualmente: ![][4]

5.	Espandere la sezione relativa alle app per le API nel gruppo di risorse nella raccolta per visualizzare tutte le app per le API disponibili. Selezionare 'Salesforce' nella raccolta per aggiungere il connettore Salesforce al flusso: ![][5]

8.	Per autorizzare l'app per la logica ad accedere all'account Salesforce, fare clic su 'Authorize' per fornire le credenziali di accesso di Salesforce: ![][6]

9.	Si viene reindirizzati alla pagina di accesso di Salesforce dove sarà possibile autenticarsi con le credenziali di accesso di Salesforce: ![][7] ![][8]

10.	Dopo aver completato l'autorizzazione, vengono visualizzate tutte le azioni: ![][9]

11.	Selezionare l'azione 'Create Account' per visualizzare i parametri di input: ![][10]

12.	Specificare 'Account Name' e fare clic su ✓: ![][11]

13.	Selezionare 'Salesforce Connector' nella sezione 'Recently Used' della raccolta. Viene aggiunta una nuova azione Salesforce.

14.	Selezionare Update Account dall'elenco delle azioni. Vengono visualizzati i parametri di input dell'azione Update Account: ![][12]

15.	Fare clic su '+' accanto a 'ID record' per selezionare il valore dell'ID nell'output dell'azione 'Create Account': ![][13]

16.	Immettere i valori per l'indirizzo, la città, il paese e il CAP di fatturazione e fare clic su ✓: ![][14]

17. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Create'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

18. Passare all'app per la logica appena creata e fare clic su 'Run' per avviare l'esecuzione.

19. È possibile controllare che un nuovo account con nome 'Contoso' venga creato nell'account Salesforce.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

<!---HONumber=AcomDC_0224_2016-->