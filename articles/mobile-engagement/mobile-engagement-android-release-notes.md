<properties 
	pageTitle="Integrazione di Android SDK per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />


#Note sulla versione

##4\.0.0 (06/07/2015)

-   Modifiche al protocollo interno per rendere più affidabili le analisi e il push.
-   Il push nativo (GCM/ADM) viene ora usato anche per le notifiche in-app. È quindi necessario configurare le credenziali del push nativo per qualsiasi tipo di campagna push.
-   Correzione della notifica generale: elementi visualizzati solo 10 secondi dopo il push.
-   Correzione della selezione di un collegamento in un annuncio Web che include un URL di azione predefinito.
-   Correzione di un arresto anomalo raro correlato alla gestione dell'archivio locale.
-   Correzione della gestione delle stringhe di configurazione dinamiche.
-   Aggiornamento del contratto di licenza.

##3\.0.0 (17/02/2015)

-   Versione iniziale di Azure Mobile Engagement
-   La configurazione di appId è stata sostituita con la configurazione della stringa di connessione.
-   L'API non invia e non riceve più messaggi XMPP da entità XMPP arbitrarie.
-   L'API non riceve e non invia più messaggi tra i dispositivi.
-   Sono stati introdotti miglioramenti per la sicurezza.
-   È stata rimossa la verifica per Google Play e SmartAd.

 

<!---HONumber=August15_HO7-->