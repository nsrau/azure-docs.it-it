<properties
	pageTitle="Azure Insights: procedure consigliate per la scalabilità automatica di Azure Insights | Microsoft Azure"
	description="Principi per usare in modo efficace la scalabilità automatica in Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="ashwink"/>

# Procedure consigliate per la scalabilità automatica in Azure Insights

Le sezioni seguenti di questo documento illustrano le procedure consigliate per la scalabilità automatica in Azure Insights. Dopo avere letto queste informazioni, sarà possibile usare in modo più efficace la scalabilità automatica nell'infrastruttura di Azure.

## Concetti di scalabilità automatica

- Una risorsa può avere *una* sola impostazione di scalabilità automatica.
- Un'impostazione di scalabilità automatica può avere uno o più profili e ogni profilo può avere una o più regole di scalabilità automatica.
- Un'impostazione di scalabilità automatica scala le istanze orizzontalmente, ovvero *aumenta* e *riduce* il numero delle istanze. Un'impostazione di scalabilità automatica ha un valore massimo, uno minimo e uno predefinito di istanze.
- Un processo di scalabilità automatica legge sempre la metrica associata alla scala controllando se ha superato la soglia configurata per l'aumento o la riduzione del numero di istanze. Per un elenco delle metriche in base alle quali può essere applicata la scalabilità automatica, vedere [Metriche comuni per la scalabilità automatica di Azure Insights](insights-autoscale-common-metrics.md).
- Tutte le soglie vengono calcolate a livello di istanza. Ad esempio, "aumentare il numero di istanze di 1 quando la media CPU > 80% quando il conteggio delle istanze è 2", significa aumentare il numero di istanze quando la media CPU in tutte le istanze è superiore all'80%.
- Le notifiche di errore verranno sempre ricevute tramite posta elettronica. In particolare, saranno il proprietario, il collaboratore e i lettori della risorsa di destinazione a ricevere il messaggio di posta elettronica. Si riceverà sempre un messaggio di posta elettronica di *ripristino* anche quando la scalabilità automatica viene ripristinata dopo un errore e inizia a funzionare normalmente.
- È possibile acconsentire esplicitamente a ricevere una notifica relativa a un'azione di scalabilità riuscita tramite posta elettronica e webhook.

## Procedure consigliate per la scalabilità automatica

Usare le procedure consigliate seguenti quando si usa la scalabilità automatica.

### Assicurarsi che i valori massimo e minimo siano diversi e che tra di essi ci sia un margine adeguato
Se il valore massimo di un'impostazione è 2 e il valore minimo è 2 e il conteggio di istanze correnti è 2, non può verificarsi alcuna azione di scalabilità. L'impostazione consigliata prevede di mantenere un margine adeguato tra i conteggi massimo e minimo delle istanze. La scalabilità automatica verrà sempre applicata entro questi limiti inclusi. Si supponga, tuttavia, di decidere di ridimensionare manualmente (aggiornare) il conteggio delle istanze impostandolo su un valore superiore a quello massimo. La volta successiva che un processo di scalabilità automatica viene eseguito, controlla se il conteggio corrente delle istanze è superiore al massimo. In questo caso, riduce il numero di istanze fino al valore massimo, indipendentemente dalla soglia impostata nelle regole. Allo stesso modo, se si imposta manualmente un conteggio corrente delle istanze inferiore a quello minimo, la volta successiva che un processo di scalabilità automatica viene eseguito, aumenta il numero di istanze fino al valore minimo.

### Usare sempre una combinazione di regole di aumento e di riduzione del numero di istanze

Se si usa solo una parte della combinazione, la scalabilità automatica verrà applicata solo per aumentare o ridurre il numero di istanze fino a raggiungere il valore massimo o minimo.

### Non passare dal portale di Azure al portale di Azure classico e viceversa quando si gestisce la scalabilità automatica
Per i servizi cloud e i servizi app (app Web), usare il portale di Azure (portal.azure.com) per creare e gestire le impostazioni di scalabilità automatica. Per i set di scalabilità di macchine virtuali, usare PowerShell, l'interfaccia della riga di comando o l'API REST per creare e gestire l'impostazione di scalabilità automatica. Non passare dal portale di Azure classico (manage.windowsazure.com) al portale di Azure (portal.azure.com) e viceversa quando si gestiscono le configurazioni di scalabilità automatica. Il portale di Azure classico e il back-end sottostante presentano delle limitazioni. Passare al portale di Azure per gestire la scalabilità automatica con un'interfaccia utente grafica. Le opzioni prevedono l'uso di PowerShell, dell'interfaccia della riga di comando o dell'API REST (tramite Esplora risorse di Azure) per la scalabilità automatica.

### Scegliere la statistica appropriata per la metrica di diagnostica
Per le metriche di diagnostica, è possibile scegliere tra *Medio*, *Minimo*, *Massimo* e *Totale* come metrica per il ridimensionamento. La statistica più comune è *Medio*.

### Scegliere attentamente le soglie per tutti i tipi di metriche
È consigliabile scegliere con attenzione soglie diverse per aumentare e ridurre il numero di istanze a seconda delle situazioni concrete.

