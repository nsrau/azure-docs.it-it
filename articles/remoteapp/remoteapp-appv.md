---
title: Uso delle app App-V in Azure RemoteApp| Microsoft Docs
description: Informazioni sull&quot;uso delle app App-V in Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0e82c639ea7bfceb89f0cf9c59ab657a951f2d5f
ms.lasthandoff: 03/31/2017


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>Uso delle app App-V in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

È possibile usare le applicazioni App-V in una raccolta ibrida di Azure RemoteApp, che richiede l'aggiunta al dominio.

Prima di iniziare, assicurarsi di installare il client App-V 5.1 con gli aggiornamenti più recenti. Sarà necessario creare un' [immagine personalizzata](remoteapp-create-custom-image.md) che include il client App-V.  

L'uso dell'infrastruttura App-V esistente con Azure RemoteApp è una procedura semplice. Poiché in una rete virtuale di Azure viene distribuita una raccolta ibrida con accesso al controller di dominio e le macchine virtuali vengono aggiunte al dominio, è possibile sfruttare i vantaggi offerti dai metodi di distribuzione e dall'infrastruttura App-V esistenti per ospitare facilmente l'applicazione App-V in Azure RemoteApp. Di seguito sono riportate alcune considerazioni da tenere presenti in base al tipo di distribuzione di App-V di cui si dispone:

| Opzioni di configurazione |  | Positive | Negative |
| --- | --- | --- | --- |
| Metodo di distribuzione |Streaming (su richiesta) |App sempre aggiornata |Prima latenza |
| Montato |Più rapido. App già presente nella macchina virtuale |Bloat: occupa lo spazio dell'immagine (limite di 127 GB) | |
| Archiviazione percorso app |Contenuto condiviso |App eseguita nella memoria dell'istanza di Azure RemoteApp |Usa la memoria e una buona connessione al server (file) di streaming in cui si trova l'app |
| Disco (memorizzato nella cache) |Esecuzione rapida. Applicazione non dipende dalla disponibilità dell'origine del contenuto |Bloat: occupa lo spazio dell'immagine (limite di 127 GB) | |
| Destinazione |Utente |Richiede un'infrastruttura App-V autonoma completa | |
| Globale (computer) |Prepubblicazione o destinazione mediante server di pubblicazione |Se si desidera aggiornare l'app (di grandi dimensioni), è necessario aggiornare l'immagine di Azure. Occupa spazio nell'immagine. | |

 Dopo aver creato l'immagine personalizzata e la raccolta ibrida, è possibile pubblicare l'applicazione, assegnare gli utenti e usare le applicazioni App-V esistenti ospitate in Azure RemoteApp distribuite a qualsiasi dispositivo e in qualsiasi luogo.


