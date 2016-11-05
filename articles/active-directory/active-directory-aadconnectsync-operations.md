---
title: 'Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni | Microsoft Docs'
description: Questo argomento descrive le attività operative per il servizio di sincronizzazione Azure AD Connect e come prepararsi per il funzionamento di questo componente.
services: active-directory
documentationcenter: ''
author: AndKjell
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2016
ms.author: andkjell

---
# Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni
L'obiettivo di questo argomento è descrivere le attività operative per il servizio di sincronizzazione Azure AD Connect.

## Modalità di gestione temporanea
La modalità di gestione temporanea può essere usata per diversi scenari, ad esempio:

* Disponibilità elevata.
* Testare e distribuire le nuove modifiche della configurazione.
* Introdurre un nuovo server e rimuovere quello vecchio.

Con un server in modalità di gestione temporanea è possibile apportare modifiche alla configurazione e visualizzarle in anteprima prima di attivare il server. È anche possibile eseguire operazioni di importazione e sincronizzazione complete per verificare che tutte le modifiche siano previste prima di introdurle nell'ambiente di produzione.

Durante l'installazione è possibile selezionare la **modalità di gestione temporanea** per il server. In questo modo il server sarà attivo per le operazioni di importazione e sincronizzazione, ma non eseguirà esportazioni. Un server in modalità di gestione temporanea non eseguirà la sincronizzazione o il writeback delle password, anche se queste funzionalità sono state selezionate durante l'installazione. Quando si disabilita la modalità di gestione temporanea, il server avvia l'esportazione e abilita la sincronizzazione e il writeback delle password.

È comunque possibile forzare un’esportazione utilizzando Synchronization Service Manager.

Un server in modalità di gestione temporanea continua a ricevere modifiche da Active Directory e Azure AD. Ha sempre una copia delle modifiche più recenti e può così assumere velocemente le funzioni di un altro server. Se si apportano modifiche alla configurazione del server primario, è necessario apportare le stesse modifiche al server in modalità di gestione temporanea.

Coloro che hanno una conoscenza delle tecnologie di sincronizzazione precedenti tengano presente che la modalità di gestione temporanea è diversa, perché il server ha il proprio database SQL. L'architettura consente al server in modalità di gestione temporanea di trovarsi in un altro data center.

### Verificare la configurazione di un server
Per applicare questo metodo, seguire questa procedura:

