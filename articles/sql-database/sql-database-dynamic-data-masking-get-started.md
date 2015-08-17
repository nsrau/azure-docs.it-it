<properties 
   pageTitle="Introduzione al mascheramento dei dati dinamici del database SQL (portale di anteprima di Azure)" 
   description="Introduzione al mascheramento dei dati dinamici del database SQL nel portale di anteprima di Azure" 
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
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Introduzione al mascheramento dei dati dinamici del database SQL (portale di anteprima di Azure)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Panoramica

Il mascheramento dei dati dinamici del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. Il mascheramento dei dati dinamici è in anteprima per i livelli di servizio Basic, Standard e Premium nella versione V12 del database SQL di Azure.

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.

Ad esempio, un addetto del call center può identificare i chiamanti da alcune cifre del numero di previdenza sociale o del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto del supporto tecnico. Uno sviluppatore può definire una regola di mascheramento da applicare a ciascun risultato di query per rendere visibili solo le ultime quattro cifre del numero di previdenza sociale o di carta di credito nel set di risultati. Oppure, mediante la maschera di dati appropriata per la protezione di dati con informazioni personali (PII), uno sviluppatore può eseguire una query negli ambienti di produzione per la risoluzione dei problemi senza violare le normative di conformità.

## Nozioni fondamentali sul mascheramento dei dati dinamici del database SQL

Per impostare un criterio di mascheramento dei dati dinamici nel portale di anteprima di Azure, selezionare l’operazione di mascheramento dei dati dinamici nel pannello di configurazione del database SQL. Prima di impostare il mascheramento dei dati dinamici verificare se si sta utilizzando un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


### Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dall'amministratore del database di Azure, dall'amministratore del server o dal responsabile della sicurezza.

### Criteri di mascheramento dei dati dinamici

* **Account di accesso con privilegi**: un set di account di accesso per i quali i dati mascherati saranno visibili nei risultati delle query SQL.
  
* **Regole di mascheramento**: un set di regole che definiscono i campi designati da nascondere e la funzione di mascheramento da usare. I campi designati possono essere definiti tramite un nome di tabella e un nome di colonna del database.

* **Funzioni di mascheramento**: un set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione di mascheramento | Logica di mascheramento |
|----------|---------------|
| **Default** |**Mascheramento completo in base ai tipi di dati dei campi designati**<br/><br/>• Utilizzare XXXXXXXX o meno X se la dimensione del campo è minore di 8 caratteri per i tipi di dati (nchar, ntext, nvarchar) della stringa.<br/>• Utilizzare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Utilizzare 01-01-1900 per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per la variante SQL, è utilizzato il valore predefinito del tipo corrente.<br/>• Per XML <masked/> viene utilizzato il documento.<br/>• Utilizzare un valore vuoto per i tipi di dati speciali (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types).
| **Carta di credito** |**Metodo di mascheramento che espone le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numero di previdenza sociale** |**Metodo di mascheramento che espone le ultime due cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di numero di previdenza sociale americano.<br/><br/>XXX-XX-XX12 |
| **Email** | **Metodo di mascheramento che espone la prima lettera e sostituisce il dominio con XXX.com** usando una stringa costante come prefisso sotto forma di indirizzo e-mail.<br/><br/>aXX@XXXX.com |
| **Numero casuale** | **Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione di mascheramento sarà un numero costante.<br/><br/>![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Testo personalizzato** | **Metodo di mascheramento che espone il primo e l'ultimo carattere** e aggiunge una stringa di riempimento personalizzata al centro.<br/>prefix[padding]suffix<br/><br/>![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### Stringa di connessione con sicurezza abilitata

Se si utilizza un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), allora è necessario aggiornare i client esistenti (esempio: applicazioni) per utilizzare un formato di stringa di connessione modificato. Per informazioni dettagliate, fare clic [qui](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

## Configurare il mascheramento dei dati dinamici per il database tramite il portale di anteprima di Azure

1. Avviare il portale di anteprima di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
	 
2. Passare al pannello di configurazione del database che include i dati sensibili che si desidera mascherare.
	
3. Fare clic sul riquadro **Mascheramento dei dati dinamici**. Verrà aperto il pannello di configurazione **Mascheramento dei dati dinamici**.

	* In alternativa, è possibile scorrere verso il basso fino alla sezione **Operazioni** e fare clic su **Mascheramento dei dati dinamici**.
	 
	![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. Nel pannello di configurazione **Mascheramento dei dati dinamici**, fare clic su **Aggiungi maschera**per aprire il pannello di configurazione **Aggiungi regola di mascheramento**.

	![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. Selezionare la **tabella** e la **colonna**, per definire i campi designati che verranno mascherati.

6. Scegliere un **Formato maschera del campo** dall'elenco di categorie di mascheramento dei dati sensibili.

	![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. Fare clic su **Salva** nel pannello delle regole di mascheramento dei dati per aggiornare il set di regole di mascheramento nei criteri di mascheramento dei dati dinamici.

8. Digitare gli account di accesso con privilegi che devono disporre dell'accesso ai dati sensibili non mascherati.
 
	![Pannello di navigazione](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]Per fare in modo che il livello dell'applicazione consenta la visualizzazione dei dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'account di accesso SQL dell'applicazione usato per l'esecuzione di query nel database. È altamente consigliabile che l'elenco contenga un numero limitato di account di accesso per ridurre al minimo l'esposizione dei dati sensibili.

9. Fare clic su **Salva** nel pannello di configurazione del mascheramento dei dati per salvare il criterio di mascheramento nuovo o aggiornato.

10. Se si utilizza un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), allora è necessario aggiornare i client esistenti (esempio: applicazioni) per utilizzare un formato di stringa di connessione modificato. Per ulteriori informazioni, vedere [Client di livello inferiore](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

## Configurare il mascheramento dei dati dinamici per il database usando i cdmlet di Powershell.

Vedere [Cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx).


## Configurare il mascheramento dei dati dinamici per il database usando l'API REST

Vedere [Operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=August15_HO6-->