*Non sono consigliate* impostazioni di scalabilità automatica come quelle degli esempi seguenti con valori di soglia uguali o molto simili per le condizioni di aumento o di riduzione del numero di istanze:

- Aumentare le istanze di 1 quando il conteggio dei thread è <= 600
- Ridurre le istanze di 1 quando il conteggio dei thread è >= 600


Verrà ora esaminato un esempio di come si può arrivare a un comportamento che può sembrare poco chiaro. Si supponga di iniziare con 2 istanze e che poi il numero medio di thread per ogni istanza cresca fino a 625. Il numero di istanze viene aumentato automaticamente aggiungendo una terza istanza. Si supponga ora che il conteggio medio dei thread nell'istanza scenda a 575. Prima di ridurre le prestazioni, la scalabilità automatica cerca di stimare quale sarà lo stato finale in caso di riduzione del numero di istanze. Ad esempio, 575 x 3 (conteggio corrente delle istanze) = 1.725 / 2 (numero finale di istanze dopo la riduzione delle prestazioni) = 862,5 thread. La scalabilità automatica dovrà quindi aumentare di nuovo immediatamente il numero di istanze anche dopo averlo ridotto, se il conteggio medio dei thread rimane invariato o scende anche solo di poco. Se, tuttavia, aumentasse di nuovo le prestazioni, l'intero processo si ripeterebbe, generando un loop infinito. Per evitare questa situazione *instabile*, la scalabilità automatica non riduce affatto le prestazioni, ma ignora la condizione e la valuta nuovamente la volta successiva che il processo del servizio viene eseguito. Questo potrebbe generare confusione perché sembra che la scalabilità automatica non funzioni quando il conteggio medio dei thread è 575.

Questo comportamento della stima durante una riduzione del numero di istanze serve a evitare una situazione instabile. È bene tenere presente questo comportamento quando si scelgono le stesse soglie per l'aumento e la riduzione del numero di istanze.

È consigliabile scegliere un margine adeguato tra le soglie di aumento e di riduzione del numero di istanze Ad esempio, considerare la combinazione di regole seguente, che è migliore.

- Aumentare le istanze di 1 quando la percentuale di CPU è >= 80
- Ridurre le istanze di 1 quando la percentuale di CPU è <= 60

Ecco cosa fa l'esempio. Si supponga di iniziare con 2 istanze. Se la % di CPU media nelle istanze arriva a 80, la scalabilità automatica aumenta il numero di istanze aggiungendone una terza. Si supponga che nel corso del tempo la percentuale di CPU scenda a 60. La regola di riduzione del numero di istanze della scalabilità automatica valuta lo stato finale se venisse applicata la riduzione del numero di istanze. Ad esempio, 60 x 3 (conteggio corrente delle istanze) = 180 / 2 (numero finale di istanze dopo la riduzione delle prestazioni) = 90. La scalabilità automatica quindi non riduce il numero di istanze perché dovrebbe aumentarlo di nuovo immediatamente. Al contrario, evita di ridurre le prestazioni. Si supponga ora che al controllo successivo risulti che la CPU continui a scendere fino a 50 e venga quindi eseguita una nuova stima: 50 x 3 istanze = 150 / 2 istanze = 75, che è al di sotto della soglia di aumento del numero di istanze pari a 80, quindi il numero di istanze viene correttamente ridotto a 2.

### Considerazioni sul ridimensionamento dei valori di soglia per le metriche speciali
 Per le metriche speciali, ad esempio la metrica di archiviazione o la metrica di lunghezza della coda del bus di servizio, la soglia è il numero medio di messaggi disponibile per il numero corrente di istanze. Scegliere con attenzione il valore di soglia per questa metrica.

Per far comprendere meglio il comportamento, verrà illustrato con esempio.

- Aumentare le istanze di 1 quando il conteggio dei messaggi della coda di archiviazione è >= 50
- Ridurre le istanze di 1 quando il conteggio dei messaggi della coda di archiviazione è <= 10

Si supponga di iniziare con 2 istanze. Si supponga ora che continuino ad arrivare messaggi e che, quando si controlla la coda di archiviazione, il conteggio totale sia pari a 50. Si potrebbe pensare che la scalabilità automatica debba avviare un'azione di aumento del numero di istanze. Si noti tuttavia che si tratta comunque di 50/2 = 25 messaggi per ogni istanza. L'aumento del numero di istanze non viene quindi eseguito. Perché venga eseguito il primo aumento del numero di istanze, il conteggio totale dei messaggi nella coda di archiviazione deve essere pari a 100. Si supponga ora che il conteggio totale dei messaggi raggiunga i 100. Viene aggiunta una terza istanza in seguito a un'azione di aumento del numero di istanze. L'azione successiva di aumento del numero di istanze verrà eseguita solo dopo che il conteggio totale dei messaggi nella coda avrà raggiunto il numero di 150. Ora verrà esaminata un'azione di riduzione del numero di istanze. Si supponga che il numero di istanze sia 3. La prima azione di riduzione del numero di istanze viene eseguita quando i messaggi totali nella coda raggiungono il numero di 30, ovvero 30/3 = 10 messaggi per istanza, che corrisponde alla soglia di riduzione del numero di istanze.

