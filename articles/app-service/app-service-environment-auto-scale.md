<properties 
	pageTitle="Scalabilità automatica e ambiente del servizio app" 
	description="Scalabilità automatica e ambiente del servizio app" 
	services="app-service"
	documentationCenter=""
	authors="btardif" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2016" 
	ms.author="byvinyal"
/>
	
#Scalabilità automatica e ambiente del servizio app

##Introduzione
Gli **ambienti del servizio app** supportano la scalabilità automatica, che consente di ridimensionare automaticamente singoli pool di lavoro in base a una metrica o a una pianificazione.
 
![][intro]
 
La scalabilità automatica consente di ottimizzare l'utilizzo delle risorse aumentando o riducendo automaticamente le risorse di un **ambiente del servizio app** in base al budget o al profilo di carico.

##Configurazione della scalabilità automatica del pool di lavoro 
È possibile accedere alla funzionalità Scalabilità automatica dalla scheda Impostazioni del **pool di lavoro**.
 
![][settings-scale]

L'interfaccia utente risulterà familiare, dal momento che si tratta della stessa procedura usata per il ridimensionamento di un **piano di servizio app**. Sarà possibile immettere un valore per il piano manualmente
 
![][scale-manual]
 
Oppure configurare un profilo di scalabilità automatica
 
![][scale-profile]
 
I profili di scalabilità automatica consentono di impostare i limiti per il piano. Questo consente di ottenere prestazioni coerenti impostando un valore di limite minimo per il piano (1) e un tetto di spesa prevedibile impostando un limite massimo (2).
 
![][scale-profile2]
 
Una volta definito il profilo, è possibile aggiungere regole di scalabilità automatica basate su metrica per aumentare o ridurre il numero di istanze nel **pool di lavoro** entro i limiti definiti nel profilo.

![][scale-rule]

 Per definire le regole di scalabilità automatica, è possibile usare qualsiasi metrica del **pool di lavoro** o del **front-end**. Si tratta delle stesse metriche che è possibile monitorare nei grafici del pannello delle risorse o per cui si possono impostare avvisi.
 
##Esempio di scalabilità automatica
Per illustrare la scalabilità automatica di un **ambiente del servizio app** verrà usato uno scenario con procedure dettagliate. Questo articolo descrive in modo dettagliato tutte le considerazioni necessarie per configurare la scalabilità automatica e le interazioni che entrano in gioco quando si configura la scalabilità automatica per gli **ambienti del servizio app** ospitati in un ambiente del servizio app.

###Introduzione dello scenario
Diego è amministratore del sistema presso una società e ha eseguito la migrazione di una parte dei carichi di lavoro che gestisce in un **ambiente del servizio app**.

L'**ambiente del servizio app** è configurato per la scalabilità manuale come segue:
* Front-end: 3
* Pool di lavoro 1: 10
* Pool di lavoro 2: 5
* Pool di lavoro 3: 5

Il **pool di lavoro 1** viene usato per i carichi di lavoro di produzione, mentre il **pool di lavoro 2** e il **pool di lavoro 3** sono usati per il controllo qualità e i carichi di lavoro di sviluppo.

I **piani di servizio app** usati per il controllo qualità e lo sviluppo sono configurati per il **ridimensionamento manuale** ma il **piano di servizio app** di produzione è impostato per la **scalabilità automatica** in modo da gestire le variazioni in termini di carico e traffico.

Diego ha molta familiarità con l'applicazione e sa che i picchi di carico si verificano tra le 9.00 e le 18.00, dal momento che si tratta di un'**applicazione line-of-business** usata dai dipendenti durante l'orario di ufficio. Al termine della giornata lavorativa degli utenti, l'utilizzo cala. Si registra ancora un certo carico dal momento che gli utenti possono accedervi in modalità remota tramite i propri dispositivi mobili o i computer di casa. Il **piano di servizio app** è già configurato per la **scalabilità automatica** in base all'utilizzo della CPU con le regole seguenti:

![][asp-scale]

