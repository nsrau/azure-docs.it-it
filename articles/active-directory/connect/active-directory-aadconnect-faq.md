---
title: Domande frequenti su Azure Active Directory Connect | Documentazione Microsoft
description: Questa pagina contiene le domande frequenti su Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054095"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Domande frequenti su Azure Active Directory Connect

## <a name="general-installation"></a>Installazione generale
**D: L'installazione verrà eseguita correttamente se per l'amministratore globale di Active Directory Azure è abilitata l'autenticazione a due fattori?**  
Questa opzione è supportata nelle build rilasciate a partire da febbraio 2016.

**D: Esiste un modo per eseguire l'installazione automatica di Azure AD Connect?**  
È supportata solo l'installazione di Azure AD Connect tramite l'installazione guidata. L'installazione automatica e invisibile all'utente non è supportata.

**D: Ho una foresta in cui un dominio non può essere contattato. Come installare Azure AD Connect?**  
Questa opzione è supportata nelle build rilasciate a partire da febbraio 2016.

**D: L'agente di integrità di AD DS funziona su Server Core?**  
Sì. Dopo aver installato l'agente, è possibile completare il processo di registrazione con il cmdlet di PowerShell seguente: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**D: AADConnect supporta la sincronizzazione da due domini in Azure AD?**</br>
Sì, questa operazione è supportata. Fare riferimento all'argomento relativo al [supporto di più domini](active-directory-aadconnect-multiple-domains.md)
 
**D: La presenza di più connettori per lo stesso dominio di Active Directory è supportata in Azure AD Connect?**</br> No, questa operazione non è supportata 

## <a name="network"></a>Rete
**D: Ho un firewall, un dispositivo di rete o qualcos'altro che limita il tempo massimo in cui le connessioni possono rimanere aperte sulla mia rete. Quanto deve durare la soglia di timeout lato client quando si utilizza Connetti AD Azure?**  
Tutti i software di rete, i dispositivi fisici o qualsiasi altra cosa che limiti il tempo massimo delle connessioni possono rimanere aperti qualora si utilizzi una soglia di almeno 5 minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect e la Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione Microsoft Identity rilasciati in precedenza.

**D: I domini SDL sono supportati?**  
No, Azure AD Connect non supporta foreste/domini in locale mediante i domini SLD.

**D: Le foreste con domini di Active Directory non contigui sono supportate?**  
No, Azure AD Connect non supporta foreste in locale che contengono spazi dei nomi non contigui.

**D: Gli elementi denominati NetBios con "punti" sono supportati?**  
No, Azure AD Connect non supporta foreste/domini in locale il cui nome NetBios contenga un periodo "." nel nome.

**D: L'ambiente puro IPv6 è supportato?**  
No, Azure AD Connect non supporta l'ambiente IPv6 puro.

## <a name="federation"></a>Federazione
**D: Cosa occorre fare se si riceve un messaggio di posta elettronica che richiede il rinnovo del certificato di Office 365?**  
Usare le indicazioni specifiche disponibili nell'argomento che spiega come [rinnovare i certificati](active-directory-aadconnect-o365-certs.md) .

**D: L'opzione di aggiornamento automatico della relying party è impostata per la relying party Office 365. È necessario eseguire un'azione quando il certificato per la firma di token esegue automaticamente il rollover?**  
Usare le linee guida descritte nell'articolo relativo al [rinnovare i certificati](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environment
**D: Il fatto di rinominare il server dopo l'installazione di Azure AD Connect è supportato?**  
di serie La modifica del nome del server farà sì che il motore di sincronizzazione no sia in grado di connettersi al database SQL e il servizio non riuscirà ad avviarsi.

## <a name="identity-data"></a>Dati di identità
**D: Perché l'attributo UPN (userPrincipalName) in Azure AD non corrisponde con l’UPN locale?**  
Vedere i seguenti articoli:

