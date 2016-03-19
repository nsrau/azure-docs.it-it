<properties 
    pageTitle="Risoluzione dei problemi di Azure RemoteApp - Errori di connessione e avvio dell'applicazione | Microsoft Azure" 
    description="Informazioni su come risolvere problemi di avvio e di connessione delle applicazioni in Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/02/2016" 
    ms.author="elizapo" />



#Risoluzione dei problemi di Azure RemoteApp - Errori di connessione e avvio dell'applicazione 

Le applicazioni ospitate in Azure RemoteApp possono non avviarsi per diversi motivi. In questo articolo vengono descritti vari motivi e i messaggi di errore che gli utenti potrebbero ricevere in caso di mancato avvio delle applicazioni. Vengono affrontati anche gli errori di connessione. (Questo articolo non affronta i problemi di accesso al client di Azure RemoteApp).

Continuare a leggere per ottenere informazioni sui messaggi di errore comuni causati da problemi di avvio e connessione delle app.

##Messaggio analogo a "Configurazione in corso... Riprovare tra 10 minuti".

Questo errore indica che Azure RemoteApp sta aumentando le prestazioni per soddisfare le esigenze di capacità degli utenti. In background vengono create varie istanze di Azure RemoteApp VM per gestire le esigenze di capacità degli utenti. Questa operazione dura in media cinque minuti ma può richiedere fino a dieci minuti. In alcuni casi, ciò non accade abbastanza rapidamente mentre c'è necessità immediata di risorse. Ad esempio, la situazione in cui alle 9:00 molti utenti hanno la necessità di usare contemporaneamente l'app in Azure RemoteApp. In questa circostanza, è possibile abilitare la **modalità capacità** nel back-end. A tale scopo, aprire un ticket di supporto tecnico di Azure e/o scrivere un'email all'indirizzo [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Assicurarsi di specificare il proprio ID sottoscrizione nella richiesta.

![Messaggio analogo a "Configurazione in corso..."](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## Impossibile riconnettersi automaticamente alle applicazioni. Riavviare le applicazioni.  

Questo messaggio di errore viene spesso visualizzato se dopo aver usato Azure RemoteApp il PC entra in modalità sospensione per più di 4 ore e quindi viene riattivato. Il PC e il client di Azure RemoteApp tentano di riconnettersi automaticamente ma il timeout è stato superato. Indicare agli utenti di ritornare all'applicazione e di provare ad aprirla dal client di Azure RemoteApp.

![Impossibile riconnettersi automaticamente alle applicazioni](./media/remoteapp-apptrouble/ra-apptrouble2.png)

## Messaggio analogo a "Problemi del profilo temporaneo" 

Questo errore si verifica in caso di mancata attivazione del profilo utente (Disco profili utente) per cui all'utente è stato assegnato un profilo temporaneo. Gli amministratori devono navigare alla raccolta nel portale di Azure e quindi passare alla scheda **Sessioni** e tentare di selezionare **Disconnetti** per l'utente. In questo modo viene imposta la disconnessione completa della sessione utente. Chiedere all'utente di tentare di avviare nuovamente un'app. Se il problema persiste, contattare il supporto tecnico di Azure e/o scrivere un'email all'indirizzo [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## Azure RemoteApp ha smesso di funzionare

Questo messaggio di errore indica che si è verificato un problema e che il client di Azure RemoteApp deve essere riavviato. Richiedere agli utenti di selezionare **Chiudi programma** e di avviare nuovamente il client di Azure RemoteApp. Se il problema persiste, aprire un ticket di supporto tecnico di Azure e/o scrivere un'email all'indirizzo [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp ha smesso di funzionare](./media/remoteapp-apptrouble/ra-apptrouble3.png)

## Si è verificato un errore durante l'accesso di Connessione Desktop remoto alla risorsa. Ritentare la connessione o contattare l'amministratore di rete

Si tratta di un messaggio di errore generico: contattare il supporto tecnico di Azure e/o scrivere un'email all'indirizzo [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) in modo che il problema possa essere analizzato.

![Messaggio generico di Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png)

<!---HONumber=AcomDC_0211_2016-->