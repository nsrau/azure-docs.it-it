---
title: Maggiore precisione dei modelli di Visione artificiale e classificazione in Azure Machine Learning
description: Come migliorare la precisione dei modelli di classificazione delle immagini, rilevamento degli oggetti e somiglianza tra immagini di Visione artificiale con il pacchetto Azure Machine Learning per Visione artificiale.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783580"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Migliorare la precisione dei modelli di Visione artificiale

Con il **pacchetto Azure Machine Learning per Visione artificiale** è possibile compilare e distribuire modelli di classificazione delle immagini, rilevamento degli oggetti e somiglianza tra immagini di Visione artificiale. Questo articolo offre informazioni su questo pacchetto e su come installarlo.

In questo articolo si apprenderà come ottimizzare questi modelli per aumentarne la precisione. 

## <a name="accuracy-of-image-classification-models"></a>Precisione dei modelli di classificazione delle immagini

Il pacchetto Visione artificiale offre buoni risultati per un'ampia varietà di set di dati. Tuttavia, come per la maggior parte dei progetti di Machine Learning, ottenere i migliori risultati possibili per un nuovo set di dati richiede un'accurata regolazione dei parametri, nonché la valutazione di diverse decisioni di progettazione. Le sezioni seguenti offrono indicazioni su come migliorare la precisione su un determinato set di dati, ossia quali parametri è consigliabile ottimizzare per primi, quali valori provare per questi parametri e quali errori evitare.

In generale, il training dei modelli di Deep Learning richiede un compromesso fra tempo di training e precisione del modello. Il pacchetto Visione artificiale offre parametri predefiniti e preimpostati (vedere la prima riga nella tabella seguente) che favoriscono un training rapido producendo allo stesso tempo modelli ad alta precisione. Questa precisione può spesso essere ulteriormente migliorata usando, ad esempio, una risoluzione delle immagini più elevata o modelli più avanzati, al prezzo però di un aumento del tempo di training di 10 volte o anche più.

È quindi consigliabile iniziare con i parametri predefiniti, eseguire il training di un modello, esaminare i risultati, correggere le annotazioni sui dati reali, se necessario, e solo allora provare a usare parametri che rallentano il tempo di training (vedere i valori dei parametri suggeriti nella tabella seguente). Conoscere questi parametri, benché tecnicamente non necessario, è comunque consigliato.


### <a name="best-practices-and-tips"></a>Procedure consigliate e suggerimenti

