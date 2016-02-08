<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni | Microsoft Azure"
   description="Questo argomento descrive le attività operative per il servizio di sincronizzazione Azure AD Connect e come prepararsi per il funzionamento di questo componente."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni
L'obiettivo di questo argomento è descrivere le attività operative per il servizio di sincronizzazione Azure AD Connect.

## Modalità di gestione temporanea
La modalità di gestione temporanea può essere usata per diversi scenari, ad esempio:

-	Disponibilità elevata.
-	Testare e distribuire le nuove modifiche della configurazione.
-	Introdurre un nuovo server e rimuovere quello vecchio.

Con un server in modalità di gestione temporanea è possibile apportare modifiche alla configurazione e visualizzarle in anteprima prima di attivare il server. È anche possibile eseguire operazioni di importazione e sincronizzazione complete per verificare che tutte le modifiche siano previste prima di introdurle nell'ambiente di produzione.

Durante l'installazione è possibile selezionare la **modalità di gestione temporanea** per il server. In questo modo il server sarà attivo per le operazioni di importazione e sincronizzazione, ma non potrà eseguire esportazioni. Un server in modalità di gestione temporanea non eseguirà la sincronizzazione password né abiliterà il writeback delle password, anche se si selezionano queste funzionalità. Quando si disabilita la modalità di gestione temporanea, il server avvierà l'esportazione e abiliterà la sincronizzazione password e il writeback delle password (se abilitati).

Un server in modalità di gestione temporanea continuerà a ricevere modifiche da Active Directory e Azure AD. Avrà quindi sempre una copia delle ultime modifiche e potrà così assumere velocemente le responsabilità di un altro server. Se si apportano modifiche alla configurazione del server primario, è necessario apportare le stesse modifiche ai server in modalità di gestione temporanea.

Coloro che hanno una conoscenza delle tecnologie di sincronizzazione precedenti tengano presente che la modalità di gestione temporanea è diversa, perché il server ha il proprio database SQL. Il server in modalità di gestione temporanea può quindi trovarsi in un altro data center.

### Verificare la configurazione di un server
Per applicare questo metodo, seguire questa procedura:

1. Preparare
2. Importare e sincronizzare
3. Verificare
4. Cambiare il server attivo

**Preparare**

1. Installare AD Azure Connect, selezionare **Modalità di gestione temporanea** e deselezionare **Avvia sincronizzazione** nell'ultima pagina dell'installazione guidata. In questo modo si potrà eseguire manualmente il motore di sincronizzazione. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Disconnettersi e accedere, quindi dal menu Start selezionare **Servizio di sincronizzazione**.

**Importare e sincronizzare**

1. Selezionare **Connettori** e quindi selezionare il primo connettore con il tipo **Servizi di dominio Active Directory**. Fare clic su **Esegui**, selezionare **Importazione completa** e fare clic su **OK**. Eseguire questa operazione per tutti i connettori di questo tipo.
2. Selezionare il connettore con il tipo **Azure Active Directory (Microsoft)**. Fare clic su **Esegui**, selezionare **Importazione completa** e fare clic su **OK**.
4. Assicurarsi che Connettori sia ancora selezionata e per ogni connettore con il tipo **Servizi di dominio Active Directory** fare clic su **Esegui**, selezionare **Sincronizzazione differenziale** e fare clic su **OK**.
5. Selezionare il connettore con il tipo **Azure Active Directory** (Microsoft). Fare clic su **Esegui**, selezionare **Sincronizzazione differenziale** e quindi fare clic su OK.

È stata eseguita l'esportazione delle modifiche in modalità di gestione temporanea in Azure AD e in AD locale (se si usa una distribuzione ibrida di Exchange). I passaggi successivi consentiranno di ispezionare quali sono gli elementi che stanno per essere modificati prima di avviare effettivamente l'esportazione nelle directory.

**Verificare**

1. Avviare un prompt dei comandi e passare a `%Program Files%\Microsoft Azure AD Sync\bin`
2. Eseguire: `csexport "Name of Connector" %temp%\export.xml /f:x`<BR/> Si può trovare il nome del connettore nel servizio di sincronizzazione. Il nome sarà simile a "contoso.com - AAD" per Azure AD.
3. Eseguire: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. A questo punto si avrà un file denominato export.csv in %temp%, che può essere esaminato in Microsoft Excel. Questo file contiene tutte le modifiche in fase di esportazione.
5. Apportare le modifiche necessarie ai dati o alla configurazione ed eseguire di nuovo questi passaggi (Importare e sincronizzare e Verificare), finché le modifiche che stanno per essere esportate saranno quelle previste.

