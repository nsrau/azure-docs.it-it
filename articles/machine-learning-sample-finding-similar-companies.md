<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Esempio di Azure Machine Learning: Individuazione di società simili

*L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:*

    Sample Experiment - S & P 500 Company Clustering - Development

## Descrizione del problema

Eseguire il clustering di società incluse nell'indice S&P 500 sulla base di articoli pubblicati su Wikipedia relativi a tali società. Questo modello può essere usato, ad esempio, per classificare le nuove società in base alle analogie con le società S&P 500 esistenti.

## Dati

I dati sono tratti da dump XML di Wikipedia. Sono stati pre-elaborati all'esterno di ML Studio per estrarre il contenuto di testo per le singole società, rimuovere la formattazione wiki, rimuovere i caratteri alfanumerici e convertire tutto il testo in minuscolo. Sono state anche aggiunte le categorie di società note. Tenere presente che per alcune società non sono stati trovati articoli. Per questo motivo il numero di record è inferiore a 500.

## Modello

In primo luogo, i contenuti degli articoli vengono passati al modulo Hashing funzionalità, che trasforma i dati testuali in formato numerico in base al valore hash di ogni token. Questi dati hanno una dimensionalità troppo alta e sono troppo sparsi per poter essere usati direttamente nel clustering K-Means. Viene quindi usata l'analisi PCA all'interno del modulo Esecuzione script R per ridurre la dimensionalità a 10 variabili. Si noti che è possibile visualizzare i risultati PCA aprendo l'output giusto del modulo R.

Dal procedimento per tentativi ed errori è emerso che la prima variabile, quella con la varianza più alta, dei dati trasformati da PCA sembra avere un effetto negativo sul clustering. Pertanto viene rimossa dal set di componenti usando il modulo Colonne progetto.

Viene quindi usato l'algoritmo K-Means per inserire i dati in 3 cluster. Mediante tentativi ed errori è stato riscontrato che questo numero porta a risultati ragionevoli. Sono stati provati anche 4 e 5 cluster.

Infine viene usato l'editor di metadati per convertire la variabile di etichetta cluster in categorica. Inoltre, i risultati vengono convertiti in un file CSV per il download.

## Risultati

I risultati possono essere esaminati visualizzando l'output dell'editor di metadati e tracciando la colonna Categoria (nota in base ai dati di Wikipedia) rispetto alle colonne Assegnazioni. I 3 cluster corrispondono approssimativamente alle seguenti categorie note.

Cluster 0: Beni di consumo discrezionali, Beni di consumo primari, Finanza, Sanità

Cluster 1: IT

Cluster 2: Energia, Pubblica utilità, Servizi di telecomunicazione

**Nota**: la suddivisione di clustering non è netta. Il cluster 0 include varie società dei comparti IT ed energia, mentre Prodotti industriali e Materiali sono divisi tra il cluster 0 e il cluster 1.

**Nota**: il clustering può variare da esecuzione a esecuzione.

