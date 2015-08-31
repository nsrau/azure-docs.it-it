<properties
	pageTitle="Attività di accesso irregolare"
	description="Questo report include accessi identificati come anomali dagli algoritmi di Machine Learning."
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

# Attività di accesso irregolare

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report include accessi identificati come "anomali" dagli algoritmi di apprendimento automatico. I motivi per contrassegnare un tentativo di accesso come irregolare prevedono posizioni di accesso imprevisto, ora del giorno e posizioni o una combinazione di queste. È possibile che un pirata informatico abbia tentato di accedere con questo account. L'algoritmo di apprendimento automatico classifica gli eventi come "anomali" o "sospetti", laddove "sospetti" indica una maggiore probabilità di una violazione della sicurezza.</p><p>Nei Risultati del report verranno mostrati questi accessi, insieme alla classificazione, alla posizione e a un timestamp associato a ogni accesso.</p><p>Se in un periodo di 30 giorni o meno verranno rilevati 10 o più eventi di accesso anomalo, Microsoft invierà agli amministratori globali una notifica tramite posta elettronica. Assicurarsi di includere aad-alerts-noreply@mail.windowsazure.com nell'elenco dei mittenti attendibili.</p> | Scheda Directory > Report |

<!---HONumber=August15_HO8-->