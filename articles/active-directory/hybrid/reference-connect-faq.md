---
title: Domande frequenti su Azure Active Directory Connect | Microsoft Docs
description: Questo articolo contiene domande frequenti su Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/02/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: f15556596063a09f68f024346e9fbe08663436c9
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497809"
---
# <a name="azure-active-directory-connect-faq"></a>Domande frequenti su Azure Active Directory Connect

## <a name="general-installation"></a>Installazione generale
**D: L'installazione funziona se per l'amministrazione globale di Azure Active Directory (Azure AD) è abilitata l'autenticazione a due fattori (2FA)?**  
A partire dalle build di febbraio 2016, questo scenario è supportato.

**D: Esiste un modo per eseguire l'installazione automatica di Azure AD Connect?**  
L'installazione di Azure AD Connect è supportata solo se si usa l'installazione guidata. L'installazione automatica invisibile all'utente non è supportata.

**D: Ho una foresta in cui un dominio non può essere contattato. Come installare Azure AD Connect?**  
A partire dalle build di febbraio 2016, questo scenario è supportato.

**D: L'agente integrità Azure Active Directory Domain Services (Azure AD DS) funziona in Server Core?**  
Sì. Dopo aver installato l'agente, è possibile completare il processo di registrazione tramite il cmdlet di PowerShell seguente: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**D: Azure AD Connect supporta la sincronizzazione da due domini in Azure AD?**  
Sì, questo scenario è supportato. Fare riferimento a [Supporto di più domini](how-to-connect-install-multiple-domains.md).
 
**D: La presenza di più connettori per lo stesso dominio di Active Directory è supportata in Azure AD Connect?**  
No, la presenza di più connettori per lo stesso dominio di AD non è supportata. 

**D: È possibile spostare il database di Azure AD Connect da un database locale a un'istanza di SQL Server remota?**   
Sì. I passaggi seguenti rappresentano materiale sussidiario su come eseguire questa operazione. È attualmente in corso la preparazione di un documento più dettagliato.
1. Eseguire il backup del database LocalDB ADSync.
Il modo più semplice per eseguire questa operazione consiste nell'usare SQL Server Management Studio, installato nello stesso computer in cui è installato Azure AD Connect. Connettersi a *(LocalDb).\ADSync* e quindi eseguire il backup del database ADSync.

2. Ripristinare il database ADSync nell'istanza remota di SQL Server.

3. Installare Azure AD Connect per il [database SQL remoto](how-to-connect-install-existing-database.md) esistente.
   L'articolo illustra come eseguire la migrazione per passare all'uso di un database SQL locale. Se si esegue la migrazione per passare all'uso di un database SQL remoto, nel passaggio 5 di questo processo è anche necessario immettere un account esistente per l'esecuzione del servizio di sincronizzazione di Windows. Il processo di sincronizzazione di questo account del servizio viene descritto di seguito:
   
      **Usare un account di servizio esistente**: per impostazione predefinita, Azure AD Connect usa un account di servizio virtuale per i servizi di sincronizzazione. Se si usa un'istanza di SQL Server remota o un proxy che richiede l'autenticazione, usare un account del servizio gestito o un account del servizio nel dominio di cui si conosce la password. In questi casi, immettere l'account da usare. Assicurarsi che gli utenti che eseguono l'installazione siano amministratori di sistema per SQL Server, in modo che sia possibile la creazione delle credenziali di accesso per l'account del servizio. Per altre informazioni, vedere [Account e autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Con la build più recente, l'amministratore SQL può ora effettuare il provisioning del database fuori banda e quindi l'amministratore di Azure AD Connect può eseguire l'installazione con diritti di proprietario del database. Per altre informazioni, vedere [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md).

Per semplicità, è consigliabile che gli utenti che installano Azure AD Connect siano amministratori di sistema in SQL Server. Con le build recenti, tuttavia, l'installazione può essere eseguita anche da amministratori SQL con delega, come descritto in [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md).

