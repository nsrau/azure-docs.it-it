<properties 
 pageTitle="Interfaccia utente di Azure Mobile Engagement - Analytics" 
 description="Panoramica dell'interfaccia utente di Azure Mobile Engagement" 
 services="mobile-engagement" 
 documentationCenter="mobile" 
 authors="v-micada" 
 manager="mattgre" 
 editor=""/>

<tags
 ms.service="mobile-engagement"
 ms.devlang="Java"
 ms.topic="article"
 ms.tgt_pltfrm="mobile"
 ms.workload="required" 
 ms.date="02/17/2015"
 ms.author="v-micada"/>

# Azure Mobile Engagement - Interfaccia utente

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introduzione</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigazione</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Home</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Account personale</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analytics</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitoraggio</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Copertura</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmenti</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Impostazioni</a>
</div>

# Analytics
 
La sezione Analytics dell'interfaccia utente fornisce informazioni aggregate sull'applicazione, tratte dai dati cronologici che vengono aggiornati ogni 24 ore. Le informazioni vengono visualizzate in dashboard diversi, composti da grafici a linee/barre/torta, griglie e mappe. I dati possono anche essere scaricati come file con estensione csv. La maggior parte di queste informazioni è disponibile in tempo reale nella sezione Monitoraggio dell'interfaccia utente. Inoltre, è possibile accedervi dall'API Analytics. Il "Glossario" nella sezione "Informazioni" riporta le definizioni dei termini e le abbreviazioni presenti in Analisi e Monitoraggio, comprese le seguenti: Utente attivo, nuovo utente, utente conservato, sessione, grafico del percorso utente, mappa utenti, rilevamento URL, tendenze, attività, evento, processo, errore, informazioni supplementari, arresto anomalo e informazioni sull'app.

**Vedere anche:** 

- [Concetti - Glossario][Link 6], [Guida alla risoluzione dei problemi - Analytics][Link 2]

## Analisi standard e personalizzate:
Azure Mobile Engagement offre un set di informazioni analitiche standard di base relative all'applicazione che possono essere rappresentate in un grafico, non appena si integra l'app con l'SDK. Azure Mobile Engagement offre anche la possibilità di riunire ulteriori informazioni analitiche personalizzate relative al comportamento degli utenti finali. È possibile effettuare questa operazione creando una pianificazione di "tag sulle informazioni dell'app" personalizzata da "Impostazioni". In questo modo, Azure Mobile Engagement può raccogliere tali dati aggiuntivi per conto dell'utente.

**Vedere anche:** 
- [Documentazione sull'interfaccia utente - Impostazioni][Link 20]
 
## Intestazione di Analisi:
- Nome elemento: vengono elencati gli elementi in fase di conteggio
- Mostra Guida: vengono visualizzate informazioni di contesto su questa sezione
- Versioni: consente di visualizzare diverse informazioni analitiche per ogni versione dell'applicazione o per tutte le versioni. Nota: se si filtrano i dati di analisi nell'interfaccia utente, è possibile visualizzare tutti gli esempi di questo tipo, a prescindere dalla versione dell'app. Ad esempio, verranno visualizzati gli "arresti anomali" filtrati in base al nome per le versioni 1 e 2 dell'app.
- Periodo: ultimi 7 giorni, ultimi 30 giorni, sempre, personalizzato
- Frequenza: ogni ora, giorno, settimana, mese
- Visualizza: grafico a linee, griglia, opzioni Invia a dashboard e Scarica file CSV
 
![Analytics1][10] 

## Analytics:
- Dashboard: vengono visualizzate informazioni generali sui nuovi utenti, su quelli attivi e sulle relative tendenze.
- Utenti: gli utenti vengono identificati tramite l'identificatore del dispositivo. Tale valore è univoco per ogni dispositivo (a un nuovo utente corrisponde un nuovo dispositivo). Un utente viene considerato come nuovo in un determinato intervallo di tempo, se ha eseguito la prima sessione durante questo intervallo di tempo. Un utente viene considerato come conservato, se ha eseguito almeno una sessione negli ultimi 7 giorni. Gli utenti attivi sono quelli che hanno effettuato almeno una sessione durante un determinato periodo. È possibile ordinarli per mese, settimana, giorno oppure ora. Tutti i grafici hanno un aspetto simile, ma consentono di filtrare gli elementi in base a funzionalità differenti (ad esempio, la versione dell'applicazione) e successivamente in base all'intervallo di tempo. Tra le informazioni standard raccolte integrando l'SDK sono incluse le seguenti: utenti attivi, nuovo utente, numero di sessioni, durata di ogni sessione, informazioni tecniche sul paese, impostazioni locali, località, carrier della lingua, dispositivi, firmware, rete (Wi-Fi), versioni dell'app e dell'SDK, utilizzato dai clienti. È possibile visualizzare tali informazioni in tempo reale dalla sezione di monitoraggio. 
- Il rilevamento in base all'origine consente di visualizzare il numero di nuovi utenti che hanno scaricato l'applicazione come risultato di una campagna promozionale. Gli utenti vengono identificati tramite l'identificatore del dispositivo. Tale valore è univoco per ogni dispositivo (a un nuovo utente corrisponde un nuovo dispositivo). Un utente viene considerato come nuovo in un determinato intervallo di tempo, se ha eseguito la prima sessione durante questo intervallo di tempo.
- Il rilevamento in base allo store consente di visualizzare il numero di nuovi utenti che hanno scaricato l'applicazione da un determinato store. Gli utenti vengono identificati tramite l'identificatore del dispositivo. Tale valore è univoco per ogni dispositivo (a un nuovo utente corrisponde un nuovo dispositivo). Un utente viene considerato come nuovo in un determinato intervallo di tempo, se ha eseguito la prima sessione durante questo intervallo di tempo.

