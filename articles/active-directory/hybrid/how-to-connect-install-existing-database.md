---
title: Installare Azure AD Connect usando un database ADSync esistente | Microsoft Docs
description: Questo argomento descrive come usare un database ADSync esistente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: f863c4b115616ff709634f3c68955c3be3241707
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494188"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installare Azure AD Connect usando un database ADSync esistente
Per archiviare i dati, Azure AD Connect richiede un database di SQL Server. È possibile usare l'istanza predefinita di Local DB di SQL Server 2012 Express installata con Azure AD Connect oppure usare la versione completa di SQL. Con le versioni precedenti, durante l'installazione di Azure AD Connect viene sempre creato un nuovo database denominato ADSync. Con la versione 1.1.613.0 o successiva, è possibile scegliere di installare Azure AD Connect associandolo a un database ADSync esistente.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Vantaggi offerti dall'utilizzo di un database ADSync
L'associazione a un database ADSync esistente offre i vantaggi seguenti:

- Fatta eccezione per i dati delle credenziali, la configurazione di sincronizzazione archiviata nel database ADSync (incluse le regole di sincronizzazione personalizzate, i connettori, i filtri e la configurazione delle funzionalità facoltative) viene recuperata automaticamente e usata durante l'installazione. Le credenziali usate da Azure AD Connect per sincronizzare le modifiche con le istanze locali di AD e Azure AD sono crittografate e sono accessibili solo dal server di Azure AD Connect precedente.
- Vengono inoltre recuperati tutti i dati sulle identità (associati agli spazi connettore e al metaverse) e i cookie di sincronizzazione archiviati nel database ADSync. Il nuovo server di Azure AD Connect installato può riprendere la sincronizzazione dal punto in cui è stata interrotta dal server di Azure AD Connect precedente anziché eseguire una sincronizzazione completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenari in cui è utile usare un database ADSync esistente
Questi vantaggi sono utili negli scenari seguenti:


- Si ha già una distribuzione di Azure AD Connect. Il server di Azure AD Connect esistente non funziona più, ma l'istanza di SQL Server che contiene il database ADSync è ancora funzionante. È possibile installare un nuovo server di Azure AD Connect e associarlo al database ADSync esistente. 
- Si ha già una distribuzione di Azure AD Connect. L'istanza di SQL Server che contiene il database ADSync non funziona più, ma si ha un backup recente del database. È possibile innanzitutto ripristinare il database ADSync in una nuova istanza di SQL Server e quindi installare un nuovo server di Azure AD Connect e associarlo al database ADSync ripristinato.
- Si ha già una distribuzione di Azure AD Connect che usa Local DB. A causa del limite di 10 GB imposto da Local DB, si vuole eseguire la migrazione alla versione completa di SQL. È possibile eseguire il backup del database ADSync da Local DB, ripristinarlo in un'istanza di SQL Server e quindi reinstallare un nuovo server di Azure AD Connect e associarlo al database ADSync ripristinato.
- Si sta provando a configurare un server di staging e si vuole avere la certezza che la configurazione corrisponda a quella del server attivo corrente. È possibile eseguire il backup del database ADSync, ripristinarlo in un'altra istanza di SQL Server e quindi reinstallare un nuovo server di Azure AD Connect e associarlo al database ADSync ripristinato.

## <a name="prerequisite-information"></a>Informazioni sui prerequisiti

Informazioni importanti da tenere presenti prima di procedere:

- Assicurarsi di esaminare i prerequisiti hardware e le informazioni sull'account e sulle autorizzazioni che è necessario avere per installare Azure AD Connect. Le autorizzazioni necessarie per installare Azure AD Connect usando un database esistente sono identiche a quelle per l'installazione personalizzata.
- La distribuzione di Azure AD Connect rispetto a un database ADSync esistente è supportata solo con SQL completo. Non è supportata con un Local DB di SQL Express. Se si dispone di un database ADSync esistente nel Local DB che si vuole usare, è necessario innanzitutto eseguire il backup del database ADSync (database locale) e ripristinarlo a SQL completo. Dopo questo processo, è possibile distribuire Azure AD Connect nel database ripristinato con questo metodo.
- La versione di Azure AD Connect usata per l'installazione deve soddisfare i criteri seguenti:
    - 1.1.613.0 o successiva E
    - Uguale o superiore all'ultima versione di Azure AD Connect usata con il database ADSync. Se la versione di Azure AD Connect usata per l'installazione è superiore all'ultima versione usata con il database ADSync, può essere necessario eseguire una sincronizzazione completa.  Questa operazione è necessaria in caso di modifiche allo schema o alle regole di sincronizzazione tra le due versioni. 
