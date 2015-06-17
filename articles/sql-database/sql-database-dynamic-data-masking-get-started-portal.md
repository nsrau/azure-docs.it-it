<properties 
   pageTitle="Introduzione al mascheramento dei dati dinamici del database SQL (portale di Azure)" 
   description="Introduzione al mascheramento dei dati dinamici del database SQL nel portale di Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="03/25/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Introduzione al mascheramento dei dati dinamici del database SQL (portale di Azure)

> [AZURE.SELECTOR]
- [Mascheramento dei dati dinamici: Portale di anteprima di Azure](sql-database-dynamic-data-masking-get-started.md)

## Informazioni generali

Il mascheramento dei dati dinamici del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. Il mascheramento dei dati dinamici è in anteprima per i livelli di servizio Basic, Standard e Premium nella versione V12 del database SQL di Azure.

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.

Ad esempio, un addetto del call center può identificare i chiamanti da alcune cifre del numero di previdenza sociale o del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto del supporto tecnico. Uno sviluppatore può definire una regola di mascheramento da applicare a ciascun risultato di query per rendere visibili solo le ultime quattro cifre del numero di previdenza sociale o di carta di credito nel set di risultati. Oppure, mediante la maschera di dati appropriata per la protezione di dati con informazioni personali (PII), uno sviluppatore può eseguire una query negli ambienti di produzione per la risoluzione dei problemi senza violare le normative di conformità.

## Nozioni fondamentali sul mascheramento dei dati dinamici del database SQL

Impostare i criteri di mascheramento dei dati dinamici nel portale di Azure e completare l'installazione mediante la stringa di connessione con protezione attivata usata dall'applicazione o dai client che accedono al database.

> [AZURE.NOTE] Per impostare un mascheramento dei dati dinamici nel portale di anteprima di Azure, vedere [Introduzione al mascheramento dei dati dinamici del database SQL (portale di Azure)](sql-database-dynamic-data-masking-get-started.md). 

### Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dall'amministratore del database di Azure, dall'amministratore del server o dal responsabile della sicurezza.

### Criteri di mascheramento dei dati dinamici

* **Account di accesso con privilegi**: un set di account di accesso per i quali i dati nei risultati delle query SQL saranno visibili.
  
* **Regole di mascheramento**: un set di regole che definiscono i campi designati da nascondere e la funzione di mascheramento da usare. I campi designati possono essere definiti tramite un nome di tabella e un nome di colonna del database o un nome alias.

* **Mascheramento per**: può essere eseguito nell'origine o nella destinazione. Il mascheramento può essere configurato a livello dell'origine identificando il nome di **tabella** e il nome di **colonna** o a livello dei risultati identificando il nome **Alias** usato nella query. Se si ha familiarità con l'architettura dei dati del database e si desidera limitare l'esposizione di tutti i risultati della query, è preferibile usare una regola di mascheramento dell'origine. Per limitare l'esposizione dei risultati della query senza analizzare l'architettura dei dati del database o per un campo che può essere generato da origini diverse, è consigliabile aggiungere una regola di mascheramento a livello dei risultati.  
  
* **Restrizione estesa**: limita l'esposizione dei dati sensibili, ma può compromettere la funzionalità di alcune applicazioni.

>[AZURE.TIP] Usare l'opzione **Restrizione estesa** per limitare l'accesso degli gli sviluppatori che usano l'accesso diretto al database ed eseguono query SQL per la risoluzione dei problemi.

* **Funzioni di mascheramento**: un set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione di mascheramento | Logica di mascheramento |
|----------|---------------|
| **Predefinita** |**Mascheramento completo in base ai tipi di dati dei campi designati**<br/><br/>• Usare XXXXXXXX o un numero minore di X se la dimensione del campo è inferiore a 8 caratteri per i tipi di dati di stringa (nchar, ntext, nvarchar).<br/>• Usare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Usare l'ora corrente per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per sql_variant viene usato il valore predefinito del tipo corrente.<br/>• Per XML viene usato il documento <masked/>.<br/>• Usare un valore vuoto per i tipi di dati speciali (timestamp  table, hierarchyid, GUID, binary, image, varbinary spatial).
| **Carta di credito** |**Metodo di mascheramento che espone le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numero di previdenza sociale** |**Metodo di mascheramento che espone le ultime due cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di numero di previdenza sociale americano.<br/><br/>XXX-XX-XX12 |
| **E-mail** | **Metodo di mascheramento che espone la prima lettera e il dominio** usando una stringa costante come prefisso sotto forma di indirizzo e-mail.<br/><br/>aXX@XXXX.com |
| **Numero casuale** | **Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione di mascheramento sarà un numero costante.<br/><br/>![Pannello di navigazione][Immagine1] |
| **Testo personalizzato** | **Metodo di mascheramento che espone la prima e l'ultima lettera** e aggiunge una stringa di riempimento personalizzata al centro.<br/>prefisso[riempimento]suffisso<br/><br/>![Pannello di navigazione][Immagine2] |

  
<a name="Anchor1"></a>
### Stringa di connessione con sicurezza abilitata