**Informazioni sul file export.csv**

La maggior parte del file è facilmente comprensibile. Ecco alcune abbreviazioni per comprendere il contenuto:

- OMODT: Object Modification Type. Indica se l'operazione a livello di oggetto è un'aggiunta, un aggiornamento o un'eliminazione.
- AMODT: Attribute Modification Type. Indica se l'operazione a livello di attributo è un'aggiunta, un aggiornamento o un'eliminazione.

Se si tratta di un attributo multivalore, non tutte le modifiche sono visualizzate. Sarà visibile solo il numero di valori aggiunti e rimossi.

**Cambiare il server attivo**

1. Disattivare il server attualmente attivo (DirSync/FIM/Azure AD Sync), in modo che non esegua l'esportazione in Azure AD oppure impostarlo in modalità di gestione temporanea (Azure AD Connect).
2. Eseguire l'installazione guidata nel server in **modalità di gestione temporanea** e disabilitare **questa modalità**.![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Ripristino di emergenza
Come parte della progettazione dell'implementazione si pianificheranno le operazioni da eseguire nel caso di un'emergenza che causa la perdita del server di sincronizzazione. Ci sono diversi modelli da usare e quale si sceglierà dipende da diversi fattori, ad esempio:

-	Qual è la tolleranza per l'impossibilità di apportare modifiche agli oggetti in Azure AD durante il tempo di inattività?
-	Se si usa la sincronizzazione password, gli utenti accetteranno di dover usare la vecchia password in Azure AD nel caso la modifichino in locale?
-	Si ha una dipendenza dalle operazioni in tempo reale, ad esempio il writeback delle password?

A seconda delle risposte a queste domande e ai criteri dell'organizzazione, si può implementare una delle strategie seguenti:

-	Ricompilare quando necessario.
-	Avere un server di standby di riserva, ovvero in **modalità di gestione temporanea**.
-	Usare macchine virtuali.

Poiché il servizio di sincronizzazione Azure AD Connect ha una dipendenza in un database SQL, è consigliabile esaminare anche la sezione Disponibilità elevata di SQL se non si usa SQL Express, incluso in Azure AD Connect.

### Ricompilare quando necessario
Una strategia valida consiste nel pianificare la ricompilazione di un server in caso di necessità. In molti casi l'installazione del motore di sincronizzazione e l'esecuzione dell'importazione iniziale e della sincronizzazione possono essere completate in poche ore. Se non è disponibile un server di riserva, è possibile usare temporaneamente un controller di dominio per ospitare il motore di sincronizzazione.

Il server del motore di sincronizzazione non archivia lo stato degli oggetti, quindi il database può essere ricompilato dai dati disponibili in Active Directory e Azure AD. L'attributo **sourceAnchor** viene usato per aggiungere gli oggetti sia locali che dal cloud. Se si ricompila il server con gli oggetti esistenti in locale e nel cloud, questi saranno abbinati di nuovo dal motore di sincronizzazione al momento della reinstallazione. Gli elementi che occorre documentare e salvare sono le modifiche della configurazione apportate al server, ad esempio le regole di filtro e sincronizzazione, che dovranno essere riapplicate prima di avviare la sincronizzazione.

### Avere un server di standby di riserva, in modalità di gestione temporanea
Nel caso di un ambiente più complesso, è consigliabile avere uno o più server di standby. Durante l'installazione è possibile abilitare un server in **modalità di gestione temporanea**.

Per altri dettagli, vedere [modalità di gestione temporanea](#staging-mode).

### Usare macchine virtuali
Un metodo comune e supportato consiste nell'eseguire il motore di sincronizzazione in una macchina virtuale. Nel caso di un problema dell'host, è possibile eseguire la migrazione dell'immagine del server del motore di sincronizzazione in un altro server.

### Disponibilità elevata di SQL
Se non si usa SQL Server Express, fornito con Azure AD Connect, è necessario prendere in considerazione anche la disponibilità elevata per SQL Server. La sola soluzione a disponibilità elevata è il clustering SQL. Le soluzioni non supportate includono il mirroring e AlwaysOn.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->