- Lo stato di sincronizzazione del database ADSync usato deve essere relativamente recente. L'ultima attività di sincronizzazione con il database ADSync esistente deve essere avvenuta nelle ultime tre settimane.
- Quando si installa Azure AD Connect usando un database esistente, il metodo di accesso configurato nel server di Azure AD Connect precedente non viene mantenuto. Non è inoltre possibile configurare il metodo di accesso durante l'installazione, ma solo al termine dell'installazione.
- Uno stesso database ADSync non può essere condiviso da più server di Azure AD Connect. Il metodo di installazione che prevede l'utilizzo di un database esistente consente di riusare un database ADSync esistente con un nuovo server di Azure AD Connect, ma non supporta la condivisione.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Passaggi per installare Azure AD Connect usando un database esistente
1.  Scaricare il programma di installazione di Azure AD Connect (AzureADConnect.MSI) in Windows Server. Fare doppio clic sul programma di installazione di Azure AD Connect per avviare l'operazione.
2.  Al termine dell'installazione del file con estensione msi, viene avviata la procedura guidata per l'installazione di Azure AD Connect in modalità rapida. Chiudere la schermata facendo clic sull'icona di chiusura.
![Schermata iniziale](./media/how-to-connect-install-existing-database/db1.png)
3.  Avviare un nuovo prompt dei comandi o una nuova sessione di PowerShell. Passare alla cartella <drive>\Programmi\Microsoft Azure AD Connect. Eseguire il comando .\AzureADConnect.exe /useexistingdatabase per avviare la procedura guidata per l'installazione di Azure AD Connect con un database esistente.
![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
4.  Viene visualizzata la schermata iniziale di Azure AD Connect. Accettare le condizioni di licenza e l'informativa sulla privacy e quindi fare clic su **Continua**.
![Schermata iniziale](./media/how-to-connect-install-existing-database/db3.png)
5.  Nella schermata **Installazione dei componenti necessari** l'opzione **Usa un'istanza di SQL Server esistente** è abilitata. Specificare il nome dell'istanza di SQL Server che ospita il database ADSync. Se l'istanza del motore SQL usata per ospitare il database ADSync non è quella predefinita in SQL Server, è necessario specificarne il nome. Inoltre, se l'esplorazione di SQL non è abilitata, è necessario specificare il numero di porta dell'istanza del motore SQL, Ad esempio:          
![Schermata iniziale](./media/how-to-connect-install-existing-database/db4.png)           

6.  Nella schermata **Connessione ad Azure AD** è necessario specificare le credenziali di amministratore globale della directory di Azure AD. È consigliabile usare un account nel dominio onmicrosoft.com predefinito. Questo account viene usato solo per creare un account di servizio in Azure AD e al termine della procedura guidata non viene più usato.
![Connettere](./media/how-to-connect-install-existing-database/db5.png)
 
7.  Nella schermata **Connessione delle directory** la foresta di Active Directory esistente, configurata per la sincronizzazione delle directory, è visualizzata con accanto una croce rossa. Per sincronizzare le modifiche da una foresta locale di Active Directory è necessario un account di Active Directory Domain Services. La procedura guidata di Azure AD Connect non è in grado di recuperare le credenziali dell'account di Active Directory Domain Services archiviate nel database ADSync perché sono crittografate e possono essere decrittografate solo dal server di Azure AD Connect precedente. Fare clic su **Cambia credenziali** per specificare l'account di Active Directory Domain Services per la foresta di Active Directory.
![Directories](./media/how-to-connect-install-existing-database/db6.png)
 
 
8.  Nella finestra di dialogo popup è possibile (i) specificare le credenziali di amministratore dell'organizzazione e consentire ad Azure AD Connect di creare automaticamente l'account di Active Directory Domain Services oppure (ii) creare manualmente l'account di Active Directory Domain Services e specificarne le credenziali per Azure AD Connect. Dopo aver selezionato un'opzione e specificato le credenziali necessarie, fare clic su **OK** per chiudere la finestra di dialogo popup.
![Schermata iniziale](./media/how-to-connect-install-existing-database/db7.png)
 
 
9.  Una volta specificate le credenziali, la croce rossa viene sostituita con un segno di spunta verde. Fare clic su **Avanti**.
![Schermata iniziale](./media/how-to-connect-install-existing-database/db8.png)
 
 
10. Nella schermata **Pronto per la configurazione** fare clic su **Installa**.
![Schermata iniziale](./media/how-to-connect-install-existing-database/db9.png)
 
 
11. Al termine dell'installazione, sul server di Azure AD Connect viene abilitata automaticamente la modalità di gestione temporanea. Prima di disabilitare questa modalità, è consigliabile esaminare la configurazione del server e le operazioni di esportazione in sospeso per eventuali modifiche impreviste. 

## <a name="post-installation-tasks"></a>Attività successive all'installazione
Quando si ripristina un backup del database creato da una versione di Azure AD Connect precedente a 1.2.65.0, il server di gestione temporanea selezionerà automaticamente il metodo di accesso **Non configurare**. Poiché le preferenze di sincronizzazione dell'hash delle password e di writeback delle password verranno ripristinate, è necessario di conseguenza modificare il metodo di accesso in modo che corrisponda agli altri criteri in vigore per il server di sincronizzazione attivo.  La mancata esecuzione di questi passaggi può impedire agli utenti di eseguire l'accesso se il server dovesse diventare attivo.  

Usare la tabella seguente per verificare gli eventuali passaggi aggiuntivi necessari.

|Funzionalità|Passaggi|
|-----|-----|
|Sincronizzazione hash password| Le impostazioni Sincronizzazione hash password e Writeback password sono completamente ripristinate per Azure AD Connect a partire dalla versione 1.2.65.0.  Se il ripristino viene effettuato con una versione precedente di Azure AD Connect, esaminare le impostazioni delle opzioni di sincronizzazione di queste funzionalità per verificare che abbiano una corrispondenza nel server di sincronizzazione attivo.  Non sono necessari altri passaggi di configurazione.|
|Federazione con ADFS|Le autenticazioni di Azure continueranno a usare i criteri AD FS configurati per il server di sincronizzazione attivo.  Se si usa Azure AD Connect per gestire la farm AD FS, è possibile facoltativamente impostare il metodo di accesso su Federazione tramite AD FS per preparare il server di standby a diventare l'istanza di sincronizzazione attiva.   Se nel server di sincronizzazione attivo sono abilitate le opzioni di dispositivo, configurare queste opzioni eseguendo l'attività Configura le opzioni del dispositivo.|
|Autenticazione pass-through e Single Sign-On desktop|Aggiornare il metodo di accesso in modo che corrisponda alla configurazione nel server di sincronizzazione attivo.  Se non si esegue questa operazione prima di alzare il server al livello di server primario, l'autenticazione pass-through con Accesso Single Sign-On facile verrà disabilitata e il tenant potrebbe essere bloccato se l'opzione di accesso Sincronizzazione hash password non è stata impostata come backup. Si noti inoltre che quando si abilita l'autenticazione pass-through in modalità di gestione temporanea, un nuovo agente di autenticazione verrà installato, registrato ed eseguito come agente a disponibilità elevata che accetta le richieste di accesso.|
|Federazione con PingFederate|Le autenticazioni di Azure continueranno a usare i criteri PingFederate configurati per il server di sincronizzazione attivo.  È possibile facoltativamente impostare il metodo di accesso su Federazione con PingFederate per preparare il server di standby a diventare l'istanza di sincronizzazione attiva.  Questo passaggio può essere rinviato fino a quando non è necessario attuare la federazione di domini aggiuntivi con PingFederate.|
## <a name="next-steps"></a>Passaggi successivi

- Dopo aver installato Azure AD Connect è possibile [verificare l'installazione e assegnare le licenze](how-to-connect-post-installation.md).
- Altre informazioni su queste funzionalità che sono state abilitate con l'installazione: [Prevenzione delle eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).
- Altre informazioni su questi argomenti comuni: [utilità di pianificazione e come attivare la sincronizzazione](how-to-connect-sync-feature-scheduler.md).
- Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
