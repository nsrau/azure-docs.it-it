<properties
   pageTitle="Uso del connettore Box nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore Box e usarlo in un'app per la logica in Azure App Service"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="andalmia"/>

# Uso del connettore Box e aggiunta all'app per la logica 
Connettersi a Box per ottenere, caricare, eliminare e svolgere altre operazioni sui file. È possibile usare i connettori nelle app per la logica come parte di un "flusso di lavoro".

In alcuni scenari potrebbe essere necessario usare un connettore Box, che consente di condividere i dati in modo sicuro con chiunque, anche con chi è all'esterno del firewall. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.


## Trigger e azioni
L'app della raccolta Box fornisce le azioni come meccanismi per interagire con Box:

**Azioni**: le azioni consentono di eseguire azioni predefinite sull'account Box configurato con l'app per la logica. Di seguito sono riportate le azioni che è possibile eseguire sull'account Box con il connettore Box:

a. *Elenca file:* questa operazione restituirà le informazioni di tutti i file di una cartella. Elenco dei parametri obbligatori per l'azione:

Nome parametro | Descrizione | Obbligatorio
--- | --- | ---
Percorso cartella | Percorso della cartella da elencare. | Sì

>[AZURE.NOTE]non restituisce i contenuti dei file.

b. *Recupera file:* questa operazione recupera un file, inclusi il contenuto e le proprietà. Elenco dei parametri obbligatori per l'azione:

Nome parametro | Descrizione | Obbligatorio
--- | --- | ---
Percorso file | Percorso della cartella in cui risiede il File. | Sì
Tipo file | Specifica se il file è di testo o binario. | No

> [AZURE.NOTE]Questa operazione non eliminerà il file dopo averlo letto.


c. *Upload File*: Come suggerito dal nome, questa azione consente di caricare il file nell'account Box. Se il file esiste già, non viene sovrascritto e viene generato un errore. Elenco dei parametri obbligatori per l'azione:

Nome parametro | Descrizione | Obbligatorio
--- | --- | ---
Percorso file | Percorso del file. | Sì
Contenuto file | Contenuto del file da caricare. | Sì
Codifica trasferimento contenuto | Tipo di codifica del contenuto: Base64 o None. | 

d. *Delete File*: l'azione elimina il file specificato da una cartella. Se non è possibile trovare il file/la cartella, viene generata un'eccezione. Elenco dei parametri obbligatori per l'azione:

Nome parametro | Descrizione | Obbligatorio
--- | --- | ---
Percorso file | Percorso del file completo incluse le cartelle. | Sì


## Creazione di un connettore Box per l'app per la logica

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Connettore Box", selezionarlo e scegliere **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà:
![][1]
4. Selezionare **Crea**.


## Usare il connettore Box nell'app per la logica

Dopo aver creato l'app per le API, è possibile usare il connettore Box come azione per l'app per la logica. A tale scopo, seguire questa procedura:

1. Nell'app per la logica, aprire **Triggers and Actions** per aprire la finestra di progettazione delle app per la logica e configurare il flusso. Il connettore Box è elencato nella raccolta. Selezionarlo per aggiungerlo automaticamente alla progettazione dell’app per la logica.

	> [AZURE.NOTE]Se il connettore Box viene selezionato all'avvio dell'app per la logica, agisce come trigger. In caso contrario, è possono eseguire azioni su account Box utilizzando il connettore. Il connettore Box non dispone di alcun trigger al momento della pubblicazione di questo articolo.

2. Autenticare e autorizzare le app per la logica in modo che eseguano operazioni per conto dell'utente. Selezionare **Authorize** nel Connettore Box:
![][2]

3. Immettere i dettagli di accesso dell'account Box su cui eseguire le operazioni:
![][3]

4. Concedere alle app per la logica l'accesso al proprio account utente per eseguire l'operazione per proprio conto: 
![][4]

5. Verrà visualizzato l'elenco delle azioni da cui è possibile scegliere l'operazione da eseguire:
![][5]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=August15_HO9-->