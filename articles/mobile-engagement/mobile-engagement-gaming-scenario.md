	<properties 
	pageTitle="Azure Mobile Engagement implementation for Gaming App"
	description="Gaming app scenario to implement Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="11/23/2015"
	ms.author="piyushjo"/>

#Implementare Mobile Engagement con app di gioco

## Panoramica

Una startup del settore gaming ha lanciato una nuova app di gioco di ruolo e strategia ispirata al mondo della pesca. Il gioco è operativo da sei mesi. Sta registrando un grande successo, con milioni di download, e il tasso di conservazione è molto alto rispetto alle app di gioco di altre startup. Durante la riunione di revisione trimestrale, le parti interessate concordano sulla necessità di aumentare i ricavi medi per utente (ARPU). All'interno del gioco sono disponibili pacchetti Premium come offerte speciali. Questi pacchetti permettono agli utenti di migliorare l'aspetto e le prestazioni delle lenze, delle esche o dell'attrezzatura da pesca all'interno del gioco. Le vendite di pacchetti, però, sono molto basse. Si decide quindi di analizzare prima di tutto l'esperienza degli utenti con uno strumento di analisi e di sviluppare poi un programma di engagement per aumentare le vendite attraverso la segmentazione avanzata.

Sulla base dell'articolo [Azure Mobile Engagement - Guida introduttiva con procedure consigliate](mobile-engagement-getting-started-best-practices.md), viene costruita una strategia di engagement.

##Obiettivi e indicatori KPI

Le principali parti interessate dal gioco si incontrano. Tutti concordano su un obiettivo primario: aumentare del 15% le vendite di pacchetti Premium. Vengono creati indicatori di prestazioni chiave aziendali per misurare e supportare questo obiettivo:

* Livello di gioco in cui vengono acquistati i pacchetti
* Definizione dei ricavi per utente, per sessione, alla settimana e al mese
* Tipi di acquisto preferiti

La parte 1 della [Guida introduttiva](mobile-engagement-getting-started-best-practices.md) illustra come definire gli obiettivi e gli indicatori KPI.

Con gli indicatori KPI aziendali appena definiti, il Mobile Product Manager crea gli indicatori KPI di engagement per determinare le tendenze dei nuovi utenti e il tasso di conservazione.

* Monitoraggio del tasso di conservazione e dell'uso nei seguenti intervalli: ogni giorno, ogni 2 giorni, ogni settimana, ogni mese e ogni 3 mesi
* Conteggio degli utenti attivi
* Classificazione dell'app nello store

In base ai suggerimenti del team IT, vengono aggiunti gli indicatori KPI tecnici riportati di seguito per rispondere alle domande seguenti:

* Percorso utente (pagina visitata e tempo dedicato alla pagina dall'utente)
* Numero di arresti anomali o bug rilevati per sessione
* Versione del sistema operativo degli utenti
* Dimensione media dello schermo degli utenti
* Tipo di connessione Internet degli utenti

Per ogni indicatore KPI, il Mobile Product Manager specifica i dati necessari e la loro posizione nello schema di progetto.

## Programma di engagement e integrazione

Prima di creare un programma di engagement avanzato, il Mobile Project Manager responsabile del progetto dovrebbe conoscere a fondo le modalità e i tempi di utilizzo dei prodotti da parte degli utenti.

Dopo 3 mesi, il Mobile Project Manager ha raccolto dati sufficienti per migliorare le vendite delle notifiche push all'interno dell'app. Informazioni raccolte:

* Il primo acquisto si verifica in genere al livello 14 del gioco. Nel 90% dei casi, vengono acquistate nuove armi leggendarie da 3 dollari.
* Nell'80% dei casi, gli utenti che hanno fatto un acquisto continuano a usare il prodotto e fanno altri acquisti.
* Gli utenti che hanno superato il livello 20 iniziano a spendere oltre 10 dollari alla settimana.
* Gli utenti tendono ad acquistare pacchetti Premium ai livelli 16, 24 e 32.

Grazie a questa analisi il Mobile Project Manager decide di creare sequenze di notifiche push specifiche per aumentare le vendite all'interno dell'app. Crea tre sequenze push denominate: programma di benvenuto, programma di vendita e programma utenti inattivi. Per altre informazioni, vedere la pagina relativa agli [schemi di progetto](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks). ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

<!---HONumber=AcomDC_1203_2015-->