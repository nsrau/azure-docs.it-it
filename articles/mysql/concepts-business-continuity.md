---
title: "Panoramica della continuità aziendale con Database di Azure per MySQL"
description: "Panoramica della continuità aziendale con Database di Azure per MySQL."
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: MySQL
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7961da65abacf6058b4b0ece4e9cbe7b8d1ef254
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql"></a>Panoramica della continuità aziendale con Database di Azure per MySQL

Questa panoramica descrive le funzionalità offerte da Database di Azure per MySQL per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Database di Azure per MySQL offre funzionalità di continuità aziendale che includono i backup automatizzati e la possibilità per gli utenti di avviare il ripristino geografico. Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse e usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per le funzionalità disponibili:

| **Funzionalità** | **Basic** | **Utilizzo generico** | **Ottimizzate per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione |
| Ripristino geografico dai backup con replica geografica | Non supportate | ERT < 12 ore<br/>RPO < 1 ora | ERT < 12 ore<br/>RPO < 1 ora |

> [!IMPORTANT]
> Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile usare i backup del servizio per ripristinare un server da svariati eventi che possono causare interruzioni del funzionamento. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un ripristino temporizzato per creare una copia del server a un punto valido nel tempo. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Eseguire il ripristino in seguito un'interruzione del data center a livello di area di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando il data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà, quindi questa opzione funziona solo se è possibile rinunciare al server per un periodo di tempo.

La seconda opzione consiste nell'usare la funzionalità di ripristino geografico di Database di Azure per MySQL che ripristina il server usando i backup con ridondanza geografica. Questi backup sono accessibili anche quando l'area in cui il server è ospitato è offline. È possibile eseguire il ripristino da questi backup a qualsiasi altra area e riportare il server online.

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui backup automatizzati, vedere [Backup in Database di Azure per MySQL](concepts-backup.md).
- Per eseguire il ripristino temporizzato con il portale di Azure, vedere l'articolo su come  [ripristinare un database a un momento specifico con il portale di Azure](howto-restore-server-portal.md).
- Per eseguire il ripristino temporizzato con l'interfaccia della riga di comando di Azure, vedere l'articolo su come  [ripristinare un database a un momento specifico con l'interfaccia della riga di comando](howto-restore-server-cli.md).