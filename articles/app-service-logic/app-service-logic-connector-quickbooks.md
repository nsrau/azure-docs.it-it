<properties
   pageTitle="QuickBooks Connector"
   description="Come usare QuickBooks Connector"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="sameerch"/>


#Uso di QuickBooks Connector nell'app per la logica#

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. QuickBooks Connector consente di creare e modificare diverse entità QuickBooks. Di seguito è riportato un elenco di entità QuickBooks supportate da QuickBooks Connector.

Entità|Descrizione
---|---
Account|Si tratta di un componente di un piano dei conti ed è parte di una contabilità generale. Viene usato per registrare un importo monetario totale a fronte di un utilizzo specifico.
CreditMemo|Si tratta di una transazione finanziaria che rappresenta un rimborso o un credito di pagamento per merci o servizi acquistati.
Customer|È il consumatore del servizio o del prodotto offerto dall'azienda.
Estimate|Rappresenta una proposta di transazione finanziaria tra un'azienda e un cliente riguardo alla vendita di merci o servizi, inclusa l'indicazione dei prezzi.
Invoice|È un documento di vendita a fronte del quale il cliente paga un prodotto o un servizio. Altre informazioni sull'uso del modello di dati Invoice sono disponibili qui.
Item|Un elemento che l'azienda acquista, vende o rivende, ad esempio prodotti, spese di spedizione e movimentazione, sconti e IVA (se applicabile). Su una fattura o su un altro documento di vendita un articolo viene visualizzato come riga.
SalesReceipt|Questa entità rappresenta la ricevuta di vendita fornita al cliente.



##Azioni QuickBooks ##
Di seguito sono indicate le diverse azioni disponibili in QuickBooks Connector.

Azione|Descrizione
---|---
Read Entity|Legge l'oggetto entità
Create Or Update Entity|Crea o aggiorna un oggetto entità. Se l'oggetto esiste già, viene aggiornato. In caso contrario, viene creato un nuovo oggetto
Elimina|Questa azione elimina l'oggetto specificato dall'entità selezionata
Query|L'operazione di query è il metodo per la creazione di una query guidata su un'entità.

##Creare un'app per le API QuickBooks Connector##
1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "QuickBooks".
3.	Configurare QuickBooks Connector specificando i dettagli relativi al piano di hosting e al gruppo di risorse e selezionando il nome dell'app per le API.

	![][13]
4. Configurare le entità QuickBooks che si desidera leggere/scrivere in Package Settings.

A questo punto è possibile creare un'app per le API QuickBooks Connector.


##Creare un'app per la logica##
In questa sezione viene descritta la creazione di una semplice app per la logica che crea un account in QuickBooks e aggiorna il valore di 'Category Type' dello stesso account.

1.	Accedere al portale di Azure e fare clic su "Nuovo -> Web e dispositivi mobili -> App per la logica"

	![][1]

2.	Nella pagina "Crea app per la logica" immettere le informazioni richieste, ad esempio nome, piano di servizio app e area.

	![][2]

3.	Fare clic su "Trigger e azioni" per aprire la schermata dell'editor dell'app per la logica.

	![][3]

4.	Selezionare "Esegui app per la logica manualmente" per fare in modo che questa app per la logica possa essere richiamata solo manualmente.


5.	Espandere la sezione relativa alle app per le API nel gruppo di risorse nella raccolta per visualizzare tutte le app per le API disponibili. Selezionare 'QuickBooks Connector' dalla raccolta. 'QuickBooks Connector' viene aggiunto al flusso.


6.	Se QuickBooks è online, per eseguire operazioni per conto dell'utente è necessario autenticare e autorizzare le app per la logica. Per avviare l'autorizzazione, fare clic su Authorize in QuickBooks Connector.

	![][4]

7.	Facendo clic su Authorize verrà visualizzata la finestra di dialogo di autenticazione di QuickBooks. Specificare i dettagli di accesso dell'account di QuickBooks su cui eseguire le operazioni.

	![][5]

8. Fare clic su Authorize nella finestra di dialogo di consenso per concedere all'account l'accesso alle app per la logica al fine di eseguire operazioni per conto dell'utente.

	![][6]

9.	Una volta completata l'autorizzazione, vengono visualizzate tutte le azioni.

	![][7]

10.	Selezionare l'azione 'Create Or Update Account'. Vengono visualizzati i parametri di input.

	![][8]

11.	Specificare un valore per 'Name' e 'Account Type', quindi fare clic su ✓.

	![][9]

12.	Selezionare 'QuickBooks Connector' nella sezione 'Recently Used' della raccolta. Viene aggiunta una nuova azione QuickBooks.

13.	Selezionare 'Create or Update Account' dall'elenco delle azioni. Vengono visualizzati i parametri di input dell'azione.

	![][10]

14.	Fare clic su '+' accanto a 'ID' per selezionare il valore dell'ID nell'output dell'azione "Create Account".

	![][11]

15.	Fornire nuovi valori per Account Type e fare clic su ✓.

	![][12]

16. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Crea'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

17. Passare all'app per la logica appena creata e fare clic su 'Esegui' per avviare l'esecuzione.

18. È possibile controllare che nell'account QuickBooks venga creato un nuovo account con nome 'Contoso'.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=August15_HO8-->