1. [Preparare](#prepare)
2. [Importare e sincronizzare](#import-and-synchronize)
3. [Verificare](#verify)
4. [Cambiare il server attivo](#switch-active-server)

#### Preparazione
1. Installare AD Azure Connect, selezionare **Modalità di gestione temporanea** e deselezionare **Avvia sincronizzazione** nell'ultima pagina dell'installazione guidata. In questo modo è possibile eseguire manualmente il motore di sincronizzazione. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Disconnettersi e accedere, quindi dal menu Start selezionare **Synchronization Service** (Servizio di sincronizzazione).

#### Importare e sincronizzare
1. Selezionare **Connettori** e quindi selezionare il primo connettore con il tipo **Servizi di dominio Active Directory**. Fare clic su **Run** (Esegui), selezionare **Full import** (Importazione completa) e fare clic su **OK**. Eseguire questi passaggi per tutti i connettori di questo tipo.
2. Selezionare il connettore con il tipo **Azure Active Directory (Microsoft)**. Fare clic su **Run** (Esegui), selezionare **Full import** (Importazione completa) e fare clic su **OK**.
3. Assicurarsi che la scheda Connectors (Connettori) sia ancora selezionata. Per ogni connettore con il tipo **Active Directory Domain Services** (Servizi di dominio Active Directory) fare clic su **Run** (Esegui), selezionare **Delta Synchronization** (Sincronizzazione delta) e fare clic su **OK**.
4. Selezionare il connettore con il tipo **Azure Active Directory (Microsoft)**. Fare clic su **Run** (Esegui), selezionare **Delta Synchronization** (Sincronizzazione delta) e quindi fare clic su **OK**.

È stata eseguita l'esportazione delle modifiche in modalità di gestione temporanea in Azure AD e in AD locale (se si usa una distribuzione ibrida di Exchange). I passaggi successivi consentono di ispezionare quali sono gli elementi che stanno per essere modificati prima di avviare effettivamente l'esportazione nelle directory.

#### Verificare
1. Avviare un prompt dei comandi e passare a `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Eseguire: `csexport "Name of Connector" %temp%\export.xml /f:x` Il nome del connettore si trova nel servizio di sincronizzazione. Il nome sarà simile a "contoso.com - AAD" per Azure AD.
3. Eseguire: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Si avrà un file denominato export.csv in %temp%, che può essere esaminato in Microsoft Excel. Questo file contiene tutte le modifiche in fase di esportazione.
5. Apportare le modifiche necessarie ai dati o alla configurazione ed eseguire di nuovo questi passaggi (importazione, sincronizzazione e verifica) finché le modifiche che verranno esportate non saranno quelle previste.

**Informazioni sul file export.csv**

La maggior parte del file è facilmente comprensibile. Ecco alcune abbreviazioni per comprendere il contenuto:

* OMODT: Object Modification Type. Indica se l'operazione a livello di oggetto è un'aggiunta, un aggiornamento o un'eliminazione.
* AMODT: Attribute Modification Type. Indica se l'operazione a livello di attributo è un'aggiunta, un aggiornamento o un'eliminazione.

Se si tratta di un attributo multivalore, non tutte le modifiche verranno visualizzate. Sarà visibile solo il numero di valori aggiunti e rimossi.

#### Cambiare il server attivo
1. Disattivare il server attualmente attivo (DirSync/FIM/Azure AD Sync), in modo che non esegua l'esportazione in Azure AD oppure impostarlo in modalità di gestione temporanea (Azure AD Connect).
2. Eseguire l'installazione guidata nel server in **modalità di gestione temporanea** e disabilitare **questa modalità**.![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Ripristino di emergenza
Nell'ambito della progettazione dell'implementazione vengono pianificate le operazioni da eseguire nel caso di un'emergenza che causa la perdita del server di sincronizzazione. Ci sono diversi modelli da usare e quello scelto dipende da diversi fattori, ad esempio:

* Qual è la tolleranza per l'impossibilità di apportare modifiche agli oggetti in Azure AD durante il tempo di inattività?
* Se si usa la sincronizzazione delle password, gli utenti accettano di dover usare la vecchia password in Azure AD nel caso la modifichino in locale?
* Si ha una dipendenza dalle operazioni in tempo reale, ad esempio il writeback delle password?

A seconda delle risposte a queste domande e dei criteri dell'organizzazione, è possibile implementare una delle strategie seguenti:

* Ricompilare quando necessario.
* Avere un server di standby di riserva, ovvero in **modalità di gestione temporanea**.
* Usare macchine virtuali.

Se non si usa il database di SQL Express predefinito, è opportuno vedere anche la sezione [Disponibilità elevata di SQL](#sql-high-availability).

### Ricompilare quando necessario
Una strategia valida consiste nel pianificare la ricompilazione di un server in caso di necessità. L'installazione del motore di sincronizzazione e l'esecuzione dell'importazione iniziale e della sincronizzazione possono essere generalmente completate in poche ore. Se non è disponibile un server di riserva, è possibile usare temporaneamente un controller di dominio per ospitare il motore di sincronizzazione.

Il server del motore di sincronizzazione non archivia lo stato degli oggetti, quindi il database può essere ricompilato dai dati disponibili in Active Directory e Azure AD. L'attributo **sourceAnchor** viene usato per aggiungere gli oggetti sia locali che dal cloud. Se si ricompila il server con gli oggetti esistenti in locale e nel cloud, il motore di sincronizzazione abbinerà di nuovo questi oggetti al momento della reinstallazione. Gli elementi che occorre documentare e salvare sono le modifiche della configurazione apportate al server, ad esempio le regole di filtro e sincronizzazione. Queste configurazioni personalizzate dovranno essere riapplicate prima di avviare la sincronizzazione.

### Avere un server di standby di riserva, in modalità di gestione temporanea
Nel caso di un ambiente più complesso, è consigliabile avere uno o più server di standby. Durante l'installazione è possibile abilitare un server in **modalità di gestione temporanea**.

Per altri dettagli, vedere [modalità di gestione temporanea](#staging-mode).

### Usare macchine virtuali
Un metodo comune e supportato consiste nell'eseguire il motore di sincronizzazione in una macchina virtuale. Nel caso di un problema dell'host, è possibile eseguire la migrazione dell'immagine del server del motore di sincronizzazione in un altro server.

### Disponibilità elevata di SQL
Se non si usa SQL Server Express fornito con Azure AD Connect, è necessario prendere in considerazione anche la disponibilità elevata per SQL Server. La sola soluzione a disponibilità elevata è il clustering SQL. Le soluzioni non supportate includono il mirroring e AlwaysOn.

## Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->