|	**Profilo di scalabilità automatica – Giorni della settimana – Piano di servizio app** |	**Profilo di scalabilità automatica – Fine settimana – Piano di servizio app** |
|	----------------------------------------------------	|	----------------------------------------------------	|
|	**Nome:** profilo giorno della settimana |	**Nome:** profilo fine settimana |
|	**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |	**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
|	**Profilo:** giorni della settimana |	**Profilo:** fine settimana |
|	**Tipo:** ricorrenza |	**Tipo:** ricorrenza |
|	**Intervallo di destinazione:** da 5 a 20 istanze |	**Intervallo di destinazione:** da 3 a 10 istanze |
|	**Giorni:** Lunedì, Martedì, Mercoledì, Giovedì, Venerdì |	**Giorni:** Sabato, Domenica |
|	**Ora di inizio:** 9.00 |	**Ora di inizio:** 9.00 |
|	**Fuso orario:** UTC – 08 |	**Fuso orario:** UTC – 08 |
| | |
|	**Regola di scalabilità (aumento)** |	**Regola di scalabilità (aumento)** |
|	**Risorsa:** produzione (Ambiente del servizio app) |	**Risorsa:** produzione (Ambiente del servizio app) |
|	**Metrica:** % CPU |	**Metrica:** % CPU |
|	**Operazione:** maggiore del 60% |	**Operazione:** maggiore del 80% |
|	**Durata:** 5 minuti |	**Durata:** 10 minuti |
|	**Aggregazione temporale:** media |	**Aggregazione temporale:** media |
|	**Azione:** aumenta numero di 2 |	**Azione:** aumenta numero di 1 |
|	**Disattiva regole dopo (minuti):** 15 |	**Disattiva regole dopo (minuti):** 20 |
| | |
|	**Regola di scalabilità (riduzione)** |	**Regola di scalabilità (riduzione)** |
|	**Risorsa:** produzione (Ambiente del servizio app) |	**Risorsa:** produzione (Ambiente del servizio app) |
|	**Metrica:** % CPU |	**Metrica:** % CPU |
|	**Operazione:** maggiore del 30% |	**Operazione:** maggiore del 20% |
|	**Durata:** 10 minuti |	**Durata:** 15 minuti |
|	**Aggregazione temporale:** media |	**Aggregazione temporale:** media |
|	**Azione:** riduci numero di 1 |	**Azione:** riduci numero di 1 |
|	**Disattiva regole dopo (minuti):** 20 |	**Disattiva regole dopo (minuti):** 10 |

###Tasso di inflazione del piano di servizio app
I **piani di servizio app** configurati per la scalabilità automatica verranno ridimensionati automaticamente al tasso massimo all'ora. Questo tasso può essere calcolato in base ai valori specificati nella regola di scalabilità automatica.

È importante comprendere e calcolare il **tasso di inflazione del piano di servizio app** per il ridimensionamento automatico del **pool di lavoro** **dell'ambiente del servizio app** in quanto l'operazione di ridimensionamento del **pool di lavoro** non è istantanea e richiede un certo tempo per diventare effettiva.

Il tasso di inflazione dell'**ambiente del servizio app** viene calcolato come segue:

![][ASP-Inflation]

In base alla regola *Scalabilità automatica - Aumento* per il profilo *Giorno della settimana* del **piano di servizio app** di produzione la formula sarà la seguente:

![][Equation1]

Nel caso della regola *Scalabilità automatica - Aumento* per il profilo *Fine settimana* del **piano di servizio app** di produzione la formula sarà la seguente:

![][Equation2]

Questo valore può anche essere calcolato per le operazioni di riduzione:

In base alla regola *Scalabilità automatica - Riduzione* per il profilo *Giorno della settimana* del **piano di servizio app** di produzione la formula sarà la seguente:

![][Equation3]

Nel caso della regola *Scalabilità automatica - Riduzione* per il profilo *Fine settimana* del **piano di servizio app** di produzione la formula sarà la seguente:

![][Equation4]

Questo significa che il **piano di servizio app** può aumentare al tasso massimo di **8** istanze all'ora durante la settimana e di **4** istanze all'ora durante il fine settimana e che può rilasciare le istanze al tasso massimo di **4** istanze all'ora durante la settimana e di **6** istanze all'ora durante il fine settimana.

Se in un **pool di lavoro** sono ospitati più **piani di servizio app**, è necessario calcolare il **tasso di inflazione totale** che sarà espresso come la *somma* dei tassi di inflazione per tutti i **piani di servizio app** ospitati in quel **pool di lavoro**.

![][ASP-Total-Inflation]

###Uso del tasso di inflazione del piano di servizio app per definire le regole di scalabilità automatica del pool di lavoro
I **pool di lavoro** che ospitano **piani di servizio app** configurati per la scalabilità automatica dovranno allocare un buffer di capacità per consentire alle operazioni di ridimensionamento automatico di aumentare/ridurre il **piano di servizio app** in base alle esigenze. Il buffer minimo sarà costituito dal **tasso di inflazione del piano di servizio app totale** calcolato.

Poiché l'applicazione delle operazioni di ridimensionamento dell'**ambiente del servizio app** richiede un certo tempo, qualsiasi modifica deve tenere conto delle ulteriori variazioni della domanda che possono verificarsi mentre è in corso un'operazione di ridimensionamento. Per questo motivo è consigliabile usare il **tasso di inflazione del piano di servizio app totale** calcolato come numero minimo di istanze aggiunte per ogni operazione di ridimensionamento automatico.

Con queste informazioni, Diego può definire le regole e il profilo di scalabilità automatica seguenti:

![][Worker-Pool-Scale]

