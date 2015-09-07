<properties
   pageTitle="Uso del connettore SugarCRM nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore SugarCRM e usarlo in un'app per la logica in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="sameerch"/>


# Uso del connettore SugarCRM e aggiunta all'app per la logica
Il connettore SugarCRM consente di creare e modificare diverse entità, ad esempio account, clienti potenziali, contatti e così via. Di seguito sono riportati gli scenari di integrazione tipici relativi a SugarCRM:

- Sincronizzazione degli account tra SugarCRM e sistemi ERP come SAP
- Sincronizzazione di account, contatti e clienti potenziali tra Marketo e SugarCRM
- Flusso dall'ordine all'incasso da SugarCRM ai sistemi ERP

Tra le impostazioni del pacchetto del connettore, è possibile specificare le entità che il connettore può gestire e le azioni e i parametri di input e di output vengono popolati in modo dinamico.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore SugarCRM al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.



## Azioni del connettore SugarCRM
Di seguito sono indicate le diverse azioni disponibili nel connettore SugarCRM.

- Create Module: usare questa azione per creare un nuovo record per il modulo SugarCRM, ad esempio Accounts, Leads e Contacts.

- Update Module: usare questa azione per aggiornare un record esistente per il modulo SugarCRM.

- Delete Module: usare questa azione per eliminare un record esistente per il modulo SugarCRM.

- List Module: usare questa azione per elencare i record filtrati. Se non viene specificata alcuna query, vengono restituiti tutti i record.

- Get Module: usare questa azione per recuperare un singolo record dal modulo specificato.

- Get Record Count: usare questa azione per ottenere il numero di record presenti nel modulo che corrispondono alla query. Se non viene specificata alcuna query, viene restituito il numero totale di record presenti nel modulo.

- Check Duplicate Module: usare questa azione per verificare la presenza di eventuali record duplicati all'interno di un modulo.

*Nota*: per altri dettagli sugli argomenti supportati nella query, vedere la documentazione dell'[API REST di SugarCRM](https://msdn.microsoft.com/library/dn705870).

## Creare un'app per le API del connettore SugarCRM
1.	Passare all'indirizzo portal.azure.com. Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Passare a "Marketplace > tutto" e cercare "SugarCRM".
3.	Configurare il connettore SugarCRM specificando i dettagli relativi al piano di hosting e al gruppo di risorse e selezionando il nome dell'app per le API.
4. Configurare le impostazioni del pacchetto del connettore SugarCRM. Di seguito sono elencate le impostazioni pacchetto che è necessario specificare per creare il connettore:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Site URL | Sì | Immettere l'URL dell'istanza di SugarCRM. Ad esempio, immettere https://abcde1234.sugarcrm.com.
Client Id | Sì | Immettere la chiave utente della chiave OAUTH 2.0 in SugarCRM. 
Client Secret | Sì | Immettere il segreto utente di OAUTH.
Nome utente | Sì | Immettere il nome dell'utente SugarCRM.
Password | Sì | Immettere la password dell'utente SugarCRM.
Module Names | Sì | Immettere i moduli di SugarCRM, ad esempio Accounts, Contacts e Products, in cui si vuole eseguire l'operazione<br><br>Esempio: Accounts, Leads, Contacts  
  
![][9]



## Creare un'app per la logica
In questa sezione viene descritta la creazione di una semplice app per la logica che crea un account in SugarCRM e aggiorna i dettagli dell'indirizzo di fatturazione dello stesso account.

1.	Accedere al portale di Azure e fare clic su 'Nuovo -> Web e dispositivi mobili -> App per la logica': ![][1]

2.	Nella pagina 'Create logic app' immettere le informazioni richieste, ad esempio nome, piano di servizio app e area: ![][2]

3.	Fare clic su 'Triggers and Actions' per aprire la schermata dell'editor dell'app per la logica. Selezionare 'Run this logic manually' per fare in modo che questa app per la logica possa essere richiamata solo manualmente.

4.	Espandere la sezione relativa alle app per le API nel gruppo di risorse nella raccolta per visualizzare tutte le app per le API disponibili. Selezionare 'SugarCRM' dalla raccolta per aggiungere il connettore SugarCRM al flusso: ![][3]

5.	Selezionare l'azione 'Create Account' per visualizzare i parametri di input: ![][4]

6.	Specificare come nome 'Microsoft Account' e fare clic su ✓: ![][5]

7.	Selezionare 'SugarCRM Connector' nella sezione 'Recently Used' della raccolta. Viene aggiunta una nuova azione SugarCRM.

8.	Selezionare 'Update Account' (nelle azioni avanzate '...') dall'elenco di azioni per visualizzare i parametri di input dell'azione 'Update Account': ![][6]

9.	Fare clic su '...' accanto a "Record Id" per selezionare il valore dell'ID nell'output dell'azione 'Create Account': ![][7]

10.	Fornire valori per le informazioni sull'indirizzo di fatturazione e fare clic su ✓: ![][8]

11. Fare clic su OK nella schermata dell'editor dell'app per la logica, quindi fare clic su 'Create'. Il completamento della creazione dell'app per le API richiederà circa 30 secondi.

12. Passare all'app per la logica appena creata e fare clic su 'Run Now' per avviare l'esecuzione.

13. È possibile verificare che nel proprio account SugarCRM venga creato un nuovo account con il nome 'Microsoft Account' e che tale account venga aggiornato con le informazioni relative all'indirizzo di fatturazione.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=August15_HO9-->