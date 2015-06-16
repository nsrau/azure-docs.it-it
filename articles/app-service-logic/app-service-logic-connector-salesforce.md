<properties 
   pageTitle="Connettore Salesforce" 
   description="Come usare il connettore Salesforce" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


#Uso del connettore Salesforce nell'app per la logica#

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Il connettore Salesforce permette di creare e modificare diverse entità, ad esempio account, lead e così via. Di seguito sono riportati gli scenari di integrazione tipici relativi a Salesforce.

- Sincronizzazione degli account tra Salesforce e i sistemi ERP, ad esempio SAP e QuickBooks	

- Flusso dall'ordine all'incasso da Salesforce ai sistemi ERP


Tra le impostazioni del pacchetto del connettore, l'utente può specificare le entità che il connettore può gestire e le azioni e i parametri di input e di output vengono popolati in modo dinamico. Di seguito sono elencate le diverse azioni disponibili nel connettore Salesforce.
 
- Crea entità: usare questa azione per creare una nuova entità Salesforce, ad esempio un account o un caso.

- Aggiorna entità: usare questa azione per aggiornare un'entità Salesforce esistente

- Esegui upsert entità: usare questa azione per aggiornare un'entità Salesforce esistente o per crearne una se non esiste

- Elimina entità: usare questa azione per eliminare un'entità Salesforce esistente

- Esegui query: usare questa azione per eseguire una query SELECT scritta in SOQL (Salesforce Object Query Language)


##Creare un'app per le API per il connettore SalesForce##
1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > App per le API" e cercare "SalesForce".
3.	Configurare il connettore SalesForce specificando i dettagli relativi al piano di hosting e al gruppo di risorse e selezionando il nome dell'app per le API.

	![][15]
4. Configurare le entità SalesForce da leggere/scrivere in 'Impostazioni pacchetto'.

A questo punto è possibile creare un'app per le API per il connettore SalesForce.


##Creare un'app per la logica##
È meglio creare un'app per la logica semplice che crea un account in Salesforce e aggiorna i dettagli dell'indirizzo di fatturazione dello stesso account.

1.	Accedere al portale di Azure e fare clic su "Nuovo -> Web e dispositivi mobili -> App per la logica"

	![][1]

2.	Nella pagina "Crea app per la logica" immettere le informazioni richieste, ad esempio nome, piano di servizio app e area.

	![][2]

3.	Fare clic su "Trigger e azioni" per aprire la schermata dell'editor dell'app per la logica.

	![][3]

4.	Selezionare "Esegui app per la logica manualmente" per fare in modo che questa app per la logica possa essere richiamata solo manualmente.

    ![][4]

5.	Espandere la sezione relativa alle app per le API nel gruppo di risorse nella raccolta per visualizzare tutte le app per le API disponibili. Selezionare "Salesforce" nella raccolta per aggiungere "Salesforce Connector" al flusso.


	![][5]

8.	Per autorizzare l'app per la logica ad accedere all'account SalesForce, fare clic su 'Autorizza' per fornire le credenziali di accesso di Salesforce.

	![][6]

9.	Si verrà reindirizzati alla pagina di accesso di Salesforce dove sarà possibile autenticarsi con le credenziali di accesso di Salesforce.

	![][7]

	![][8]

10.	Una volta completata l'autorizzazione, vengono visualizzate tutte le azioni.

	![][9]

11.	Selezionare l'azione "Crea account" per visualizzare i parametri di input.

	![][10]

12.	Specificare il "Nome account" e fare clic su ✓.

	![][11]

13.	Selezionare "Salesforce Connector" nella sezione "Usati di recente" della raccolta. Verrà aggiunta una nuova azione Salesforce.

14.	Selezionare "Aggiorna account" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Aggiorna account".

	![][12]

15.	Fare clic su "+" accanto a "ID record" per selezionare il valore dell'ID nell'output dell'azione "Crea account".

	![][13]

16.	Immettere i valori per l'indirizzo, la città, il paese e il CAP di fatturazione e fare clic su ✓.

	![][14]

17. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Crea'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

18. Passare all'app per la logica appena creata e fare clic su 'Esegui' per avviare l'esecuzione.

19. È possibile controllare che un nuovo account con nome 'Contoso' venga creato nell'account Salesforce.

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



<!--HONumber=54--> 