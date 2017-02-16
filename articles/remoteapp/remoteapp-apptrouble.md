---
title: Risoluzione dei problemi di Azure RemoteApp - Errori di connessione e avvio dell&quot;applicazione | Microsoft Docs
description: Informazioni su come risolvere problemi di avvio e di connessione delle applicazioni in Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 4e6c6571be85cd5fe374c7df644741ae017ccd19
ms.openlocfilehash: 6db4cf048147cb9559ca1a2e6921c679fe6ec9e5


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Risoluzione dei problemi di Azure RemoteApp: Errori di connessione e avvio dell'applicazione
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Le applicazioni ospitate in Azure RemoteApp possono non avviarsi per diversi motivi. In questo articolo vengono descritti vari motivi e i messaggi di errore che gli utenti potrebbero ricevere in caso di mancato avvio delle applicazioni. Vengono affrontati anche gli errori di connessione ma non i problemi di accesso al client di Azure RemoteApp.  

Continuare a leggere per ottenere informazioni sui messaggi di errore comuni causati da problemi di avvio e connessione delle app.

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Messaggio analogo a "Configurazione in corso... Riprovare tra 10 minuti".
Questo errore indica che Azure RemoteApp sta aumentando le prestazioni per soddisfare le esigenze di capacità degli utenti. In background vengono create varie istanze di Azure RemoteApp VM per gestire le esigenze di capacità degli utenti. Questa operazione dura in media cinque minuti ma può richiedere fino a dieci minuti. In alcuni casi, ciò non accade abbastanza rapidamente mentre c'è necessità immediata di risorse. Ad esempio, la situazione in cui alle 9:00 molti utenti hanno la necessità di usare contemporaneamente l'app in Azure RemoteApp. In questa circostanza, è possibile abilitare la **modalità capacità** nel back-end. A tale scopo, aprire un ticket di supporto di Azure. Assicurarsi di specificare il proprio ID sottoscrizione nella richiesta.  

![Messaggio analogo a "Configurazione in corso..."](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Impossibile riconnettersi automaticamente alle applicazioni. Riavviare le applicazioni.
Questo messaggio di errore viene spesso visualizzato se dopo aver usato Azure RemoteApp il PC entra in modalità sospensione per più di 4 ore e quindi viene riattivato. Il PC e il client di Azure RemoteApp tentano di riconnettersi automaticamente ma il timeout è stato superato.  Indicare agli utenti di ritornare all'applicazione e di provare ad aprirla dal client di Azure RemoteApp.

![Impossibile riconnettersi automaticamente alle applicazioni](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Messaggio analogo a "Problemi del profilo temporaneo"
Questo errore si verifica in caso di mancata attivazione del profilo utente (Disco profili utente) per cui all'utente è stato assegnato un profilo temporaneo.  Gli amministratori devono passare alla raccolta nel portale di Azure e quindi passare alla scheda **Sessions** (Sessioni) e provare a disconnettere l'utente facendo clic su **Log Off** (Disconnetti). In questo modo viene imposta la disconnessione completa della sessione utente. Chiedere all'utente di tentare di avviare nuovamente un'app. Se il problema persiste, contattare il supporto tecnico di Azure.

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp ha smesso di funzionare
Questo messaggio di errore indica che si è verificato un problema e che il client di Azure RemoteApp deve essere riavviato. Richiedere agli utenti di selezionare **Chiudi programma** e di avviare nuovamente il client di Azure RemoteApp.  Se il problema persiste, aprire un ticket di supporto di Azure.

![Azure RemoteApp ha smesso di funzionare](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Si è verificato un errore durante l'accesso di Connessione Desktop remoto alla risorsa. Ritentare la connessione o contattare l'amministratore di rete
Si tratta di un messaggio di errore generico. Contattate il supporto tecnico di Azure. 

![Messaggio generico di Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png) 




<!--HONumber=Jan17_HO1-->


