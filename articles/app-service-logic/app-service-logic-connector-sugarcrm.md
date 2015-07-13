<properties 
   pageTitle="SugarCRM Connector" 
   description="Come usare SugarCRM Connector" 
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
   ms.date="04/01/2015"
   ms.author="vagarw"/>


#Uso di SugarCRM Connector nell'app per la logica#

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Il connettore SugarCRM permette di creare e modificare diverse entità, ad esempio account, lead, contatti e così via. Di seguito sono riportati gli scenari di integrazione tipici relativi a SugarCRM.

- Sincronizzazione degli account tra SugarCRM e sistemi ERP come SAP

- Sincronizzazione di account, contatti e clienti potenziali tra Marketo e SugarCRM

- Flusso dall'ordine all'incasso da SugarCRM ai sistemi ERP


Tra le impostazioni del pacchetto del connettore, l'utente può specificare le entità che il connettore può gestire e le azioni e i parametri di input e di output vengono popolati in modo dinamico.

##Azioni SugarCRM Connector##
Di seguito sono indicate le diverse azioni disponibili in SugarCRM Connector.
 
- Create Module: usare questa azione per creare un nuovo record per il modulo SugarCRM, ad esempio Accounts, Leads e Contacts.

- Update Module: usare questa azione per aggiornare un record esistente per il modulo SugarCRM.

- Delete Module: usare questa azione per eliminare un record esistente per il modulo SugarCRM.

- List Module: usare questa azione per elencare i record filtrati. Se non viene specificata alcuna query, vengono restituiti tutti i record.

- Get Module: usare questa azione per recuperare un singolo record dal modulo specificato.

- Get Record Count: usare questa azione per ottenere il numero di record presenti nel modulo che corrispondono alla query. Se non viene specificata alcuna query, viene restituito il numero totale di record presenti nel modulo.

- Check Duplicate Module: usare questa azione per verificare la presenza di eventuali record duplicati all'interno di un modulo.

*Nota*: per altri dettagli sugli argomenti supportati nella query, fare riferimento alla documentazione dell'API REST di SugarCRM.
   
##Creare un'app per le API SugarCRM Connector##
1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "SugarCRM".
3.	Configurare SugarCRM Connector specificando i dettagli relativi al piano di hosting e al gruppo di risorse e selezionando il nome dell'app per le API.

4. Configurare SugarCRM Connector e fare clic su Create. Di seguito sono elencate le impostazioni pacchetto che è necessario specificare per creare il connettore:

	<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Site URL</td>
    <td>Sì</td>
    <td>Specificare l'URL dell'istanza di SugarCRM, ad esempio https://abcde1234.sugarcrm.com</td>
  </tr>
  <tr>
    <td>Client Id</td>
    <td>Sì</td>
    <td>Specificare la chiave utente della chiave OAuth 2.0 in SugarCRM </td>
  </tr>
  <tr>
    <td>Client Secret</td>
    <td>Sì</td>
    <td>Specificare la chiave privata utente della chiave OAuth 2.0 in SugarCRM </td>
  </tr>
<tr>
    <td>Username</td>
    <td>Sì</td>
    <td>Specificare il nome dell'utente SugarCRM</td>
  </tr>
	<tr>
    <td>Password</td>
    <td>Sì</td>
    <td>Specificare la password dell'utente SugarCRM</td>
  </tr>
  <tr>
    <td>Module Names</td>
    <td>Sì</td>
    <td>Specificare i moduli di SugarCRM, ad esempio Accounts, Contacts, Products e così via, in cui si desidera eseguire l'operazione<br><br>Esempio: Accounts, Leads, Contacts</td>
  </tr>
</table>![][9]



##Creare un'app per la logica##
In questa sezione viene descritta la creazione di una semplice app per la logica che crea un account in SugarCRM e aggiorna i dettagli dell'indirizzo di fatturazione dello stesso account.

1.	Accedere al portale di Azure e fare clic su "Nuovo -> Web e dispositivi mobili -> App per la logica"

	![][1]

2.	Nella pagina "Crea app per la logica" immettere le informazioni richieste, ad esempio nome, piano di servizio app e area.

	![][2]

3.	Fare clic su "Trigger e azioni" per aprire la schermata dell'editor dell'app per la logica. Selezionare "Esegui app per la logica manualmente" per fare in modo che questa app per la logica possa essere richiamata solo manualmente.


5.	Espandere la sezione relativa alle app per le API nel gruppo di risorse nella raccolta per visualizzare tutte le app per le API disponibili. Selezionare 'SugarCRM' dalla raccolta. 'SugarCRM Connector' viene aggiunto al flusso.


	![][3]

6.	Selezionare l'azione "Crea account" per visualizzare i parametri di input.

	![][4]

12.	Specificare come nome 'Microsoft Account' e fare clic su ✓.

	![][5]

13.	Selezionare 'SugarCRM Connector' nella sezione 'Recently Used' della raccolta. Viene aggiunta una nuova azione SugarCRM.

14.	Selezionare "Aggiorna account" nell'elenco di azioni. Verranno visualizzati i parametri di input dell'azione "Aggiorna account".

	![][6]

15.	Fare clic su "+" accanto a "ID record" per selezionare il valore dell'ID nell'output dell'azione "Crea account".

	![][7]

16.	Fornire valori per le informazioni sull'indirizzo di fatturazione e fare clic su ✓.

	![][8]

17. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Crea'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

18. Passare all'app per la logica appena creata e fare clic su 'Esegui' per avviare l'esecuzione.

19. È possibile verificare che nel proprio account SugarCRM venga creato un nuovo account con il nome 'Microsoft Account' e che tale account venga aggiornato con le informazioni relative all'indirizzo di fatturazione.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png


 

<!---HONumber=62-->