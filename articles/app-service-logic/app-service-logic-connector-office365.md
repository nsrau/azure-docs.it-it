<properties
   pageTitle="Connettore Office 365"
   description="Come usare il connettore Office 365"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Uso del connettore Office 365 nell'app per la logica#

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Il connettore Office 365 consente di inviare e ricevere messaggi di posta elettronica e di gestire il calendario e i contatti nell'account di Office 365. È possibile eseguire diverse azioni, ad esempio inviare, ricevere e ottenere messaggi di posta elettronica, creare ed eliminare eventi nel calendario e creare, aggiornare, ottenere ed eliminare i contatti.

**Azioni di base**

- Nuovo messaggio (trigger)
- Invio di un messaggio
- Risposta a un messaggio
- Invio di un evento
- Aggiunta di un contatto

##Creare l'app per le API del connettore O365##
Per creare l'app per le API del connettore O365, eseguire la procedura seguente:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > App per le API" e cercare "Office365".
3.	Configurare il connettore Office 365 specificando i dettagli relativi al piano di hosting e al gruppo di risorse e selezionando il nome dell'app per le API.

	![][21]


##Creare un'app per la logica##
È meglio creare un'app per la logica semplice che viene attivata quando viene ricevuto un messaggio di posta elettronica all'ID posta elettronica per le indagini sulle vendite, ad esempio sales@contoso.com). L'app crea un evento, aggiunge un contatto con i dettagli del mittente, invia un messaggio all'account personale e infine invia una risposta con una conferma.

1.	Accedere al portale di Azure e fare clic su "Nuovo -> Web e dispositivi mobili -> App per la logica"

	![][1]

2.	Nella pagina "Crea app per la logica" immettere le informazioni richieste, ad esempio nome, piano di servizio app e area.

	![][2]

3.	Fare clic su "Trigger e azioni" per aprire la schermata dell'editor dell'app per la logica.

	![][3]

4.	Selezionare il trigger Office 365 dalla sezione relativa alle app per le API nel gruppo di risorse della raccolta per aggiungerlo al flusso.

	![][4]

6.	Per la connessione a Office 365 è necessario autorizzare l'app per la logica ad accedere all'account. Fare clic su "Autorizza" per immettere le credenziali di accesso di Office 365.

	![][5]

7.	Si verrà reindirizzati alla pagina di accesso di Office 365 dove sarà possibile autenticarsi con le credenziali di accesso di Office 365.

	![][6]

	![][7]

8.	Una volta completata l'autorizzazione, vengono visualizzati i trigger di Office 365.

	![][8]

9.	Selezionare il trigger "Nuovo messaggio di posta elettronica" per visualizzare i parametri di input.


10.	Impostare la frequenza del trigger su 'Minuti' e fare clic su ✓.

	![][9]

11. Il trigger 'Nuovo messaggio di posta elettronica' di Office 365 viene configurato ed è possibile visualizzare anche i parametri di output.

	![][10]

12.	Selezionare "Office 365 Connector" nella sezione "Usati di recente" della raccolta. Verrà aggiunta una nuova azione "Office 365".

13.	Selezionare "Invia evento" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Invia evento".

	![][11]

14.	Specificare i dettagli dell'evento e fare clic su ✓.

	![][12]

15.	Selezionare "Office 365 Connector" nella sezione "Usati di recente" della raccolta. Verrà aggiunta una nuova azione "Office 365".

16.	Selezionare "Aggiungi contatto" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Aggiungi contatto".

	![][13]

17.	Fare clic su "+" accanto al campo 'Indirizzo di posta elettronica' e selezionare il valore 'Da' del campo di output nel trigger.

	![][14]

18. Fare clic su ✓ per completare la configurazione dell'azione.

	![][15]

19.	Selezionare "Office 365 Connector" nella sezione "Usati di recente" della raccolta. Verrà aggiunta una nuova azione "Office 365".


20.	Selezionare "Invia messaggio" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Invia messaggio".

	![][19]

21.	Immettere le informazioni richieste per inviare il messaggio. Per creare un messaggio, digitare un testo come quello sotto. Dopo avere configurato l'azione 'Invia messaggio', fare clic su ✓.

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	Selezionare "Office 365 Connector" nella sezione "Usati di recente" della raccolta. Verrà aggiunta una nuova azione "Office 365".


23.	Selezionare "Rispondi a" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Rispondi a".

	![][16]

24.	Fare clic su "+" accanto al campo 'Da', selezionare il valore nell'ID messaggio di output del trigger e fare clic su ✓.

	![][17]

25. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Crea'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

26. Inviare un messaggio all'account con cui si è configurato il trigger. Verranno visualizzati un messaggio nell'account di posta personale e un evento del calendario e un contatto nell'account di posta di Office. Si riceverà anche un messaggio che conferma che a breve arriverà una risposta all'indagine sulle vendite.

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png
[21]: ./media/app-service-logic-connector-office365/21-create-new-o365-api-app.png

<!---HONumber=August15_HO6-->