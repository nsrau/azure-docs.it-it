<properties
	pageTitle="Accessi dopo più errori"
	description="Un report indica gli utenti che hanno eseguito correttamente l'accesso dopo più tentativi di accesso consecutivi non riusciti."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Accessi dopo più errori

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Questo report indica gli utenti che hanno eseguito correttamente l'accesso dopo più tentativi di accesso consecutivi non riusciti. Le possibili cause includono: <ul><li>L'utente ha dimenticato la password</li><li>L'utente è vittima di un attacco di forza bruta che ha portato all'individuazione della password</li></ul><p>Nei risultati del report verranno mostrati il numero di accesso consecutivi non riusciti eseguiti prima dell'accesso riuscito e un timestamp associato al primo accesso riuscito.</p><p><b>Impostazioni report</b>: è possibile configurare il numero minimo di tentativi di accesso non riusciti consecutivi eseguiti prima della visualizzazione del report. Quando si apportano modifiche a questa impostazione si noti che le modifiche non si applicano a eventuali accessi non riusciti attualmente visualizzati nel report. Verranno tuttavia applicati a tutti gli accessi successivi. Le modifiche a questo report possono essere apportate solo dagli amministratori autorizzati. | Scheda Directory > Report |

![Accessi dopo più errori](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=August15_HO8-->