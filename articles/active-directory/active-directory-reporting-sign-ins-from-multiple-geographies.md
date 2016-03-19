<properties
	pageTitle="Accessi da più aree geografiche"
	description="Report che segnala agli utenti situazioni in cui due accessi sembrano provenire da diverse aree, ma dato il tempo intercorso tra gli accessi non è possibile che l'utente si sia spostato da un'area all'altra."
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

# Accessi da più aree geografiche
<p>Questo report include accessi completati da un utente laddove due accessi sembrano provenire da diverse aree, ma dato il tempo intercorso tra gli accessi non è possibile che l'utente si sia spostato da un'area all'altra. Le possibili cause includono:</p><ul><li>L'utente sta condividendo la password con altri utenti</li><li>L'utente usa un desktop remoto per avviare un Web browser per l'accesso</li><li>Un pirata informatico ha eseguito l'accesso all'account di un utente da un altro paese.</li><li>L'utente sta usando una rete VPN o un proxy</li><li>L'utente ha eseguito l'accesso da più dispositivi allo stesso momento, ad esempio da un computer desktop e da un cellulare, e l'indirizzo IP del cellulare è insolito.</li></ul><p>Nei risultati del report verranno mostrati gli eventi di accesso con esito positivo, nonché il tempo tra gli accessi, le aree da cui sembrano provenire gli accessi e il tempo di spostamento stimato tra tali aree.</p><p>Il tempo di spostamento mostrato è solo una stima e può essere diverso da quello effettivo necessario per spostarsi tra le posizioni.</p>


![Accessi da più aree geografiche](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=Oct15_HO3-->