## <a name="network"></a>Rete
**D: Un firewall, un dispositivo di rete o un altro meccanismo limita il tempo massimo di apertura delle connessioni all'interno della rete. Quale deve essere la soglia di timeout lato client quando si usa Azure AD Connect?**  
Tutto il software di rete e tutti i dispositivi fisici o i meccanismi di altro tipo che limitano la durata delle connessioni devono applicare una soglia di almeno cinque minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect ed Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione delle identità di Microsoft rilasciati in precedenza.

**D: I nomi di dominio con etichetta singola sono supportati?**  
Sebbene questa configurazione di rete non sia consigliata ([vedere l'articolo](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), l'uso della sincronizzazione di Azure AD Connect con un dominio con etichetta singola è supportato, a condizione che la configurazione di rete per il dominio a livello singolo funzioni correttamente.

**D: Le foreste con domini di Active Directory non contigui sono supportate?**  
No, Azure AD Connect non supporta foreste locali contenenti spazi dei nomi non contigui.

**D: I nomi NetBIOS contenenti un punto sono supportati?**  
No, Azure AD Connect non supporta foreste e domini locali il cui nome NetBIOS contenga un punto (.).

**D: L'ambiente puro IPv6 è supportato?**  
No, Azure AD Connect non supporta un ambiente IPv6 puro.

**D: È presente un ambiente a più foreste, dove la rete tra due foreste usa NAT (Network Address Translation). L'uso di Azure AD Connect tra le due foreste è supporto?**</br>
 No, l'uso di Azure AD Connect con NAT non è supportato. 

## <a name="federation"></a>Federazione
**D: Cosa occorre fare se si riceve un messaggio di posta elettronica che richiede il rinnovo del certificato di Office 365?**  
Per materiale sussidiario sul rinnovo del certificato, vedere [Rinnovare i certificati](how-to-connect-fed-o365-certs.md).

**D: L'opzione "Aggiorna automaticamente componente" è impostata per la relying party Office 365. È necessario eseguire un'azione quando il certificato per la firma di token esegue automaticamente il rollover?**  
Seguire il materiale sussidiario illustrato nell'articolo [Rinnovare i certificati](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Environment
**D: Il fatto di rinominare il server dopo l'installazione di Azure AD Connect è supportato?**  
 No. La modifica del nome del server impedisce al motore di sincronizzazione di connettersi all'istanza del database SQL e il servizio non può essere avviato.

## <a name="identity-data"></a>Dati di identità
**D: Perché l'attributo userPrincipalName (UPN) in Azure AD non corrisponde all'UPN locale?**  
Per informazioni, vedere questi articoli:

* [Usernames in Office 365, Azure, or Intune don't match the on-premises UPN or alternate login ID](https://support.microsoft.com/kb/2523192) (I nomi utente in Office 365, Azure e Intune non corrispondono all'UPN locale o all'ID di accesso alternativo)
* [Le modifiche non vengono sincronizzate tramite lo strumento di sincronizzazione con Active Directory Azure dopo aver modificato l'UPN di un account utente per l'utilizzo di un altro dominio federato](https://support.microsoft.com/kb/2669550)

È anche possibile configurare Azure AD in modo da consentire al motore di sincronizzazione di aggiornare l'UPN come descritto in [Funzionalità del servizio di sincronizzazione di Azure AD Connect](how-to-connect-syncservice-features.md).

**D: È supportata la corrispondenza flessibile degli oggetti contatto o gruppo di Azure AD locali con gli oggetti contatto o gruppo di Azure AD esistenti?**  
Sì, la corrispondenza si basa su proxyAddress. La corrispondenza flessibile non è supportata per i gruppi non abilitati alla posta elettronica.

**D: È supportata l'impostazione manuale dell'attributo ImmutableId di oggetti contatto o gruppo di Azure AD esistenti per ottenere una corrispondenza rigida con oggetti contatto o gruppo di Azure AD locali?**  
No, l'impostazione manuale dell'attributo ImmutableId di oggetti contatto o gruppo di Azure AD esistenti per ottenere una corrispondenza rigida non è attualmente supportata.

## <a name="custom-configuration"></a>Configurazione personalizzata
**D: Dove sono documentati i cmdlet PowerShell per Azure AD Connect?**  
Fatta eccezione per i cmdlet documentati in questo sito, i cmdlet di PowerShell disponibili in Azure AD Connect non sono supportati per l'uso da parte degli utenti.

**D: È possibile usare le opzioni "Server esportazione" e "Server importazione" disponibili in Synchronization Service Manager per spostare la configurazione da un server all'altro?**  
 No. Questa opzione non recupera tutte le impostazioni di configurazione e non deve essere usata. Usare invece la procedura guidata per creare la configurazione di base per il secondo server e usare l'editor delle regole di sincronizzazione per generare script di PowerShell e spostare regole personalizzate da un server all'altro. Per altre informazioni, vedere [Migrazione swing](how-to-upgrade-previous-version.md#swing-migration).

**D: È possibile memorizzare nella cache le password per la pagina di accesso di Azure ed è possibile evitare questa memorizzazione se la password contiene un elemento di input con l'attributo *autocomplete = "false"*?**  
La modifica degli attributi HTML del campo **Password**, tra cui il tag autocomplete, non è attualmente supportata. È attualmente in fase di elaborazione una funzionalità che consente la creazione di codice JavaScript personalizzato che consenta di aggiungere qualsiasi attributo al campo **Password**.

**D: La pagina di accesso di Azure visualizza i nomi utente che in precedenza hanno eseguito l'accesso correttamente. Questo comportamento può essere disattivato?**  
La modifica degli attributi HTML del campo di input **Password**, tra cui il tag autocomplete, non è attualmente supportata. È attualmente in fase di elaborazione una funzionalità che consente la creazione di codice JavaScript personalizzato che consenta di aggiungere qualsiasi attributo al campo **Password**.

**D: Esiste un modo per impedire sessioni simultanee?**  
 No.

## <a name="auto-upgrade"></a>Aggiornamento automatico

**D: Quali sono i vantaggi e le conseguenze derivanti dall'uso dell'aggiornamento automatico?**  
È consigliabile abilitare l'aggiornamento automatico dell'installazione di Azure AD Connect in uso. In questo modo si riceveranno sempre le patch più recenti, inclusi gli aggiornamenti di sicurezza per le vulnerabilità rilevate in Azure AD Connect. Il processo di aggiornamento non comporta alcun problema e viene eseguito automaticamente non appena è disponibile una nuova versione. Diverse migliaia di clienti di Azure AD Connect si avvalgono dell'aggiornamento automatico ogni volta che viene rilasciata una nuova versione.

Il processo di aggiornamento automatico stabilisce sempre inizialmente se un'installazione è idonea per l'aggiornamento automatico. Se è idonea, l'aggiornamento viene eseguito e testato. Il processo prevede anche la ricerca di modifiche personalizzate a regole e fattori ambientali specifici. Se i test indicano che un aggiornamento non è riuscito, la versione precedente viene ripristinata automaticamente.

A seconda delle dimensioni dell'ambiente, il processo può richiedere circa due ore. Durante l'aggiornamento, non viene eseguita alcuna sincronizzazione tra Windows Server Active Directory e Azure AD.

**D: Un messaggio di posta elettronica informa che l'aggiornamento automatico non funziona più ed è necessario installare una nuova versione. Perché è necessario eseguire questa operazione?**  
Lo scorso anno è stata rilasciata una versione di Azure AD Connect che, in determinate circostanze, può aver disabilitato la funzionalità di aggiornamento automatico nel server. Il problema è stato risolto in Azure AD Connect versione 1.1.750.0. Gli utenti interessati dal problema possono attenuarlo eseguendo uno script di PowerShell o eseguire manualmente l'aggiornamento alla versione più recente di Azure AD Connect. 

Per eseguire lo script di PowerShell, [scaricarlo](https://aka.ms/repairaadconnect) ed eseguirlo nel server Azure AD Connect in una finestra amministrativa di PowerShell. Per informazioni su come eseguire lo script, [guardare questo breve video](https://aka.ms/repairaadcau).

Per eseguire l'aggiornamento manuale, è sufficiente scaricare ed eseguire la versione più recente del file AADConnect.msi.
 
-  Se la versione in uso è precedente alla versione 1.1.750.0, [scaricare e la versione più recente ed eseguire l'aggiornamento](https://www.microsoft.com/download/details.aspx?id=47594).
- Se si usa Azure AD Connect 1.1.750.0 o versione successiva, non è necessaria alcuna azione, perché si sta già usando la versione che contiene la correzione dell'aggiornamento automatico. 

**D: È stato ricevuto un messaggio di posta elettronica in cui si richiede di eseguire l'aggiornamento alla versione più recente per riabilitare l'aggiornamento automatico. Se si usa la versione 1.1.654.0, è necessario eseguire l'aggiornamento?**  
Sì, per riabilitare l'aggiornamento automatico è necessario eseguire l'aggiornamento alla versione 1.1.750.0 o a una versione successiva. [Scaricare la versione più recente ed eseguire l'aggiornamento](https://www.microsoft.com/download/details.aspx?id=47594).

**D: È stato ricevuto un messaggio di posta elettronica in cui si richiede di eseguire l'aggiornamento alla versione più recente per riabilitare l'aggiornamento automatico. Se per abilitare l'aggiornamento automatico si è usato PowerShell, è comunque necessario installare la versione più recente?**  
Sì, è comunque necessario eseguire l'aggiornamento alla versione 1.1.750.0 o a una versione successiva. L'abilitazione del servizio di aggiornamento automatico con PowerShell non risolve il problema dell'aggiornamento automatico rilevato nelle versioni precedenti alla 1.1.750.0.

**D: Si vuole eseguire l'aggiornamento a una versione più recente, ma non si conosce l'utente che ha installato Azure AD Connect e il nome utente e la password di tale utente non sono disponibili. Queste informazioni sono necessarie?**
Per aggiornare Azure AD Connect, non è necessario conoscere il nome utente e la password usati la prima volta. Usare un qualsiasi account di Azure AD che abbia il ruolo Amministratore globale.

**D: Come si può conoscere la versione di Azure AD Connect in uso?**  
Per sapere quale versione di Azure AD Connect è installata nel server, passare al Pannello di controllo e cercare la versione installata di Microsoft Azure AD Connect selezionando **Programmi** > **Programmi e funzionalità**, come illustrato qui:

![Versione di Azure AD Connect nel Pannello di controllo](./media/reference-connect-faq/faq1.png)

**D: Come si esegue l'aggiornamento alla versione più recente di Azure AD Connect?**  
Per informazioni su come eseguire l'aggiornamento alla versione più recente di Azure AD Connect, vedere [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](how-to-upgrade-previous-version.md). 

**D: L'aggiornamento alla versione più recente di Azure AD Connect è già stato eseguito lo scorso anno. È necessario effettuare nuovamente l'aggiornamento?**  
Il team di Azure AD Connect crea numerosi aggiornamenti del servizio. Per trarre vantaggio dalle correzioni di bug e dagli aggiornamenti della sicurezza, nonché dalle nuove funzionalità, è importante mantenere il server aggiornato alla versione più recente. Se si abilita l'aggiornamento automatico, la versione del software viene aggiornata automaticamente. Per trovare la cronologia del rilascio delle versioni di Azure AD Connect, vedere [Azure AD Connect: Cronologia delle versioni](reference-connect-version-history.md).

**D: Quanto tempo richiede l'aggiornamento e qual è l'impatto sugli utenti?**  
Il tempo necessario per eseguire l'aggiornamento dipende dalle dimensioni del tenant. Per organizzazioni di grandi dimensioni, può essere consigliabile eseguire l'aggiornamento la sera o nel fine settimana. Durante l'aggiornamento non viene eseguita alcuna attività di sincronizzazione.

**D: L'aggiornamento ad Azure AD Connect è stato effettuato, ma nel portale di Office risulta ancora DirSync. Perché?**  
Il team di Office è al lavoro perché gli aggiornamenti al portale di Office riflettano i nomi di prodotto correnti, non lo strumento di sincronizzazione usato dagli utenti.

**D: Lo stato dell'aggiornamento automatico corrisponde a "Sospeso". Perché è sospeso? È necessario abilitarlo?**  
Per un bug in una versione precedente, in determinate circostanze lo stato dell'aggiornamento automatico rimane impostato su "Sospeso". L'abilitazione manuale è tecnicamente possibile, ma richiede diversi passaggi complessi. La soluzione migliore è scaricare e installare la versione più recente di Azure AD Connect.

**D: In azienda sono in vigore requisiti rigorosi di gestione delle modifiche e si vuole controllare quando non vengono rispettati. È possibile sapere quando viene lanciata la procedura di aggiornamento automatico?**  
No, al momento una funzione di questo tipo non è disponibile. La funzione è in corso di valutazione per una versione futura.

**D: Se l'aggiornamento automatico ha esito negativo, si riceve un messaggio di posta elettronica? Come è possibile sapere se invece ha avuto esito positivo?**  
Non si riceve una notifica del risultato dell'aggiornamento. La funzione è in corso di valutazione per una versione futura.

**D: Viene pubblicato un piano di rilascio degli aggiornamenti automatici?**  
L'aggiornamento automatico è il primo passaggio nel processo di rilascio di una versione più recente. Ogni volta che è disponibile una nuova versione, gli aggiornamenti vengono rilasciati automaticamente. Le nuove versioni di Azure AD Connect vengono pre-annunciate nella [roadmap di Azure Active Directory](../fundamentals/whats-new.md).

**D: Con l'aggiornamento automatico viene aggiornato anche Azure AD Connect Health?**  
Sì, con l'aggiornamento automatico viene aggiornato anche Azure AD Connect Health.

**D: Viene eseguito l'aggiornamento automatico anche dei server Azure AD Connect in modalità di gestione temporanea?**  
Sì, è possibile eseguire l'aggiornamento automatico di un server Azure AD Connect in modalità di staging.

**D: Se l'aggiornamento automatico non riesce e il server Azure AD Connect non si avvia, cosa è necessario fare?**  
In rari casi, il servizio Azure AD Connect non si avvia dopo l'aggiornamento. In questi casi, il riavvio del server in genere risolve il problema. Se il servizio Azure AD Connect continua a non avviarsi, aprire un ticket di supporto. Per altre informazioni, vedere [Create a service request to contact Office 365 support](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) (Creare una richiesta di servizio per contattare il supporto di Office 365). 

**D: Quali rischi si corrono quando si esegue l'aggiornamento a una versione più recente di Azure AD Connect? È possibile ricevere assistenza telefonica per eseguire correttamente l'aggiornamento?**  
Se è necessaria assistenza per l'aggiornamento a una versione di Azure AD Connect più recente, aprire un ticket di supporto nella pagina [Create a service request to contact Office 365 support](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) (Creare una richiesta di servizio per contattare il supporto di Office 365).

## <a name="troubleshooting"></a>risoluzione dei problemi
**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Cercare nella KB soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Cercare domande e risposte tecniche o porre le proprie domande visitando [la community di Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Ottenere supporto per Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
