<properties
	pageTitle="Gestire e monitorare le app per le API e i connettori nel servizio app | Microsoft Azure"
	description="Visualizzare le prestazioni dei connettori e delle app per le API nel servizio app di Azure; architettura dei microservizi"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="mandia"/>

# Gestire e monitorare le app per le API e i connettori predefiniti

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

È stata creata un'app per le API predefinita. Come si procede a questo punto?

In Azure ogni app per le API è un sito Web distinto ospitato in Azure. È quindi possibile vedere facilmente quante richieste vengono fatte e la quantità di dati usata dal connettore È anche possibile eseguire il backup dell'app per le API, creare avvisi, abilitare Tinfoil Security e aggiungere utenti e ruoli.

Questo argomento descrive alcune delle diverse opzioni disponibili per gestire l'app per le API.

Per vedere le funzionalità predefinite, aprire l'app per le API nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Se l'app per le API si trova nella schermata iniziale, selezionarla per aprire le proprietà. È anche possibile selezionare **Sfoglia**, **App per le API** e quindi l'app per le API.

![][browse]

## Visualizzare le proprietà immesse

All'apertura dell'app per le API sono disponibili diverse funzionalità e attività:

![][settings]

È possibile:

- Visualizzare le informazioni specifiche sull'app per le API, inclusi i dettagli della sottoscrizione e l'elenco degli utenti che hanno accesso all'app per le API in **Impostazioni**. Tra le altre funzionalità disponibili, è anche possibile aumentare o ridurre il numero di istanze dell'app per le API tramite la funzionalità di ridimensionamento.
- Usare i pulsanti **Avvia** e **Arresta** per controllare l'app per le API.
- Quando vengono effettuati aggiornamenti del prodotto per i file sottostanti usati dall'app per le API, è possibile fare clic su **Aggiorna** per ottenere le ultime versioni. Ad esempio, se è disponibile una correzione o un aggiornamento della sicurezza rilasciato da Microsoft, facendo clic su **Aggiorna** l'app per le API viene aggiornata automaticamente, in modo da includere la correzione.
- Selezionare **Cambia piano** per effettuare l'aggiornamento o il downgrade, in base all'utilizzo dei dati da parte dell'app per le API. È anche possibile usare questa funzionalità per vedere l'utilizzo dei propri dati.
- Quando si crea un connettore con tabelle, ad esempio SQL Connector, è possibile immettere un nome di tabella a cui connettersi. Viene creato automaticamente uno schema basato sulla tabella, che sarà disponibile quando si fa clic su **Scarica schemi**. È quindi possibile usare questo schema scaricato per creare una trasformazione o una mappa.

## Modificare i valori di configurazione del connettore o dell'API immessi

Dopo avere configurato o creato il connettore predefinito, è possibile modificare i valori immessi. Se ad esempio è stato configurato SQL Connector e si vuole modificare il nome di SQL Server o il nome della tabella, è possibile farlo nel pannello dell'app per le API del connettore.

Di seguito è riportata la procedura:

1. Aprire il connettore o l'app per le API. Si aprirà il pannello dell'app per le API.
2. In **Informazioni di base**, fare clic sul collegamento ipertestuale nella proprietà dell'host. Il nome del collegamento ipertestuale è simile a *slackconnector* o *microsoftsqlconnector123*:

	![][apiapphost]

3. Nel pannello Host app API selezionare **Impostazioni**. Nel pannello Impostazioni selezionare **Impostazioni applicazione**. I valori di configurazione sono elencati in **Impostazioni app**:

	![][hostsettings]

4. Fare clic sull'impostazione che si vuole modificare, immettere il nuovo valore e scegliere **Salva** per salvare le modifiche.


## Installare Gestione connessione ibrida - Facoltativo

![][hcsetup]

Gestione connessione ibrida consente di connettersi a un sistema locale, ad esempio SQL Server o SAP. La connettività ibrida usa il bus di servizio di Azure per la connessione e il controllo della sicurezza tra le risorse di Azure e le risorse locali.