### Considerazioni sul ridimensionamento quando vengono configurati più profili in un'impostazione di scalabilità automatica

In un'impostazione di scalabilità automatica è possibile scegliere un profilo predefinito, che viene sempre applicato indipendentemente da qualsiasi pianificazione o orario, oppure è possibile scegliere un profilo ricorrente o un profilo per un periodo fisso con un intervallo di data e ora.

Quando il servizio di scalabilità automatica li elabora, li controlla sempre nell'ordine seguente:

1. Profilo con data fissa
2. Profilo ricorrente
3. Profilo predefinito ("sempre")

Se la condizione di un profilo viene soddisfatta, la scalabilità automatica non controlla la condizione del profilo successivo. La scalabilità automatica elabora solo un profilo alla volta. Quindi, per includere anche una condizione di elaborazione da un profilo di livello inferiore, è necessario includere anche tali regole nel profilo corrente.

Per spiegarlo, verrà usato un esempio:

L'immagine seguente illustra un'impostazione di scalabilità automatica con un profilo predefinito con un numero minimo di istanze = 2 e un numero massimo = 10. In questo esempio, le regole vengono configurate per aumentare il numero di istanze quando il conteggio dei messaggi nella coda è maggiore di 10 e per ridurlo quando il conteggio dei messaggi nella coda è minore di 3. Ora la risorsa può quindi essere ridimensionata tra le 2 e le 10 istanze.

È anche stato impostato un profilo ricorrente per il lunedì. È stato impostato per un numero minimo di istanze = 2 e un numero massimo di istanze = 12. Quindi il lunedì, la prima volta che la scalabilità automatica controlla questa condizione, se il conteggio delle istanze è 2, ne ridurrà il numero al nuovo valore minimo pari a 3. Finché la scalabilità automatica continua a trovare una corrispondenza per la condizione di questo profilo (lunedì), elaborerà la CPU solo in base alle regole di aumento/riduzione del numero di istanze configurate per questo profilo. Per il momento, non controllerà la lunghezza della coda. Se, tuttavia, si vuole controllare anche la condizione della lunghezza della coda, è necessario includere anche le regole del profilo predefinito nel profilo del lunedì.

Allo stesso modo, quando la scalabilità automatica torna al profilo predefinito, per prima cosa controlla se sono soddisfatte le condizioni minima e massima. Se al momento il numero di istanze è 12, ne riduce il numero a 10, il massimo consentito per il profilo predefinito.

![Impostazioni di scalabilità automatica](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### Considerazioni sul ridimensionamento quando vengono configurate più regole in un profilo
In alcuni casi potrebbe essere necessario impostare più regole in un profilo. Il set di regole di scalabilità automatica seguente viene usato dai servizi quando vengono impostate più regole.

In caso di *aumento del numero di istanze*, la scalabilità automatica verrà eseguita se risulta soddisfatta una regola qualsiasi. In caso di *riduzione del numero di istanze*, la scalabilità automatica richiede che tutte le regole siano soddisfatte.
 
Per illustrare questo concetto, si supponga di avere le 4 regole di scalabilità automatica seguenti:
 
- Se la CPU è < 30%, ridurre il numero di istanze di 1
- ​Se la memoria è < 50%, ridurre il numero di istanze di 1
- ​Se la CPU è > 75%, aumentare il numero di istanzedi 1
- ​Se la memoria è > 75%, aumentare il numero di istanze di 1

Si verificherà quindi quanto segue:
- Se la CPU è pari al 76% e la memoria è pari al 50%, il numero di istanze verrà aumentato.
- Se la CPU è pari al 50% e la memoria è pari al 76%, il numero di istanze verrà aumentato.
 
Ma, se la CPU è pari al 25% e la memoria è pari al 51%, la scalabilità automatica **non** ridurrà il numero di istanze. Per ridurre il numero di istanze, la CPU deve essere al 29% e la memoria al 49%.

### Selezionare sempre un conteggio di istanze predefinito sicuro
Il conteggio predefinito delle istanze è importante perché è in base al conteggio delle istanze che la scalabilità automatica ridimensiona il servizio quando le metriche non sono disponibili. Selezionare quindi un conteggio di istanze predefinito sicuro per i carichi di lavoro.

### Configurare le notifiche relative alla scalabilità automatica
La scalabilità automatica invia una notifica tramite posta elettronica agli amministratori e ai collaboratori della risorsa se si verifica una delle condizioni seguenti:
- Il servizio di scalabilità automatica non riesce a eseguire un'azione.
- Non sono disponibili metriche che consentono al servizio di scalabilità automatica di prendere una decisione sulla scalabilità.
- Sono di nuovo disponibili metriche (ripristino) che consentono di prendere una decisione sulla scalabilità. Oltre alle condizioni precedenti, è possibile configurare notifiche di posta elettronica o webhook per ricevere notifiche relative alle azioni di scalabilità riuscite.

<!---HONumber=AcomDC_0803_2016-->