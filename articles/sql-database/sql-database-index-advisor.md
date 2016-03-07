<properties 
   pageTitle="Advisor dell’indice del database SQL di Azure" 
   description="L’advisor dell’indice del database SQL di Azure consiglia nuovi indici per i database SQL esistenti che consentono di migliorare le prestazioni delle query correnti." 
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
   ms.date="01/23/2016"
   ms.author="sstein"/>

# Advisor dell’indice del database SQL

L'Index Advisor per database SQL di Azure offre indicazioni sugli indici per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query. Il servizio database SQL consente di valutare le prestazioni degli indici analizzando la cronologia relativa all'utilizzo del database SQL. È consigliabile usare gli indici più adatti per l'esecuzione del carico di lavoro tipico del database.

Index Advisor facilita l'ottimizzazione delle prestazioni del database nei modi seguenti:

- Offrendo indicazioni su quali indici creare (le indicazioni sono disponibili solo per indici non cluster).
- Offrendo indicazioni su quali indici eliminare (le indicazioni sugli indici da eliminare sono in anteprima e attualmente si applicano solo agli indici duplicati).
- Consentendo il consenso esplicito ad applicare automaticamente le indicazioni relative agli indici senza alcuna interazione dell'utente. Le indicazioni automatizzate richiedono che la funzionalità [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia abilitata e in esecuzione.
- Eseguendo il rollback automatico delle indicazioni che hanno un impatto negativo sulle prestazioni. 


Questo articolo descrive Index Advisor per i server versione 12. Le indicazioni sugli indici sono disponibili per i server versione 11, ma è necessario eseguire lo script Transact-SQL (T-SQL) in dotazione per implementare l'indicazione. Index Advisor non ripristinerà le operazioni sugli indici nei server versione 11, pertanto è consigliabile monitorare e ripristinare l'impatto sulle prestazioni in base alle esigenze.


### Autorizzazioni

Per visualizzare e creare indicazioni relative agli indici, sono necessarie le autorizzazioni di [controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-configure.md) corrette in Azure.

- Le autorizzazioni **Lettore** e **Collaboratore DB SQL** sono necessarie per visualizzare le indicazioni.
- Le autorizzazioni **Proprietario** e **Collaboratore DB SQL** sono necessarie per eseguire qualunque azione, creare o eliminare gli indici e annullare la creazione dell'indice.


## Visualizzazione delle indicazioni relative agli indici

Nella pagina delle indicazioni relative agli indici sono visualizzati i principali indici suggeriti in base all'impatto potenziale sul miglioramento delle prestazioni. È anche possibile visualizzare lo stato delle ultime operazioni sugli indici. Selezionare un'indicazione o lo stato per visualizzarne i dettagli.

Per visualizzazione le indicazioni relative agli indici:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **SFOGLIA** > **Database SQL** e selezionare il database.
5. Fare clic su **Tutte le impostazioni** > **Index Advisor** per visualizzare le **Indicazioni relative agli indici** disponibili per il database selezionato.

> [AZURE.NOTE] Per ottenere indicazioni relative agli indici, un database deve disporre di circa una settimana di utilizzo e all'interno di tale settimana devono essere presenti attività. Inoltre devono essere presenti anche alcune attività coerenti. L’advisor dell’indice è in grado di ottimizzare più facilmente modelli di query coerenti anziché picchi irregolari casuali di attività. Se non sono disponibili indicazioni, la pagina **Indicazioni relative agli indici** visualizzerà un messaggio per spiegarne il motivo.

![Indici consigliati](./media/sql-database-index-advisor/recommendations.png)

Le indicazioni vengono ordinate nelle seguenti 4 categorie in base al potenziale impatto sulle prestazioni:

| Impatto | Descrizione |
| :--- | :--- |
| Alto | Le indicazioni ad alto impatto devono fornire l'impatto più significativo sulle prestazioni. |
| Sostanziale | Le indicazioni ad impatto significativo devono migliorare notevolmente le prestazioni. |
| Moderato | Le indicazioni ad impatto moderato devono migliorare le prestazioni, ma non sostanzialmente. |
| Basso | Le indicazioni a basso impatto devono offrire prestazioni migliori rispetto all’assenza dell'indice, ma i miglioramenti potrebbero non essere significativi. 
Utilizzare il tag dell’impatto per determinare i migliori candidati per la creazione di nuovi indici.


### Rimozione delle indicazioni relative agli indici dall'elenco

Se l'elenco di indici raccomandati contiene indici che si vuole rimuovere dall'elenco, ignorare l'indicazione:

1. Selezionare un'indicazione nell'elenco **Indici raccomandati**.
2. Fare clic su **Elimina indice** sul pannello **Dettagli indice**.


Se si vuole, aggiungere nuovamente gli indici eliminati all'elenco **Indici raccomandati**:

1. Nel pannello **Indicazioni relative agli indici** fare clic su **Visualizza indicazioni relative agli indici eliminati**.
1. Selezionare un indice eliminato dall'elenco per visualizzarne i dettagli.
1. Facoltativamente, fare clic su **Annulla eliminazione** per aggiungere nuovamente l'indice all'elenco principale delle **Indicazioni relative agli indici**.



## Applicazione delle indicazioni relative agli indici

Index Advisor offre controllo completo su come le indicazioni relative agli indici vengono attivate usando una delle 3 opzioni seguenti.