* [I nomi utente in Office 365, Azure o Intune non corrispondono agli ID di accesso o alternativi dell'UPN locale](https://support.microsoft.com/en-us/kb/2523192)
* [Le modifiche non sono sincronizzate dallo strumento di sincronizzazione di Azure Active Directory dopo aver modificato l'UPN di un account utente per usare un dominio federato diverso](https://support.microsoft.com/en-us/kb/2669550)

È anche possibile configurare Azure AD per consentire al motore di sincronizzazione di aggiornare userPrincipalName come descritto in [Funzionalità del servizio di sincronizzazione di Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**D: È supportata la corrispondenza flessibile degli oggetti contatto/gruppo AD locali con gli oggetti contatto/gruppo Azure AD esistenti?**  
Sì, si baserà su proxyAddress.  La corrispondenza flessibile non è supportata per i gruppi non abilitati alla posta elettronica.

**D: È supportata l'impostazione manualmente dell'attributo ImmutableId su oggetti contatto/gruppo Azure AD esistenti per farlo corrispondere a livello rigido a oggetti contatto/gruppo AD locali?**  
No, attualmente non è supportata.

## <a name="custom-configuration"></a>Configurazione personalizzata
**D: Dove sono documentati i cmdlet PowerShell per Azure AD Connect?**  
Fatta eccezione per i cmdlet documentati in questo sito, gli altri cmdlet di PowerShell disponibili in Azure AD Connect di non sono supportati per l'utilizzo da parte degli utenti.

**D: Si può usare l'opzione "Server export/server import" disponibile in *Synchronization Service Manager* per spostare la configurazione tra i server?**  
di serie Questa opzione non recupererà tutte le impostazioni di configurazione e non deve essere utilizzata. Si dovrebbe invece utilizzare la procedura guidata per creare la configurazione di base sul secondo server e usare l'editor delle regole di sincronizzazione per generare script di PowerShell per spostare qualsiasi regola personalizzata tra i server. Vedere [Migrazione swing](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**D: Le password per la pagina di accesso di Azure possono essere memorizzate nella cache; è possibile evitare questa condizione poiché contiene un elemento di input della password impostando l'attributo della funzionalità di completamento automatico = "false"?**</br>
Attualmente la modifica degli attributi HTML del campo di input della password non è supportata, incluso il tag di completamento automatico. Attualmente è in fase di elaborazione una funzionalità che consente un JavaScript personalizzato con cui è possibile aggiungere qualsiasi attributo al campo della password. Dovrebbe essere disponibile più avanti nel 2017.

**D: Nella pagina di accesso di Azure vengono visualizzati i nomi utente per gli utenti che hanno già eseguito l'accesso correttamente.  Questo comportamento può essere disattivato?**</br>
Attualmente la modifica degli attributi HTML della pagina di accesso non è supportata. Attualmente è in fase di elaborazione una funzionalità che consente un JavaScript personalizzato con cui è possibile aggiungere qualsiasi attributo al campo della password. Dovrebbe essere disponibile più avanti nel 2017.

**D: Esiste un modo per impedire sessioni simultanee?**</br>
di serie

## <a name="auto-upgrade"></a>Aggiornamento automatico

**D: Quali sono i vantaggi e le conseguenze derivanti dall'uso dell'aggiornamento automatico?**</br>
Si consiglia a tutti i clienti di abilitare l'aggiornamento automatico per l'installazione di Azure AD Connect in uso. In questo modo riceveranno sempre le patch più recenti, inclusi gli aggiornamenti di sicurezza per le vulnerabilità rilevate in Azure AD Connect. Il processo di aggiornamento non comporta alcun problema e viene eseguito automaticamente non appena è disponibile una nuova versione. Diverse migliaia di clienti di Azure AD Connect si avvalgono dell'aggiornamento automatico ogni volta che viene rilasciata una nuova versione.

Per prima cosa, il processo di aggiornamento automatico stabilisce sempre se un'installazione è idonea per l'aggiornamento automatico (operazione che include la ricerca di modifiche personalizzate alle regole, specifici fattori ambientali e così via) e, in caso affermativo, l'aggiornamento viene eseguito e testato. Se i test indicano che un aggiornamento non è riuscito, verrà automaticamente ripristinata la versione precedente.

In base alle dimensioni dell'ambiente, il processo può richiedere fino a due ore e, mentre l'aggiornamento è in corso, non viene eseguita alcuna sincronizzazione tra Windows Server Active Directory e Azure AD.

**D: È stato ricevuto un messaggio di posta elettronica in cui si specifica che l'aggiornamento automatico non funziona più ed è necessario installare una nuova versione. Perché è necessario eseguire questa operazione?**</br>
Lo scorso anno è stata rilasciata una versione di Azure AD Connect che, in determinate circostanze, può aver disattivato la funzionalità di aggiornamento automatico sul server. Questo problema è stato risolto in Azure AD Connect versione 1.1.750.0, rilasciato alla fine del mese scorso. Per risolvere il problema, è necessario che gli utenti interessati eseguano manualmente l'aggiornamento alla versione più recente di Azure AD Connect. Per eseguire l'aggiornamento manuale, è sufficiente scaricare ed eseguire la versione più recente del file AADConnect.msi.
 
-  Se la versione in uso è precedente alla 1.1.750.0, è necessario eseguire l'aggiornamento alla versione più recente, [che può essere scaricata qui](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Se invece è in uso la versione 1.1.750.0 di Azure AD Connect o una versione successiva, non è necessario eseguire alcuna operazione per risolvere i problemi di aggiornamento automatico, poiché la versione corrente contiene già una correzione per il problema. 

**D: È stato ricevuto un messaggio di posta elettronica in cui si richiede di eseguire l'aggiornamento alla versione più recente per riabilitare l'aggiornamento automatico. È in uso la versione 1.1.654.0: è necessario eseguire l'aggiornamento?** </br>    
Sì, è necessario eseguire l'aggiornamento alla versione 1.1.750 o successiva per riabilitare l'aggiornamento automatico. Ecco il collegamento che spiega come eseguire l'aggiornamento a una versione più recente

**D: È stato ricevuto un messaggio di posta elettronica in cui si richiede di eseguire l'aggiornamento alla versione più recente per riabilitare l'aggiornamento automatico. È stato usato PowerShell per abilitare l'aggiornamento automatico, è comunque necessario installare la versione più recente?**</br>    
Sì, è comunque necessario eseguire l'aggiornamento alla versione 1.1.750.0 o successiva. L'abilitazione del servizio di aggiornamento automatico con PowerShell non risolve il problema di aggiornamento automatico rilevato nelle versioni precedenti alla 1.1.750

**D: Si vuole eseguire l'aggiornamento a una versione più recente, ma non si sa chi ha installato Azure AD Connect e non si dispone del relativo nome utente e password.  Queste informazioni sono necessarie?**</br>
Non è necessario conoscere il nome utente e la password originariamente usati per aggiornare Azure AD Connect: è possibile usare qualsiasi account di Azure AD con ruolo di amministratore globale.

**D: Come è possibile conoscere la versione di Azure AD Connect in uso?**</br>   
Per sapere quale versione di Azure AD Connect è installata nel server, passare al pannello di controllo e cercare la versione installata di Microsoft Azure AD Connect in "Programmi > Programmi e funzionalità":

![versione](media/active-directory-aadconnect-faq/faq1.png)

**D: Come è possibile eseguire l'aggiornamento alla versione più recente di AADConnect?**</br>    
Questo [articolo](active-directory-aadconnect-upgrade-previous-version.md) spiega come eseguire l'aggiornamento a una versione più recente. 

**D: L'aggiornamento alla versione più recente di AADConnect è stato già eseguito lo scorso anno, è necessario effettuare nuovamente l'aggiornamento?**</br> I team di Azure AD Connect apportano frequenti aggiornamenti al servizio ed è importante che il server sia aggiornato con la versione più recente in modo da poter trarre vantaggio dalle correzioni di bug, dagli aggiornamenti alla sicurezza e dalle nuove funzionalità. Se si abilita l'aggiornamento automatico, la versione del software verrà aggiornata automaticamente. Per trovare la cronologia delle versioni di Azure AD Connect, seguire questo [collegamento](active-directory-aadconnect-version-history.md).

**D: Quanto tempo sarà necessario per eseguire l'aggiornamento e quale sarà l'impatto sugli utenti?**</br>    
Il tempo necessario per eseguire l'aggiornamento dipende dalla dimensione del tenant e, per le organizzazioni di grandi dimensioni, è consigliabile effettuare questa operazione la sera o nei fine settimana. Durante l'aggiornamento non viene eseguita alcuna attività di sincronizzazione.

**D: L'aggiornamento ad AADConnect è stato effettuato ma nel portale di Office risulta ancora DirSync.  Perché?**</br>    
Il team di Office lavora costantemente per mantenere il portale di Office aggiornato con i nomi di prodotto correnti, non con lo strumento di sincronizzazione in uso.

**D: Lo stato dell'aggiornamento automatico è impostato su "sospeso". Perché è sospeso? È necessario abilitarlo?**</br>     
In una versione precedente è stato introdotto un bug che, in determinate circostanze, lascia lo stato di aggiornamento automatico impostato su "sospeso". L'abilitazione manuale dell'aggiornamento automatico è tecnicamente possibile ma richiede vari passaggi complessi. La soluzione migliore, quindi, è installare la versione più recente di Azure AD Connect.

**D: In azienda sono in vigore rigorosi requisiti di gestione delle modifiche e si vuole controllare quando non vengono rispettati. È possibile sapere quando viene lanciata la procedura di aggiornamento automatico?**</br> No, attualmente questa possibilità non esiste, ma si tratta di una funzionalità di cui si sta valutando l'introduzione in una versione futura.

**D: Si riceve un messaggio di posta elettronica se l'aggiornamento automatico ha esito negativo? Come è possibile sapere se invece ha avuto esito positivo?**</br>     
Non viene inviata alcuna notifica in merito all'esito dell'aggiornamento, ma si tratta di una funzionalità di cui si sta valutando l'introduzione in una versione futura.

**D: Viene pubblicato un piano di rilascio degli aggiornamenti automatici?**</br>    
L'aggiornamento automatico è il primo passaggio del processo di rilascio di una nuova versione. Ogni volta che è disponibile una nuova versione, quindi, viene lanciato l'aggiornamento automatico. Le nuove versioni di Azure AD Connect vengono pre-annunciate nella [roadmap di Azure Active Directory](../../active-directory/whats-new.md).

**D: Con l'aggiornamento automatico viene aggiornato anche Azure Active Directory Connect Health?**</br>   Sì, con l'aggiornamento automatico viene aggiornato anche Azure Active Directory Connect Health

**D: Viene eseguito l'aggiornamento automatico anche dei server AAD Connect in modalità di staging?**</br>   
No, non è possibile eseguire l'aggiornamento automatico di un server Azure AD Connect in modalità di staging.

**D: Se l'aggiornamento automatico non riesce e il server AAD Connect non si avvia, cosa è necessario fare?**</br>   
In alcuni casi rari, il servizio Azure AD Connect non si avvia dopo l'aggiornamento. In questi casi, riavviare il server, operazione che in genere risolve il problema. Se il servizio Azure AD Connect continua a non avviarsi, aprire un ticket di supporto. Ecco un [collegamento](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) che spiega come eseguire questa operazione. 

**D: Quali rischi si corrono quando si esegue l'aggiornamento a una nuova versione di Azure AD Connect? È possibile ricevere assistenza telefonica per eseguire correttamente l'aggiornamento?**</br>
Se è necessaria assistenza per l'aggiornamento a una nuova versione di Azure AD Connect, aprire un ticket di supporto seguendo questo [collegamento](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) che illustra come eseguire questa operazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi
**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Cercare nella Microsoft Knowledge Base (KB) soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Per cercare e passare in rassegna domande e risposte tecniche della community o porre domande, fare clic [qui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Come ottenere supporto per Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Usare questo collegamento per ottenere assistenza tramite il portale di Azure.