|	**Profilo di scalabilità automatica – Giorni della settimana** |	**Profilo di scalabilità automatica – Fine settimana** |
|	----------------------------------------------------	|	--------------------------------------------	|
|	**Nome:** profilo giorno della settimana |	**Nome:** profilo fine settimana |
|	**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |	**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
|	**Profilo:** giorni della settimana |	**Profilo:** fine settimana |
|	**Tipo:** ricorrenza |	**Tipo:** ricorrenza |
|	**Intervallo di destinazione:** da 13 a 25 istanze |	**Intervallo di destinazione:** da 6 a 15 istanze |
|	**Giorni:** Lunedì, Martedì, Mercoledì, Giovedì, Venerdì |	**Giorni:** Sabato, Domenica |
|	**Ora di inizio:** 7.00 |	**Ora di inizio:** 9.00 |
|	**Fuso orario:** UTC – 08 |	**Fuso orario:** UTC – 08 |
| | |
|	**Regola di scalabilità (aumento)** |	**Regola di scalabilità (aumento)** |
|	**Risorsa:** pool di lavoro 1 |	**Risorsa:** pool di lavoro 1 |
|	**Metrica:** Pool di lavoro disponibili |	**Metrica:** Pool di lavoro disponibili |
|	**Operazione:** minore di 8 |	**Operazione:** minore di 3 |
|	**Durata:** 20 minuti |	**Durata:** 30 minuti |
|	**Aggregazione temporale:** media |	**Aggregazione temporale:** media |
|	**Azione:** aumenta numero di 8 |	**Azione:** aumenta numero di 3 |
|	**Disattiva regole dopo (minuti):** 180 |	**Disattiva regole dopo (minuti):** 180 |
| | |
|	**Regola di scalabilità (riduzione)** |	**Regola di scalabilità (riduzione)** |
|	**Risorsa:** pool di lavoro 1 |	**Risorsa:** pool di lavoro 1 |
|	**Metrica:** Pool di lavoro disponibili |	**Metrica:** Pool di lavoro disponibili |
|	**Operazione:** maggiore di 8 |	**Operazione:** maggiore di 3 |
|	**Durata:** 20 minuti |	**Durata:** 15 minuti |
|	**Aggregazione temporale:** media |	**Aggregazione temporale:** media |
|	**Azione:** riduci numero di 2 |	**Azione:** riduci numero di 3 |
|	**Disattiva regole dopo (minuti):** 120 |	**Disattiva regole dopo (minuti):** 120 |

L'intervallo di destinazione definito nel profilo è calcolato in base al numero minimo di istanze definito nel profilo per il **piano di servizio app** più il buffer.

L'intervallo massimo corrisponde alla somma di tutti gli intervalli massimi per tutti i **piani di servizio app** ospitati nel **pool di lavoro**.

L'aumento del numero per le regole di aumento deve essere impostato su un valore pari ad almeno 1 volta il **tasso di inflazione per il piano di servizio app** per l'operazione di aumento.

L'opzione Riduci numero di può essere impostata su un valore compreso tra la metà o una volta il **tasso di inflazione del piano di servizio app** per l'operazione di riduzione.

###Scalabilità automatica per il pool front-end
Le regole di scalabilità automatica per il pool **front-end** sono più semplici di quelle per i **pool di lavoro** ed è principalmente necessario assicurarsi che la durata della misurazione e i timer di disattivazione regole tengano conto del fatto che le operazioni di ridimensionamento in un **piano di servizio app** non sono istantanee.

Per questo scenario, Diego sa che la percentuale di errore aumenta quando i pool front-end raggiungono un utilizzo di CPU dell'80% e di conseguenza imposta la regola di scalabilità automatica in modo da aumentare le istanze come descritto di seguito:
 
![][Front-End-Scale]
  
|	**Profilo di scalabilità automatica – Front-end** |
|	--------------------------------------------	|
|	**Nome:** scalabilità automatica – Front-end |
|	**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
|	**Profilo:** ogni giorno |
|	**Tipo:** ricorrenza |
|	**Intervallo di destinazione:** da 3 a 10 istanze |
|	**Giorni:** ogni giorno |
|	**Ora di inizio:** 9.00 |
|	**Fuso orario:** UTC – 08 |
| |
|	**Regola di scalabilità (aumento)** |
|	**Risorsa:** pool front-end |
|	**Metrica:** % CPU |
|	**Operazione:** maggiore del 60% |
|	**Durata:** 20 minuti |
|	**Aggregazione temporale:** media |
|	**Azione:** aumenta numero di 3 |
|	**Disattiva regole dopo (minuti):** 120 |
| |
|	**Regola di scalabilità (riduzione)** |
|	**Risorsa:** pool di lavoro 1 |
|	**Metrica:** % CPU |
|	**Operazione:** maggiore del 30% |
|	**Durata:** 20 minuti |
|	**Aggregazione temporale:** media |
|	**Azione:** riduci numero di 3 |
|	**Disattiva regole dopo (minuti):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!---HONumber=AcomDC_0518_2016-->