> Nota: il periodo si basa sulla data presente nelle impostazioni del dispositivo dell'utente. Pertanto, se nel telefono di un utente la data è impostata in modo errato, è possibile che venga visualizzato il periodo errato.

- Conservazione: un utente viene considerato come conservato in un determinato intervallo di tempo, se ha eseguito la prima sessione durante questo intervallo di tempo. È possibile modificare gli intervalli temporali durante i quali gli utenti conservati(e quelli nuovi) vengono conteggiati su base oraria, giornaliera, settimanale o mensile. Le analisi relative alla conservazione degli utenti vengono realizzate a partire dalle coorti. Si definisce coorte il set di tutti i nuovi utenti rilevati in un determinato periodo di tempo (ad esempio, il set di utenti che eseguono la prima sessione durante questo periodo). Vengono utilizzate coorti di 1 giorno, 2 giorni, 4 giorni, 7 giorni o 1 mese. Data una coorte, ogni giorno, ogni 2 giorni, ogni 4 giorni, ogni 7 giorni oppure ogni mese, Azure Mobile Engagement calcola il set di tutti gli utenti che ne fanno parte e che sono ancora attivi (vale a dire, gli utenti che hanno eseguito almeno una sessione durante questo periodo). Questo set di utenti viene definito come versione coorte. È possibile che Azure Mobile Engagement visualizzi il numero di utenti che ancora utilizzano l'app, ma soltanto lo store di una piattaforma specifica riesce a offrire informazioni sul numero di utenti che hanno disinstallato l'app (ad esempio, Google Play, iTunes, Windows Store e così via). 
- Sessioni: un utilizzo dell'applicazione da parte di un utente. Le sessioni vengono create dalla sequenza di attività eseguite dall'utente. Solitamente, un'attività viene associata all'utilizzo di una schermata dell'applicazione. Tuttavia, può variare in base al modo in cui l'SDK è stato integrato nell'applicazione. Un utente può eseguire soltanto un'attività per volta: una sessione inizia appena l'utente avvia la prima attività e si interrompe quando conclude l'ultima attività. Se l'utente non esegue attività per alcuni secondi, la sequenza viene suddivisa in due sessioni distinte.
- Attività: i nomi di ogni schermata dell'applicazione e il tempo trascorso dall'utente su ogni schermata. Le attività rappresentano un'opzione di analisi personalizzata che corrisponde ai tag "informazioni sull'app" impostati per l'app.
- Percorso utente: consente di visualizzare in che modo gli utenti navigano tra le attività (schermate) dell'applicazione. È possibile spostare il dispositivo di scorrimento per regolare il livello dei dettagli. I nodi blu rappresentano le attività dell'applicazione. La loro dimensione è proporzionale al tempo che gli utenti trascorrono nell'app. I nodi bianchi rappresentano l'inizio e la fine della sessione. I nodi rossi rappresentano gli arresti anomali. I collegamenti rappresentano i passaggi tra attività dell'applicazione (o tra attività e arresti anomali). Selezionare un nodo o un collegamento per visualizzare una descrizione comando con ulteriori informazioni sui dati: il tempo trascorso su una determinata schermata, il conteggio dei passaggi, la percentuali di passaggi dall'attività originale a quella di destinazione. A ---60% ---> B indica che, nel 60% dei casi, gli utenti dell'attività A passano a quella B. È possibile riorganizzare il grafico come si desidera per renderlo più chiaro. La sua posizione viene salvata ogni volta che si apporta una modifica. È possibile visualizzare o nascondere gli arresti anomali al fine di evidenziare il grafico.
- Eventi: azioni specifiche intraprese da un utente nell'applicazione. La distribuzione degli eventi viene visualizzata come numero di eventi per utente a ogni sessione. Un evento rappresenta un'azione immediata, ad esempio, la selezione di un pulsante o la ricezione di una notifica. Il significato degli eventi dipende dal modo in cui l'SDK è stato integrato nell'applicazione. Un evento può verificarsi durante una sessione o un processo oppure come evento autonomo.
- Processi: simili agli eventi, tranne per il fatto che si concentrano sulla durata dell'azione. Ad esempio, i processi forniscono informazioni tecniche sulla durata del caricamento o di un contenuto o della chiamata di un servizio Web. Inoltre, consentono di visualizzare il tempo impiegato da un utente a completare un modulo, creare un account o effettuare un acquisto. Un processo rappresenta la durata di un'attività, ad esempio, la durata di un'attività di download o il tempo per il quale un banner viene visualizzato sullo schermo. Il significato dei processi dipende dal modo in cui l'SDK è stato integrato nell'applicazione. Generalmente, i processi sono associati ad attività di background eseguite al di fuori dell'ambito di una sessione (vale a dire, senza alcuna attività utente).
- Informazioni tecniche: informazioni tecniche sui dispositivi degli utenti dell'app di cui è possibile tenere traccia, ad esempio, le impostazioni locali, il carrier, la rete, il dispositivo, il firmware e la dimensione del dispositivo dell'utente, la versione dell'app e dell'SDK utilizzate.
- Errori: informazioni sugli errori tecnici all'interno dell'applicazione che non causano arresti anomali. Un errore rappresenta un problema immediato, ad esempio, un errore di rete o una modifica non valida. Il significato degli eventi dipende dal modo in cui l'SDK è stato integrato nell'applicazione. Un errore può verificarsi durante una sessione o un processo oppure come evento autonomo.
- Arresti anomali: informazioni sugli errori che causano l'arresto anomalo dell'applicazione. Un arresto anomalo consiste in una situazione imprevista durante la quale l'applicazione smette di eseguire le funzioni previste e deve essere interrotta. Di solito, un arresto anomalo è causato da un bug dell'applicazione.
 