Vedere [Uso di Gestione connessione ibrida nel servizio app di Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Gestione connessione ibrida è necessario solo se ci si connette a una risorsa locale protetta da un firewall. Se non ci si connette a un sistema locale, Gestione connessione ibrida potrebbe non essere presente nel pannello del connettore.

## Monitorare le prestazioni
La metrica delle prestazioni è costituita da funzionalità predefinite e incluse in ogni app per le API creata. Questa metrica è specifica dell'app per le API ospitata in Azure: Metriche di esempio:

![][monitoring]

È possibile:

- Selezionare **Richieste ed errori** per aggiungere diverse metriche delle prestazioni, inclusi i codici di errore HTTP comunemente noti, come i codici di stato HTTP 200, 400 o 500. È anche possibile vedere i tempi di risposta, la quantità di richieste effettuate all'app per le API e la quantità di dati in entrata e in uscita. In base alla metrica delle prestazioni, è possibile creare avvisi di posta elettronica se una metrica supera una determinata soglia.
- In **Utilizzo** è possibile vedere la quantità di **CPU** usata dall'app per le API, esaminare l'attuale **Quota di utilizzo** in MB e vedere l'utilizzo massimo dei dati in base al proprio piano dei costi. **Spesa stimata** può essere utile per determinare i potenziali costi di esecuzione dell'app per le API.
- Selezionare **Processi** per aprire Esplora processi. Verranno mostrate le istanze Web e le relative proprietà, incluso il conteggio dei thread e l'utilizzo della memoria.

Con questi strumenti è possibile determinare se è necessario aumentare o ridurre il piano di servizio app in base alle esigenze aziendali. Queste funzionalità sono integrate nel portale e non sono richiesti altri strumenti.

## Controllare la sicurezza

Le app per le API usano la sicurezza basata sui ruoli. Questi ruoli si applicano all'intera esperienza di Azure, incluse le app per le API e altre risorse di Azure. I ruoli includono:

Ruolo | Descrizione
--- | ---
Proprietario | Ha l'accesso completo all'esperienza di gestione e può concedere l'accesso ad altri utenti o gruppi.
Collaboratore | Ha l'accesso completo all'esperienza di gestione Non può concedere l'accesso ad altri utenti o gruppi.
Lettore | Può visualizzare tutte le risorse, eccetto i segreti.
Amministratore accessi utente | Può visualizzare tutte le risorse, creare/gestire i ruoli e creare/gestire i ticket di supporto.

Vedere [Controllo degli accessi in base al ruolo nel portale di Microsoft Azure](../active-directory/role-based-access-control-configure.md)

È possibile aggiungere facilmente utenti e assegnare loro ruoli specifici per l'app per le API. Il portale mostra a quali utenti è consentito l'accesso e il ruolo assegnato:

![][access]

- Selezionare **Utenti** per aggiungere un utente, assegnare un ruolo e rimuovere un utente.
- Selezionare **Ruoli** per vedere tutti gli utenti in un ruolo specifico, aggiungere un utente a un ruolo e rimuovere un utente da un ruolo.


## Altre funzionalità utili
- Selezionare **Definizione API** per aprire il file Swagger creato automaticamente per l'app per le API specifica.
- Selezionare **Dipendenze** per visualizzare i file richiesti dall'app per le API. Se ad esempio si usa SAP Connector, vengono installati alcuni file aggiuntivi in Gestione connessioni ibride in locale. Queste dipendenze sono visualizzate nel pannello dell'app per le API.

> [AZURE.IMPORTANT] Quando si visualizzano le proprietà dell'app per le API, in **Informazioni di base** sono presenti i collegamenti **Host** e **Gateway** che aprono nuovi pannelli:
>
> ![][host]
>
> Queste proprietà sono specifiche del sito Web che ospita l'app per le API. Quando si usa un'app per le API o un connettore predefinito, la maggior parte di queste proprietà non sono in effetti applicabili ed è consigliabile non aggiornarle. Se è stata creata un'app per le API personalizzata in Visual Studio ed è stata distribuita nella sottoscrizione di Azure, è possibile usare i pannelli Host e Gateway.


>[AZURE.NOTE] Per iniziare a usare le app per la logica di Azure prima di creare un account di Azure, passare alla pagina [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Altre informazioni

[Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md)<br/> [Elenco di connettori e app per le API nel servizio app](app-service-logic-connectors-list.md)<br/> [Controllo degli accessi in base al ruolo nel portale di Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/> [Uso di Gestione connessione ibrida nel servizio app di Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_0413_2016-->