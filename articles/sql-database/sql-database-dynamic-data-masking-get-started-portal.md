<properties
   pageTitle="Introduzione alla maschera dati dinamica del database SQL (portale di Azure classico)"
   description="Introduzione al maschera dati dinamica del database SQL nel portale di Azure classico"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/11/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# Introduzione alla maschera dati dinamica del database SQL (portale di Azure classico)

> [AZURE.SELECTOR]
- [Maschera dati dinamica: portale di Azure](sql-database-dynamic-data-masking-get-started.md)

## Panoramica

Il mascheramento dei dati dinamici del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica è supportata per la versione 12 del database SQL di Azure.

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.

Ad esempio, un addetto all'assistenza in un call center può identificare i chiamanti da alcune cifre del codice fiscale o del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto dell'assistenza. È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del codice fiscale o del numero di carta di credito nel set di risultati di tutte le query. Oppure, è possibile definire una maschera dati appropriata per la protezione di informazioni personali identificabili (PII), in modo che uno sviluppatore possa eseguire una query negli ambienti di produzione a scopi di risoluzione dei problemi senza violare le normative di conformità.

## Nozioni fondamentali sul mascheramento dei dati dinamici del database SQL

Impostare i criteri di mascheramento dei dati dinamici per il database nel portale di Azure classico nella scheda Controllo e sicurezza


> [AZURE.NOTE] Per impostare un maschera dati dinamica nel portale di Azure, vedere [Introduzione al maschera dati dinamica del database SQL (portale di Azure)](sql-database-dynamic-data-masking-get-started.md).


### Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dall'amministratore del database di Azure, dall'amministratore del server o dal responsabile della sicurezza.

### Criteri di mascheramento dei dati dinamici

* **Utenti SQL esclusi dalla maschera**: set di utenti SQL o identità AAD che visualizzano dati senza maschera nei risultati delle query SQL. Si noti che gli utenti con privilegi di amministratore verranno sempre esclusi dalla maschera e potranno visualizzare i dati originali senza maschera.

* **Regole di maschera**: set di regole che definiscono i campi designati a cui applicare la maschera e la funzione maschera da usare. I campi designati possono essere definiti tramite uno schema, un nome di tabella e un nome di colonna del database.

* **Funzioni maschera**: set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione di mascheramento | Logica di mascheramento |
|----------|---------------|
| **Default** |**Maschera completa in base ai tipi di dati dei campi designati**<br/><br/>• Usare XXXX o meno X se la dimensione del campo è minore di 4 caratteri per i tipi di dati della stringa (nchar, ntext, nvarchar).<br/>• Usare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Usare 01-01-1900 per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per la variante SQL, viene usato il valore predefinito del tipo corrente.<br/>• Per XML viene usato il documento <masked/>.<br/>• Usare un valore vuoto per i tipi di dati speciali (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types).
| **Carta di credito** |**Metodo di maschera che rende visibili le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Codice fiscale** |**Metodo di maschera che rende visibili le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di codice fiscale.<br/><br/>XXX-XX-1234 |
| **Indirizzo di posta elettronica** | **Metodo di maschera che rende visibile la prima lettera e sostituisce il dominio con XXX.com** usando un prefisso stringa costante sotto forma di indirizzo di posta elettronica.<br/><br/>aXX@XXXX.com |
| **Numero casuale** | **Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione maschera sarà un numero costante.<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Testo personalizzato** | **Metodo di maschera che rende visibile il primo e l'ultimo carattere** e aggiunge una stringa di riempimento personalizzata al centro. Se la stringa originale è più corta del prefisso e del suffisso visibili, verrà usata solo la stringa di riempimento.<br/>prefisso[riempimento]suffisso<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## Configurare il mascheramento dei dati dinamici per il database tramite il portale di Azure classico

1. Avviare il portale di Azure classico all'indirizzo [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Fare clic sul database a cui si desidera applicare la maschera e quindi sulla scheda **CONTROLLO E SICUREZZA**.

3. In **Maschera dati dinamica** fare clic su **ABILITATA** per abilitare la funzionalità di maschera dati dinamica.

4. Digitare gli utenti SQL o le identità AAD da escludere dalla maschera e che hanno accesso ai dati sensibili senza maschera. Deve trattarsi di un elenco di utenti separati da punto e virgola. Si noti che gli utenti con privilegi di amministratore dispongono sempre dell'accesso ai dati originali senza maschera.

	>[AZURE.TIP] Per fare in modo che il livello dell'applicazione consenta la visualizzazione dei dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'utente SQL o l'identità AAD usata dall'applicazione per eseguire query nel database. È altamente consigliabile che l'elenco contenga un numero limitato di utenti con privilegi per ridurre al minimo l'esposizione dei dati sensibili.

	![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Nella parte inferiore della pagina nella barra dei menu fare clic su **Aggiungi maschera** per aprire la finestra di configurazione delle regole di maschera.

6. Selezionare **Schema**, **Tabella** e **Colonna** dagli elenchi a discesa per definire i campi designati a cui applicare la maschera.

7. Scegliere una **FUNZIONE MASCHERA** dall'elenco di categorie maschera dei dati sensibili.

	![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Fare clic su **OK** nella finestra delle regole di maschera dei dati per aggiornare il set di regole di maschera nei criteri della maschera dati dinamica.

9. Fare clic su **SALVA** per salvare il criterio di maschera nuovo o aggiornato.


## Impostare la maschera dei dati dinamica per il database usando le istruzioni Transact-SQL

Vedere [Maschera dati dinamica](https://msdn.microsoft.com/library/mt130841.aspx).

## Configurare il mascheramento dei dati dinamici per il database usando i cmdlet di PowerShell.

Vedere [Cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## Configurare il mascheramento dei dati dinamici per il database usando l'API REST

Vedere [Operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0413_2016-->