- Applicare le singole indicazioni una alla volta.
- Abilitare Index Advisor per applicare automaticamente le indicazioni relative agli indici.
- Eseguire manualmente lo script T-SQL consigliato nel database per implementare un'indicazione.

Selezionare un suggerimento per visualizzarne i dettagli e quindi fare clic su **Visualizza script** per esaminare i dettagli esatti di come l'indicazione verrà creata.

Il database rimane online mentre Index Advisor applica l'indicazione: Index Advisor non trasferirà mai un database offline.

### Applicare una singola indicazione

È possibile leggere e accettare le indicazioni una alla volta.

1. Nel pannello **Indicazioni relative agli indici** fare clic su un'indicazione.
2. Nel pannello **Dettagli indice** fare clic su **Applica**.

    ![Applica suggerimento](./media/sql-database-index-advisor/apply.png)


### Abilitare la gestione automatica degli indici

È possibile impostare Index Advisor per implementare automaticamente le indicazioni. Man mano che le indicazioni vengono rese disponibili, verranno applicate automaticamente. Come per tutte le operazioni sugli indici gestite dal servizio, se l'impatto sulle prestazioni è negativo le indicazioni verranno ripristinate.

1. Nel pannello **Indicazioni relative agli indici** fare clic su **Impostazioni Advisor**:

    ![Impostazioni di Advisor](./media/sql-database-index-advisor/settings.png)

2. Impostare Advisor per **creare** o **eliminare** automaticamente gli indici:

    ![Indici consigliati](./media/sql-database-index-advisor/automation.png)




### Eseguire manualmente lo script T-SQL consigliato

Selezionare le indicazioni e quindi fare clic su **Visualizza script**. Eseguire questo script nel database per applicare manualmente l'indicazione.

*Gli indici eseguiti manualmente non vengono monitorati e convalidati per l'impatto sulle prestazioni da parte del servizio*, pertanto è consigliabile monitorare questi indici dopo la creazione per verificare che offrano miglioramenti delle prestazioni e modificarli o eliminarli se necessario. Per informazioni dettagliate sulla creazione di indici, vedere [CREAZIONE INDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Annullamento della creazione dell'indice

Gli indici con stato **In sospeso** possono essere annullati. Non è possibile annullare gli indici in fase di creazione (stato **In esecuzione**)

1. Selezionare qualunque indice **In sospeso** nell’area **Operazioni sugli indici** per aprire il pannello **Dettagli indice**.
2. Fare clic su **Annulla** per interrompere la procedura di creazione dell’indice.



## Monitoraggio delle operazioni sugli indici

L'applicazione di un'indicazione potrebbe non avvenire in tempo reale. Il portale fornisce dettagli relativi allo stato delle operazioni sugli indici. Quando si gestiscono gli indici, di seguito sono riportati gli stati possibili di un indice:

| Stato | Descrizione |
| :--- | :--- |
| In sospeso | Il comando di creazione dell’indice è stato ricevuto e la creazione dell’indice è stata pianificata. |
| In esecuzione | Il comando di creazione dell’indice è in funzione e la creazione dell'indice è in corso. |
| Esito positivo | L'indice è stato creato correttamente. |
| Non riuscito | L’indice non è stato creato. Può trattarsi di un problema temporaneo o eventualmente di una modifica dello schema della tabella e lo script non è più valido. |
| Ripristino | La procedura di creazione dell’indice è stata annullata o è stata considerata non efficiente ed è in corso il ripristino automatico. |

Fare clic su un'indicazione in-process nell'elenco per visualizzarne i dettagli:

![Indici consigliati](./media/sql-database-index-advisor/operations.png)



### Ripristino di un indice

Se è stato usato Advisor per creare un indice, ovvero non è stato eseguito manualmente lo script T-SQL, l'indice verrà automaticamente ripristinato se viene rilevato un impatto negativo sulle prestazioni. Se per qualsiasi motivo si vuole semplicemente annullare un'operazione di Index Advisor, procedere come segue.


1. Selezionare un indice creato correttamente nell'elenco di **Operazioni sugli indici**.
2. Fare clic su **Ripristina** nel pannello **Dettagli indice** oppure fare clic su **Visualizza script** per uno script DROP INDEX che è possibile eseguire.

![Indici consigliati](./media/sql-database-index-advisor/details.png)


## Monitoraggio dell'impatto sulle prestazioni delle indicazioni relative agli indici

Dopo aver implementato correttamente le indicazioni, fare clic su **Informazioni dettagliate query** nel pannello Dettagli indice per aprire [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) ed esaminare l'impatto sulle prestazioni delle query principali.

![Monitorare l'impatto sulle prestazioni](./media/sql-database-index-advisor/query-insights.png)


## Riepilogo

Index Advisor offre indicazioni relative agli indici e un'esperienza automatizzata per la gestione degli indici per il database SQL. Offrendo script T-SQL, nonché opzioni di gestione degli indici individuali e completamente automatizzate, Index Advisor risulta molto utile per l'ottimizzazione degli indici del database e quindi per migliorare le prestazioni delle query.



## Passaggi successivi

Monitorare le indicazioni relative agli indici e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. L’advisor dell’indice continuerà a monitorare e raccomandare indici che potenzialmente possono migliorare le prestazioni del database.

<!---HONumber=AcomDC_0224_2016-->