* Qualità dei dati: i set di training e di test devono essere di qualità elevata. Ciò significa che le immagini sono annotate correttamente, quelle ambigue vengono rimosse (ad esempio quelle in cui non è chiaro a un occhio umano se l'immagine mostra una pallina da tennis o un limone) e gli attributi si escludono a vicenda (ovvero, ogni immagine appartiene esattamente a un solo attributo).

* Prima di perfezionare la rete neurale profonda, occorre eseguire il training di un classificatore SVM usando come utilità di funzioni una rete neurale profonda predefinita sottoposta a un training preliminare. Questo scenario è supportato nel pacchetto Visione artificiale e non richiede lunghi tempi di training dal momento che la rete non viene modificata. Questo semplice approccio ottiene spesso buoni risultati in termini di precisione e quindi rappresenta una solida baseline. Il passaggio successivo consiste quindi nel perfezionare la rete neurale profonda che dovrebbe offrire il miglior livello di precisione.

* Nel caso in cui l'oggetto di interesse all'interno dell'immagine sia di piccole dimensioni, gli approcci di classificazione delle immagini non rappresentano notoriamente una valida soluzione. In questi casi è consigliabile usare un approccio di rilevamento degli oggetti come Faster R-CNN basato su Tensorflow del pacchetto Visione artificiale.

* È sempre consigliabile eseguire un training dei dati approfondito. Come regola generale, è opportuno avere almeno 100 esempi per ogni classe, ossia 100 immagini per "cane", 100 immagini per "gatto" e così via. Il training di un modello con meno immagini è possibile, ma potrebbe produrre risultati non soddisfacenti.

* Le immagini di training devono risiedere localmente nel computer con la GPU, in un'unità SSD (non HDD). In caso contrario, la latenza della lettura delle immagini potrebbe ridurre drasticamente la velocità di training (addirittura di 100 volte).


### <a name="parameters-to-optimize"></a>Parametri da ottimizzare

Trovare i valori ottimali per questi parametri è importante e spesso può migliorare la precisione in maniera significativa:
* Velocità di apprendimento (`lr_per_mb`): è senza dubbio il parametro più importante per un'analisi accurata delle immagini. Se la precisione del set di training dopo il perfezionamento della rete neurale profonda è superiore a ~5%, molto probabilmente la velocità di apprendimento è troppo alta oppure il numero di periodi di training è troppo basso. Specialmente con i set di dati di piccole dimensioni, la rete neurale profonda tende all'overfitting dei dati di training, anche se all'atto pratico produce buoni modelli sul set di test. Generalmente si usano 15 periodi in cui la velocità di apprendimento iniziale è ridotta di due volte. Il training che usa un numero maggiore di periodi può in alcuni casi migliorare le prestazioni.

* Risoluzione di input(`image_dims`): la risoluzione predefinita delle immagini è di 224 x 224 pixel. L'uso di una risoluzione delle immagini superiore, ad esempio 500 x 500 pixel o 1000 x 1000 pixel, può comportare un miglioramento significativo della precisione, ma rallenta il perfezionamento della rete neurale profonda. Il pacchetto Visione artificiale prevede che la risoluzione di input sia una tupla di (color-channels, image-width, image-height), ad esempio (3, 224, 224), dove il numero di canali di colore deve essere impostato su 3 (bande Rosso-Verde-Blu).

* Architettura del modello (`base_model_name`): provare a usare reti neurali più profonde come ResNet-34 o ResNet-50 invece del modello predefinito ResNet-18. Il modello Resnet-50 non solo è più profondo, ma il suo output del penultimo livello è di 2048 float (contro i 512 float dei modelli ResNet-18 e ResNet 34). Questa maggiore dimensionalità può essere particolarmente vantaggiosa se si mantiene fissa la rete neurale profonda e si esegue invece il training di un classificatore SVM.

* Dimensioni del mini-batch (`mb_size`): dimensioni del mini-batch elevate accelerano il tempo di training, al prezzo però di un maggior consumo di memoria della rete neurale profonda. Di conseguenza, se si selezionano modelli più profondi (ad esempio ResNet-50 piuttosto che ResNet-18) e/o una risoluzione delle immagini superiore (500 \* 500 pixel invece di 224 \* 224 pixel), occorre in genere ridurre le dimensioni del mini-batch per evitare errori di memoria insufficiente. Quando si cambiano le dimensioni del mini-batch, spesso occorre modificare anche la velocità di apprendimento, come illustrato nella tabella seguente.
* Tasso di perdita di dati (`dropout_rate`) e normalizzatore L2 (`l2_reg_weight`): l'overfitting della rete neurale profonda può essere ridotto usando un tasso di perdita di dati di 0,5 (l'impostazione predefinita è 0,5 nel pacchetto Visione artificiale) o superiore e aumentando il peso del normalizzatore (l'impostazione predefinita è 0,0005 nel pacchetto Visione artificiale). Tenere presente però che, specialmente nei set di dati di piccole dimensioni, l'overfitting della rete neurale profonda è difficile e spesso impossibile da evitare.


### <a name="parameter-definitions"></a>Definizioni dei parametri

- **Velocità di apprendimento**: dimensioni del passo usate durante l'apprendimento basato su discesa del gradiente. Se vengono impostate su un valore troppo basso, il training del modello richiederà molti periodi, mentre se si usa un valore troppo alto il modello non convergerà verso una buona soluzione. In genere si usa una pianificazione in cui la velocità di apprendimento viene ridotta dopo un determinato numero di periodi. Ad esempio, la pianificazione della velocità di apprendimento `[0.05]*7 + [0.005]*7 + [0.0005]` corrisponde all'uso di una velocità di apprendimento iniziale di 0,05 per i primi sette periodi, seguita da una velocità di apprendimento ridotta di 10 volte, ossia pari a 0,005, per altri sette periodi, per finire con l'ottimizzazione del modello per un singolo periodo con una velocità di apprendimento ridotta di 100 volte, ossia pari a 0,0005.

- **Dimensioni del mini-batch**: le GPU possono elaborare più immagini in parallelo per accelerare la velocità di calcolo. Queste immagini elaborate in parallelo sono dette anche mini-batch. Maggiori sono le dimensioni del mini-batch, più rapido sarà il training, al prezzo però di un maggior consumo di memoria della rete neurale profonda.

### <a name="recommended-parameter-values"></a>Valori dei parametri consigliati

La tabella seguente elenca diversi set di parametri che hanno prodotto modelli con un alto livello di precisione su un'ampia varietà di attività di classificazione delle immagini. I parametri ottimali dipendono dal set di dati specifico e dall'esatta GPU usata, pertanto la tabella dovrebbe essere considerata un semplice riferimento. Dopo aver provato questi parametri, prendere in considerazione anche risoluzioni delle immagini di oltre 500 x 500 pixel oppure modelli più profondi come Resnet-101 o Resnet-152.

La prima riga della tabella corrisponde ai parametri predefiniti impostati all'interno del pacchetto Visione artificiale. Tutte le altre righe richiedono tempi di training più lunghi (indicati nella prima colonna), a vantaggio però di una maggiore precisione (vedere la seconda colonna per conoscere la precisione media su tre set di dati interni). Ad esempio, i parametri nell'ultima riga richiedono un training 5-15 volte più lungo, il cui risultato è però una precisione (espressa come media) su tre set di test interni aumentata da 82,6% a 92,8%.

Modelli più profondi e una risoluzione di input più elevata consumano più memoria della rete neurale profonda, pertanto è necessario ridurre le dimensioni del mini-batch con una maggiore complessità del modello per evitare errori di memoria insufficiente. Come si può vedere nella tabella seguente, è utile ridurre la velocità di apprendimento di un fattore di due quando si riducono le dimensioni del mini-batch per lo stesso moltiplicatore. Potrebbe essere necessario ridurre ulteriormente le dimensioni del mini-batch sulle GPU con quantità di memoria inferiori.

| Tempo di training (stima approssimativa) | Esempio di precisione | Dimensioni mini-batch (*mb_size*) | Velocità di apprendimento (*lr_per_mb*) | Risoluzione immagine (*image_dims*) | Architettura rete neurale profonda (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 volta (riferimento) | 82,6% | 32 | [0,05]\*7  + [0,005]\*7  + [0,0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2-5 volte    | 90,2% | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2-5 volte    | 87,5% | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5-15 volte        | 92,8% |  8 | [0,01]\*7  + [0,001]\*7  + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul pacchetto Azure Machine Learning per Visione artificiale:
+ Vedere la documentazione di riferimento

+ Vedere l'articolo su [altri pacchetti Python per Azure Machine Learning](reference-python-package-overview.md)