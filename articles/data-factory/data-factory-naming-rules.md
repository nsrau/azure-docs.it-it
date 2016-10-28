<properties 
	pageTitle="Data factory - Regole di denominazione | Microsoft Azure" 
	description="Descrive le regole di denominazione per le entità di Data factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Data factory di Azure - Regole di denominazione 
La tabella seguente specifica le regole di denominazione per gli elementi di Data factory.



Nome | Univocità del nome | Controlli di convalida
:--- | :-------------- | :----------------
Data factory | Univoco in Microsoft Azure. Per i nomi non viene fatta distinzione tra maiuscole e minuscole; MyDF e mydf, ad esempio, fanno riferimento alla stessa data factory. |<ul><li>Ogni data factory è collegata a un'unica sottoscrizione di Azure.</li><li>I nomi di oggetto devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Nei nomi di contenitori non sono consentiti trattini consecutivi.</li><li>I nomi possono avere una lunghezza compresa tra 3 e 63 caratteri.</li></ul>
Servizi collegati/Tabelle/Pipeline | Univoco in una data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | <ul><li>Numero massimo di caratteri in un nome di tabella: 260.</li><li>I nomi di oggetto devono iniziare con una lettera, un numero o un carattere di sottolineatura (\_).</li><li>I caratteri seguenti non sono consentiti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\"</li></ul>
Gruppo di risorse | Univoco in Microsoft Azure. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | <ul><li>Numero massimo di caratteri: 1000.</li><li>l nome può contenere lettere, cifre e i caratteri seguenti: "-", "_", "," e ".".</li></ul>

<!---HONumber=AcomDC_0914_2016-->