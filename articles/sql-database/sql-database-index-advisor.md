<properties 
   pageTitle="Advisor dell’indice del database SQL di Azure" 
   description="Vengono fornite indicazioni relative agli indici che consentono di creare facilmente indici adatti all'esecuzione del carico di lavoro di un database SQL di Azure esistente." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>


<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="06/30/2015"
   ms.author="sstein"/>


# Advisor dell’indice del database SQL

L’advisor dell’indice del database SQL di Azure consiglia nuovi indici per i database SQL esistenti che consentono di migliorare le prestazioni delle query correnti.

Il servizio del database SQL consente di valutare le prestazioni degli indici analizzando l'utilizzo cronologico delle risorse per un database SQL e vengono consigliati gli indici più adatti per l'esecuzione del carico di lavoro tipico del database.

L’advisor dell’indice semplifica la gestione degli indici fornendo indicazioni su quali indici creare. Per i server V12, l’advisor dell’indice può anche creare e convalidare gli indici nel [portale di Azure](https://portal.azure.com/) con pochi clic. Dopo aver creato l'indice, il servizio del database SQL analizza le prestazioni del carico di lavoro del database e fornisce i dettagli dell'impatto del nuovo indice. Se l'analisi determina che un indice consigliato ha un impatto negativo sulle prestazioni, l'indice viene ripristinato automaticamente.

L’advisor dell'indice consente di dedicare meno tempo all’ottimizzazione delle prestazioni del database.


> [AZURE.NOTE]L’advisor dell’indice è attualmente in anteprima ed è disponibile solo nel [portale di Azure](https://portal.azure.com/).


## Considerazioni di anteprima

L’advisor dell’indice è attualmente in anteprima e presenta le seguenti limitazioni:

- Le indicazioni relative agli indici possono essere create e convalidate automaticamente solo per i server V12 (le indicazioni e gli script di creazione degli indici di vengono fornite per i server V12).
- Indicazioni e gestione sono disponibili solo per indici non cluster.

## Prerequisiti

Per visualizzare e creare indicazioni relative agli indici, sono necessarie le autorizzazioni di [controllo dell'accesso basato sui ruoli](role-based-access-control-configure.md) corrette in Azure.

- Le autorizzazioni **Lettore** e **Collaboratore DB SQL** sono necessarie per visualizzare le indicazioni.
- Le autorizzazioni **Proprietario** e **Collaboratore DB SQL** sono necessarie per eseguire qualunque azione, creare o eliminare gli indici e annullare la creazione dell'indice.


## Utilizzo dell’advisor dell'indice

L’advisor dell’indice è facile da utilizzare. Per semplificare la gestione degli indici per il database, attenersi alle seguenti indicazioni:

- Prima, esaminare l'elenco delle indicazioni relative agli indici e decidere quali indici da creare o ignorare. L'elenco delle indicazioni viene ordinato e contrassegnato con il relativo impatto delle prestazioni stimato (descritto di seguito). 
- Creare o ignorare indici consigliati. Creare automaticamente l'indice facendo clic su **Creare un indice** nel portale o creare l'indice manualmente eseguendo lo script di creazione di indici.
- Per gli indici creati manualmente, è necessario monitorare la procedura di creazione e misurare l'impatto sulle prestazioni. Per gli indici creati automaticamente, il monitoraggio e l’analisi dell’impatto sulle prestazioni vengono eseguiti automaticamente dal servizio del database SQL di Azure. 



## Esaminare gli indici consigliati

L’advisor degli indici fornisce un elenco di indicazioni relative agli indici nel pannello del database nel [portale di Azure](https://portal.azure.com/). Le migliori indicazioni selezionate vengono visualizzate per ogni tabella nel database selezionato in cui la creazione di un nuovo indice può fornire miglioramenti delle prestazioni.

### Per esaminare le indicazioni relative agli indici attualmente disponibili:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **SFOGLIA** nel menu a sinistra.
3. Fare clic su **Database SQL** nel pannello **Sfoglia**.
4. Nel pannello dei **database SQL**, fare clic sul database di cui si desidera esaminare gli indici consigliati.
5. Fare clic su **Advisor dell’indice** per aprire e visualizzare le **Indicazioni relative agli indici** disponibili per il database selezionato.

> [AZURE.NOTE]Per ottenere indicazioni relative agli indici, un database deve disporre di circa una settimana di utilizzo e all'interno di tale settimana devono essere presenti attività. Inoltre devono essere presenti anche alcune attività coerenti. L’advisor dell’indice è in grado di ottimizzare più facilmente modelli di query coerenti anziché picchi irregolari casuali di attività.

![Indici consigliati][3]

Le indicazioni vengono ordinate nelle seguenti 4 categorie in base al potenziale impatto sulle prestazioni:

| Impatto | Descrizione |
| :--- | :--- |
| Alto | Le indicazioni ad alto impatto devono fornire l'impatto più significativo sulle prestazioni. |
| Sostanziale | Le indicazioni ad impatto significativo devono migliorare notevolmente le prestazioni. |
| Moderato | Le indicazioni ad impatto moderato devono migliorare le prestazioni, ma non sostanzialmente. |
| Basso | Le indicazioni a basso impatto devono offrire prestazioni migliori rispetto all’assenza dell'indice, ma i miglioramenti potrebbero non essere significativi. 
Utilizzare il tag dell’impatto per determinare i migliori candidati per la creazione di nuovi indici.

### Gestione dell'elenco di indici consigliati

Se l'elenco di indici consigliati contiene indici che non sono ritenuti utili, l’advisor dell’indice consente di ignorare le indicazioni relative agli indici (è possibile aggiungere nuovamente indici eliminati all’elenco degli **Indici consigliati** in un secondo momento, se necessario).

#### Eliminazione di una indicazione di indici

1. Selezionare l'indice nell'elenco degli **Indici consigliati**.
1. Fare clic su **Elimina indice** sul pannello **Dettagli indice**.

#### Visualizzazione degli indici eliminati e nuova aggiunta all'elenco principale

1. Nel pannello **Indicazioni relative agli indici** fare clic su **Visualizza indicazioni relative agli indici eliminati**.
1. Selezionare un indice eliminato dall'elenco per visualizzarne i dettagli.
1. Facoltativamente, fare clic su **Annulla eliminazione** per aggiungere nuovamente l'indice all'elenco principale delle **Indicazioni relative agli indici**.



## Creazione di nuovi indici

L’advisor dell’indice fornisce il controllo completo della modalità di creazione degli indici. Ogni indicazione fornisce uno script di creazione di indice T-SQL ed è possibile esaminare i dettagli esatti di come l'indice verrà creato prima di eseguire qualsiasi azione in un database.

Le indicazioni relative agli indici sono disponibili per tutti i server di database SQL di Azure, ma solo i server V12 forniscono la creazione automatizzata dell'indice. I server non V12 possono comunque trarre vantaggio dall’advisor dell'indice, ma è necessario creare manualmente gli indici, come descritto di seguito.

Sia per la creazione automatica che manuale di un indice, è sufficiente selezionare un indice consigliato dal pannello **Indicazioni relative agli indici** ed eseguire le operazioni seguenti:

### Creazione automatica dell'indice (solo server V12)

Se il database è in un server V12, è possibile creare facilmente un indice consigliato selezionando l'indice desiderato nel portale e facendo clic su **Crea indice**.

Il database rimane online durante la creazione dell'indice, l’utilizzo dell’advisor dell’indice per creare un indice non disconnette il database.

Inoltre, gli indici creati con **Crea indice** non richiedono un ulteriore monitoraggio delle prestazioni. Se l'indice ha un impatto negativo sulle prestazioni, esso viene ripristinato automaticamente. Dopo l'utilizzo di Crea indice, nel portale sono disponibili metriche sull'impatto del nuovo indice.


### Creazione manuale dell'indice (tutti i server)

Selezionare qualunque indice consigliato nel portale e fare clic su **Visualizza script**. Eseguire questo script nel database per creare l'indice consigliato. Gli indici creati manualmente non vengono monitorati e convalidati per l’impatto effettivo sulle prestazioni, pertanto è consigliabile monitorare questi indici dopo la creazione per verificare che forniscano miglioramenti delle prestazioni e modificarli o eliminarli se necessario. Per informazioni dettagliate sulla creazione di indici, vedere [CREAZIONE INDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Annullamento della creazione dell'indice

Gli indici con stato **In sospeso** possono essere annullati. Non è possibile annullare gli indici in fase di creazione (stato **In esecuzione**)

1. Selezionare qualunque indice **In sospeso** nell’area **Operazioni sugli indici** per aprire il pannello **Dettagli indice**.
1. Fare clic su **Annulla** per interrompere la procedura di creazione dell’indice.

## Monitoraggio delle operazioni dell’indice dopo la creazione di indici

La creazione di un indice non si verifica immediatamente. Il portale fornisce dettagli relativi allo stato delle operazioni sugli indici. Quando si gestiscono gli indici, di seguito sono riportati gli stati possibili di un indice:

| Stato | Descrizione |
| :--- | :--- |
| In sospeso | Il comando di creazione dell’indice è stato ricevuto e la creazione dell’indice è stata pianificata. |
| In esecuzione | Il comando di creazione dell’indice è in funzione e la creazione dell'indice è in corso. |
| Esito positivo | L'indice è stato creato correttamente. |
| Non riuscito | L’indice non è stato creato. Può trattarsi di un problema temporaneo o eventualmente di una modifica dello schema della tabella e lo script non è più valido. |
| Ripristino | La procedura di creazione dell’indice è stata annullata o è stata considerata non efficiente ed è in corso il ripristino automatico. |



![Indici consigliati][4]



## Rimozione di un indice
È possibile rimuovere gli indici creati con l’advisor dell’indice.


1. Selezionare un indice creato correttamente nell'elenco di **Operazioni sugli indici**.
1. Fare clic su **Rimuovi indice** sul pannello **Dettagli indice** oppure fare clic su **Visualizza script** per uno script DROP INDEX.



## Riepilogo

Le indicazioni relative agli indici offrono un'esperienza automatizzata per la gestione della creazione e dell'analisi dell'indice di ogni database SQL e la raccomandazione degli indici migliori. Fare clic sul riquadro **Advisor dell’indice** sul pannello di un database per visualizzare le indicazioni relative agli indici.



## Passaggi successivi

Monitorare le indicazioni relative agli indici e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. L’advisor dell’indice continuerà a monitorare e raccomandare indici che potenzialmente possono migliorare le prestazioni del database.


<!--Image references-->
[1]: ./media/sql-database-index-advisor/index-recommendations.png
[2]: ./media/sql-database-index-advisor/index-details.png
[3]: ./media/sql-database-index-advisor/recommended-indexes.png
[4]: ./media/sql-database-index-advisor/index-operations.png

<!---HONumber=August15_HO6-->