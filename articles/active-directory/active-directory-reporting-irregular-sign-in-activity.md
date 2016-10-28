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
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Attività di accesso irregolare

Gli accessi irregolari sono quelli individuati dagli algoritmi di Machine Learning in base a una condizione di tipo "tempo di spostamento impossibile" combinata con una posizione e un dispositivo di accesso anomali. È possibile che un pirata informatico abbia eseguito l'accesso con questo account. Microsoft invierà una notifica tramite posta elettronica agli amministratori globali se si verificano 10 o più eventi di accesso anomalo in un intervallo di 30 giorni o meno. Assicurarsi di includere aad-alerts-noreply@mail.windowsazure.com nell'elenco dei mittenti attendibili.

<!---HONumber=AcomDC_0309_2016-->