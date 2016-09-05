<properties 
	pageTitle="Implementazione di Azure Mobile Engagement per app multimediali"
	description="Scenario di app multimediali per l'implementazione di Azure Mobile Engagement" 
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
	ms.date="08/19/2016"
	ms.author="piyushjo"/>  

#Implementare Mobile Engagement con app multimediali

## Overview

Mattia è un project manager impegnato in progetti per dispositivi mobili per una grande società di servizi multimediali. Ha recentemente lanciato una nuova app che sta registrando un numero molto elevato di download. Ha raggiunto i suoi obiettivi di download, ma il ritorno sugli investimenti (ROI) per utente non soddisfa ancora i requisiti stabiliti.

Mattia ha già scoperto perché il ritorno sugli investimenti è troppo basso. Molti degli utenti smettono di usare la sua app dopo sole due settimane e la maggior parte di essi non riprende più a usarla. Mattia vuole aumentare il tasso di conservazione della sua app.

Dopo alcuni test iniziali ha scoperto che quando incoraggia gli utenti con notifiche push, questi tendono a continuare a usare l'app. Anche gli utenti inattivi spesso riprendono a usare l'app in base alle notifiche ricevute. Mattia decide di investire in un programma di engagement per la sua app che usa il targeting avanzato con le notifiche push.

Ha letto di recente l'articolo [Azure Mobile Engagement - Guida introduttiva con procedure consigliate](mobile-engagement-getting-started-best-practices.md) e ha deciso di mettere in pratica le indicazioni della guida.

##Obiettivi e indicatori KPI

Le principali parti interessate si incontrano per discutere dell'app di Mattia. Il fatturato è generato dagli annunci pubblicitari visualizzati dagli utenti durante l'uso dei contenuti multimediali. Aumentando l'uso di contenuti per utente, Mattia potrà aumentare i ricavi. Tutti concordano su un obiettivo primario: aumentare del 25% le vendite da annunci pubblicitari. Vengono creati indicatori di prestazioni chiave aziendali per misurare e supportare questo obiettivo:

* Numero di annunci scelti per utente
* Numero di pagine di articoli visitate (per utente, per sessione, alla settimana, al mese)
* Categorie preferite

Sulla base della riunione tenuta con le principali parti interessate, Mattia ha definito i suoi KPI aziendali. Segue le indicazioni della parte 1 di [Azure Mobile Engagement - Guida introduttiva con procedure consigliate](mobile-engagement-getting-started-best-practices.md).

Successivamente, crea gli indicatori KPI di engagement seguenti per garantire il raggiungimento degli obiettivi:

* Monitoraggio della conservazione dell'app nei seguenti intervalli: giornaliero, settimanale, bisettimanale e mensile.
* Conteggio degli utenti attivi
* Classificazione dell'app negli store di app

In base ai suggerimenti del team IT, vengono aggiunti gli indicatori KPI tecnici riportati di seguito per rispondere alle domande seguenti:

* Percorso utente (pagina visitata e tempo dedicato alla pagina dall'utente)
* Numero di arresti anomali o bug rilevati per sessione
* Versione del sistema operativo degli utenti
* Dimensione media dello schermo degli utenti
* Tipo di connessione Internet degli utenti

Per ogni indicatore KPI, i dati necessari vengono classificati e registrati correttamente nello schema di progetto.

## Programma di engagement e integrazione

Dopo aver definito gli indicatori KPI, Mattia inizia la fase della strategia di engagement stabilendo quattro programmi di engagement e i relativi obiettivi: ![][1]

Mattia crea notifiche push dettagliate per ogni programma. Le notifiche push sono definite da cinque elementi:

1. Obiettivo: lo scopo della notifica
2. Come: il modo in cui verrà raggiunto l'obiettivo
3. Target: chi riceverà la notifica
4. Contenuto: il testo e il formato della notifica (interno o esterno all'app)
5. Quando: il momento migliore per l'invio della notifica push

	![][2]

Per altre informazioni, vedere la pagina relativa agli [schemi di progetto](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Seguendo la parte 2 del white paper, Mattia usa la sezione relativa al target per definire i dati da raccogliere e scrive il piano di tag insieme al team IT per implementare la soluzione. Dopo una settimana di implementazione e test di accettazione utente, Mattia è finalmente pronto per lanciare i suoi programmi.

##Risultati del programma

Quattro mesi dopo, Mattia verifica le prestazioni dei programmi. Il programma di benvenuto e il programma settimanale soddisfano gli obiettivi. Il numero di utenti con una sola sessione è diminuito, vengono usate più funzionalità dell'app e il numero di connessioni per settimana è raddoppiato.

Il **programma utenti inattivi** sta aiutando Mattia a comprendere le tendenze degli utenti. Sembra che il 15% degli utenti inattivi sia tornato a usare l'app. La maggior parte di essi, tuttavia, non rimane attiva per più di un mese. Mattia pensa di poter ottimizzare ulteriormente questa sequenza aggiungendo altre notifiche ed espandendo la scelta di contenuti.

Il **programma di scoperta** non sta funzionando. Aumenta il cross-selling ma non abbastanza da raggiungere gli obiettivi. Mattia scopre di non avere dati sufficienti per un targeting corretto e per proporre contenuti appropriati. Elimina questo programma e si concentra sull'invio di "notifiche push editoriali" con Azure Mobile Engagement. I suoi giornalisti hanno già una soluzione CMS per l'invio di notifiche push e non vogliono cambiarla.

Mattia decide di usare l'API Reach, un'API REST HTTP che permette di gestire le campagne di copertura senza dover usare l'interfaccia Web AZME. Con questo approccio Mattia può raccogliere i dati necessari e permettere ai giornalisti di continuare a usare la soluzione CMS.

Per garantire che funzioni correttamente, Mattia chiede al team IT di prestare attenzione a quanto segue:

1. **Sistemi operativi**: ogni sistema operativo ha le proprie regole per la gestione delle notifiche push. Mattia decide di fare un elenco di tutti i casi e controllare se sono gestiti dalle API. Ad esempio, il sistema push di Android permette di ottenere un quadro generale, al contrario di iOS.

2. **Intervallo di tempo**: Mattia ha bisogno di un'API per impostare l'intervallo di tempo e la fine delle campagne. Gli utenti non dovranno essere disturbati da un numero eccessivo di notifiche.

3. **Categorie**: il team di marketing prepara un modello per ogni tipo di avviso. Mattia chiede al team IT di impostare le categorie all'interno dell'API.

Dopo alcuni test, Mattia si ritiene soddisfatto. Grazie a questa API, i giornalisti possono ancora inviare notifiche push con la propria soluzione CMS e Azure Mobile Engagement raccoglie tutti i relativi dati comportamentali.

Dopo questi primi quattro mesi, i risultati riflettono buone prestazioni generali e rassicurano Mattia e il consiglio di amministrazione, il ROI per utente è aumentato del 15% e le vendite da dispositivi mobili rappresentano il 17,5% delle vendite totali. Un aumento del 7,5% in soli quattro mesi.

<!--Image references-->  
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->  
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

<!---HONumber=AcomDC_0824_2016-->