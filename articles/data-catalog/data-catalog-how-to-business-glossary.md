<properties
	pageTitle="Come configurare il glossario aziendale per l'assegnazione di tag regolamentata | Microsoft Azure"
	description="Articolo sulle procedure che illustra il glossario aziendale in Azure Data Catalog per definire e usare un vocabolario aziendale comune in base al quale assegnare tag ad asset di dati registrati."
	services="data-catalog"
	documentationCenter=""
	authors="steelanddata"
	manager="NA"
	editor=""
	tags=""/> 
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="09/21/2016"
	ms.author="maroche"/> 

# Come configurare il glossario aziendale per l'assegnazione di tag regolamentata

## Introduzione

Azure Data Catalog offre funzionalità per l'individuazione delle origini dati che consentono agli utenti di trovare e comprendere facilmente le origini dati necessarie per eseguire analisi e prendere decisioni. Le funzionalità di individuazione fanno veramente la differenza quando gli utenti hanno la possibilità di trovare e comprendere la più ampia gamma di origini dati disponibili.

L'assegnazione di tag è una delle funzionalità di Data Catalog che aiuta a capire i dati relativi agli asset. Questa funzionalità consente agli utenti di associare parole chiave a un asset o una colonna semplificando così l'individuazione dell'asset tramite ricerca o navigazione. Consente anche di comprendere più facilmente il contesto e lo scopo dell'asset.

L'assegnazione di tag può, tuttavia, causare a volte problemi. Alcuni dei problemi causati dall'assegnazione di tag sono illustrati di seguito:

1.	Uso di abbreviazioni su alcuni asset e testo espanso su altre in fase di assegnazione di tag. Questa incoerenza impedisce l'individuazione di asset nonostante l'obiettivo fosse di contrassegnare gli asset usando lo stesso tag.
2.	Tag con significati differenti in contesti diversi. Ad esempio, un tag denominato "Ricavi" su un set di dati del cliente potrebbe significare ricavi del cliente, ma lo stesso tag su un set di dati di vendite trimestrali potrebbe invece significare ricavi trimestrali dell'azienda.

Per affrontare queste e altre problematiche simili, Data Catalog include un glossario aziendale.

Il glossario aziendale di Data Catalog dati consente alle organizzazioni di spiegare i termini aziendali principali e includere le relative definizioni per creare un vocabolario aziendale comune. In questo modo si garantisce coerenza nell'uso dei dati all'interno dell'organizzazione. Dopo aver definito i termini nel glossario aziendale, è possibile assegnarli ad asset di dati nel catalogo usando lo stesso sistema dell'assegnazione di tag, abilitando in questo modo l'_assegnazione di tag regolamentata_.

> [AZURE.NOTE] Le funzionalità descritte in questo articolo sono disponibili solo nell'edizione Standard di Azure Data Catalog. L'edizione gratuita non prevede l'assegnazione di tag regolamentata né un glossario aziendale tra le funzionalità.

## Disponibilità e privilegi del glossario

*Il glossario aziendale è disponibile nell'edizione Standard di Azure Data Catalog. L'edizione gratuita di Data Catalog non include un glossario.*

È possibile accedere al glossario aziendale dall'opzione Glossario nel menu di navigazione del portale di Data Catalog.

![Accesso al glossario aziendale](./media/data-catalog-how-to-business-glossary/01-portal-menu.png) 


Gli amministratori del catalogo dati e i membri del ruolo Amministratori del glossario possono creare, modificare ed eliminare i termini del glossario aziendale. Tutti gli utenti di Data Catalog possono visualizzare le definizioni dei termini e possono assegnare agli asset tag usando i termini del glossario.

![Aggiunta di un nuovo termine di glossario](./media/data-catalog-how-to-business-glossary/02-new-term.png) 


## Creazione dei termini di glossario

Facendo clic sul pulsante Nuovo termine, gli amministratori del catalogo dati e gli amministratori del glossario possono creare nuovi termini di glossario con i campi seguenti:

* Una definizione aziendale del termine
* Una descrizione che acquisisce l'uso previsto o le regole business per l'asset e/o la colonna
* Un elenco delle parti interessate che conoscono meglio il termine
* Il termine padre, che definisce la gerarchia in cui è organizzato il termine


## Gerarchie dei termini di glossario

Il glossario aziendale di Data Catalog offre la possibilità di descrivere il vocabolario aziendale sotto forma di gerarchia di termini. In questo modo le organizzazioni possono creare una classificazione di termini che rappresenta meglio la propria tassonomia aziendale.

Il nome di un termine deve essere univoco a un determinato livello della gerarchia. Non sono consentiti nomi duplicati. Non esiste alcun limite al numero di livelli in una gerarchia. Una gerarchia risulta tuttavia di più facile comprensione se è costituita da non oltre tre livelli.

L'uso delle gerarchie nel glossario aziendale è facoltativo. Se non viene specificato il campo del termine padre, viene creato un elenco semplice, vale a dire senza gerarchia, di termini di glossario.

## Assegnazione di tag agli asset con termini di glossario

Dopo aver definito i termini di glossario, è possibile assegnare tag agli asset per ottimizzare la ricerca nel glossario ogni volta in cui l'utente digita il tag. Il portale di Data Catalog visualizza un elenco di termini di glossario che corrispondono alla ricerca dell'utente. Se l'utente seleziona un termine di glossario dall'elenco, questo viene aggiunto all'asset come tag. In questo caso si parla di tag di glossario. L'utente può anche scegliere di creare un nuovo tag digitando un termine che non è incluso nel glossario. In questo caso si parla di tag utente.

![Assegnazione di tag ad asset di dati con un tag utente o due tag di glossario](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png) 

> [AZURE.NOTE] I tag utente sono l'unico tipo di tag supportato nell'edizione gratuita di Data Catalog.

### Comportamento al passaggio del mouse sui tag
Nel portale di Data Catalog i due tipi di tag sono visivamente distinti e il comportamento al passaggio del mouse su questi tag è diverso. Quando il mouse viene spostato su un tag utente, è possibile visualizzare il testo del tag e l'utente o gli utenti che lo hanno aggiunto. Quando il mouse viene spostato su un tag di glossario, vengono visualizzati anche la definizione del termine di glossario e un collegamento per aprire il glossario aziendale e visualizzare la definizione completa del termine.

### Filtri di ricerca per i tag
È possibile cercare sia che i tag di glossario che i tag utente. I tag possono essere applicati come filtri in una ricerca.

## Riepilogo
Il glossario aziendale di Azure Data Catalog e la relativa assegnazione di tag regolamentata consentono di identificare, gestire e individuare asset di dati in modo coerente. Grazie al glossario aziendale è possibile promuovere l'apprendimento del vocabolario aziendale tra gli utenti di un'organizzazione e supportare metadati significativi da acquisire, semplificando in questo modo l'individuazione e la comprensione di asset.

## Vedere anche

- [Documentazione relativa all'API REST per operazioni che riguardano il glossario aziendale](https://msdn.microsoft.com/library/mt708855.aspx)

<!---HONumber=AcomDC_0921_2016-->