![Analytics2][11] 

## Accesso alla schermata Panoramica di conservazione
 
![Analytics3][12] 

La parte centrale della schermata Panoramica di conservazione è suddivisa in diverse schede, ciascuna delle quali riporta una panoramica su un determinato periodo di conservazione. Nell'esempio viene visualizzato il periodo di conservazione di due giorni. Nelle altre schede vengono visualizzati i periodi di conservazione di 4 e 7 giorni. 

## Informazioni sulle schede della schermata Panoramica di conservazione
 
![Analytics4][13] 

Ogni scheda comprende tre parti principali:

1. 1: coorte e periodo considerato
2. 2-4: conservazione per il periodo corrente
3. 5: grafico sparkline della cronologia

**Di seguito, sono riportate le informazioni dettagliate su ogni elemento:**

1. Coorte e periodo: l'intestazione fornisce informazioni sul tipo di coorte. Qui, "Periodo di 2 giorni" indica che sarà esaminato il comportamento degli utenti per 2 giorni, di quelli che hanno iniziato a utilizzare l'app nei 2 giorni e si stabilisce se tali utenti si connettono nel periodo di 2 giorni. Nell'esempio riportato in alto, vengono considerate le attività degli utenti tra il 21 e il 22 novembre.
2. In questo modo, è possibile visualizzare il tasso di conservazione del 21 e 22 novembre, relativo agli utenti che hanno iniziato a utilizzare l'app il 19 e 20 novembre. In questo esempio, sono presenti 3 nuovi utenti tra il 19 e il 20; di questi, solo 1 attivo tra il 21 e il 22 novembre.
3. L'indicatore visivo fornisce le stesse informazioni rappresentate graficamente. Un terzo del cerchio rappresenta il 33%. Il colore fornisce ulteriori indicazioni: il verde indica che il numero è in aumento rispetto al calcolo precedente. Il giallo indica una situazione stabile, mentre il rosso rappresenta una riduzione.
4. Indica i valori utilizzati per il calcolo.
5. Si tratta di un grafico sparkline relativo alla cronologia dei valori di conservazione. Consente di visualizzare i valori del passato per avere una visione più ampia sull'evoluzione.

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
