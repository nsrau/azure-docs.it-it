<properties
	pageTitle="Accessi da più aree geografiche"
	description="Un report che indica agli utenti laddove due accessi avere avuto luogo da diverse aree e l’intervallo tra i due accessi fa sì che sia impossibile che l'utente si sia spostato tra tali aree.""
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="ilanas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="kenhoff"/>

# Accessi da più aree geografiche

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report include attività di accesso completate da un utente laddove due accessi sembrano provenire da diverse aree, ma dato il tempo intercorso tra gli accessi non è possibile che l'utente si sia spostato da un'area all'altra. Le possibili cause includono:</p><ul><li>L'utente sta condividendo la password</li><li>L'utente usa un desktop remoto per avviare un Web browser per l'accesso</li><li>Un pirata informatico ha effettuato l'accesso all'account di un utente da un altro paese.</li></ul><p>Nei risultati del report verranno mostrati gli eventi di accesso riuscito, nonché il tempo tra gli accessi, le aree da cui sembrano provenire gli accessi e il tempo di spostamento stimato tra tali aree.</p><p>Il tempo di spostamento mostrato è solo una stima e può essere diverso da quello effettivo necessario per spostarsi tra le posizioni. Non viene inoltre generato alcun evento per gli accessi tra aree adiacenti.</p> | Scheda Directory > Report |

![Accessi da più aree geografiche](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=July15_HO5-->