Quando si configura il mascheramento dei dati dinamici, Azure fornisce una stringa di connessione con sicurezza abilitata per il database. I dati sensibili vengono mascherati in base ai criteri di mascheramento dei dati dinamici solo per i client che usano questa stringa di connessione. È inoltre necessario aggiornare i client esistenti (ad esempio, le applicazioni) per usare il nuovo formato della stringa di connessione.

* Formato della stringa di connessione originale: <*nome server*>.database.windows.net
* Stringa di connessione con sicurezza abilitata: <*nome server*>.database.**secure**.windows.net

È inoltre possibile modificare l'**ACCESSO CON SICUREZZA ABILITATA** da **FACOLTATIVO** a **OBBLIGATORIO**. In questo modo non sarà possibile accedere al database con la stringa di connessione originale e ignorare i criteri di mascheramento dei dati dinamici. Per esercitarsi con l'uso di client specifici per il mascheramento dei dati dinamici (ad esempio, un'applicazione SSMS o in fase di sviluppo), scegliere **FACOLTATIVO**. Per la produzione, scegliere **OBBLIGATORIO**.<br/><br/>

![Navigation pane][Image3]<br/><br/>

## Configurare il mascheramento dei dati dinamici per il database tramite il portale di Azure

1. Avviare il portale di Azure in [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Fare clic sul database che si desidera mascherare e quindi sulla scheda **CONTROLLO E SICUREZZA**.

3. In **Mascheramento dei dati dinamici** fare clic su **ABILITATO** per abilitare la funzionalità di mascheramento dei dati dinamici.  

4. Digitare gli account di accesso con privilegi che devono disporre dell'accesso ai dati sensibili non mascherati.

	>[AZURE.TIP] Per fare in modo che il livello dell'applicazione consenta la visualizzazione dei dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'account di accesso all'applicazione usato per l'esecuzione di query e il database. È consigliabile che l'elenco contenga un numero limitato di account di accesso per ridurre al minimo l'esposizione dei dati sensibili.

	![Pannello di navigazione][Immagine4]

5. Nella parte inferiore della pagina, nella barra dei menu, fare clic su **Aggiungi MASCHERA** per aprire la finestra di configurazione delle regole di mascheramento.

6. Scegliere **Maschera per** per indicare se il mascheramento viene eseguito nell'origine o nella destinazione. Il mascheramento può essere configurato a livello dell'origine identificando il nome di **tabella** e il nome di **colonna** o a livello dei risultati identificando il nome **Alias** usato nella query. Se si ha familiarità con l'architettura dei dati del database e si desidera limitare l'esposizione di tutti i risultati della query, è preferibile usare una regola di mascheramento dell'origine. Per limitare l'esposizione dei risultati della query senza analizzare l'architettura dei dati del database o per un campo che può essere generato da origini diverse, è consigliabile aggiungere una regola di mascheramento a livello dei risultati.

7. Digitare il nome di **tabella** e il nome di **colonna** o il nome **Alias** per definire i campi designati che verranno mascherati.

8. Scegliere una **FUNZIONE DI MASCHERAMENTO** dall'elenco di categorie di mascheramento dei dati sensibili.

	![Pannello di navigazione][Immagine5] 
 	
9. Fare clic su **Aggiorna** nella finestra delle regole di mascheramento dei dati per aggiornare il set di regole di mascheramento nei criteri di mascheramento dei dati dinamici.

10. È consigliabile selezionare **USA RESTRIZIONI ESTESE** per limitare l'esposizione dei dati sensibili tramite query ad hoc.

11. Fare clic su **SALVA** per salvare la regola di mascheramento nuova o aggiornata.

## Configurare il mascheramento dei dati dinamici per il database usando l'API REST

Vedere [Operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/dn505719.aspx).

[Image1]: ./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png
[Image2]: ./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png
[Image3]: ./media/sql-database-dynamic-data-masking-get-started-portal/3_DDM_Current_Preview.png
[Image4]: ./media/sql-database-dynamic-data-masking-get-started-portal/4_DMM_Policy_Classic_Portal.png
[Image5]: ./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png

<!--HONumber=49--> 