---
title: Domande frequenti su Azure Active Directory Connect | Documentazione Microsoft
description: Questa pagina contiene le domande frequenti su Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: fd5988b2d4170166902bb5cc39603d4a0f83be59
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

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

## <a name="network"></a>Rete
**D: Ho un firewall, un dispositivo di rete o qualcos'altro che limita il tempo massimo in cui le connessioni possono rimanere aperte sulla mia rete. Quanto deve durare la soglia di timeout lato client quando si utilizza Connetti AD Azure?**  
Tutti i software di rete, i dispositivi fisici o qualsiasi altra cosa che limiti il tempo massimo delle connessioni possono rimanere aperti qualora si utilizzi una soglia di almeno 5 minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect e la Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione Microsoft Identity rilasciati in precedenza.

**D: I domini SDL sono supportati?**  
No, Azure AD Connect non supporta foreste/domini in locale mediante i domini SLD.

**D: Gli elementi denominati NetBios con "punti" sono supportati?**  
No, Azure AD Connect non supporta foreste/domini in locale il cui nome NetBios contenga un periodo "." nel nome.

## <a name="federation"></a>Federazione
**D: Cosa occorre fare se si riceve un messaggio di posta elettronica che richiede il rinnovo del certificato di Office 365?**  
Usare le indicazioni specifiche disponibili nell'argomento che spiega come [rinnovare i certificati](active-directory-aadconnect-o365-certs.md) .

**D: L'opzione di aggiornamento automatico della relying party è impostata per la relying party Office 365. È necessario eseguire un'azione quando il certificato per la firma di token esegue automaticamente il rollover?**  
Usare le linee guida descritte nell'articolo relativo al [rinnovare i certificati](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environment
**D: Il fatto di rinominare il server dopo l'installazione di Azure AD Connect è supportato?**  
No. La modifica del nome del server farà sì che il motore di sincronizzazione no sia in grado di connettersi al database SQL e il servizio non riuscirà ad avviarsi.

## <a name="identity-data"></a>Dati di identità
**D: Perché l'attributo UPN (userPrincipalName) in Azure AD non corrisponde con l'UPN locale?**  
Vedere i seguenti articoli:

* [I nomi utente in Office 365, Azure o Intune non corrispondono agli ID di accesso o alternativi dell'UPN locale](https://support.microsoft.com/en-us/kb/2523192)
* [Le modifiche non sono sincronizzate dallo strumento di sincronizzazione di Azure Active Directory dopo aver modificato l'UPN di un account utente per usare un dominio federato diverso](https://support.microsoft.com/en-us/kb/2669550)

È anche possibile configurare Azure AD per consentire al motore di sincronizzazione di aggiornare userPrincipalName come descritto in [Funzionalità del servizio di sincronizzazione di Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**D: È supportata la corrispondenza flessibile degli oggetti contatto/gruppo AD locali con gli oggetti contatto/gruppo Azure AD esistenti?**  
No, attualmente non è supportata.

**D: È supportata l'impostazione manualmente dell'attributo ImmutableId su oggetti contatto/gruppo Azure AD esistenti per farlo corrispondere a livello rigido a oggetti contatto/gruppo AD locali?**  
No, attualmente non è supportata.



## <a name="custom-configuration"></a>Configurazione personalizzata
**D: Dove sono documentati i cmdlet PowerShell per Azure AD Connect?**  
Fatta eccezione per i cmdlet documentati in questo sito, gli altri cmdlet di PowerShell disponibili in Azure AD Connect di non sono supportati per l'utilizzo da parte degli utenti.

**D: Si può usare l'opzione "Server export/server import" disponibile in *Synchronization Service Manager* per spostare la configurazione tra i server?**  
No. Questa opzione non recupererà tutte le impostazioni di configurazione e non deve essere utilizzata. Si dovrebbe invece utilizzare la procedura guidata per creare la configurazione di base sul secondo server e usare l'editor delle regole di sincronizzazione per generare script di PowerShell per spostare qualsiasi regola personalizzata tra i server. Vedere [Migrazione swing](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**D: Le password per la pagina di accesso di Azure possono essere memorizzate nella cache; è possibile evitare questa condizione poiché contiene un elemento di input della password impostando l'attributo della funzionalità di completamento automatico = "false"?**</br>
Attualmente la modifica degli attributi HTML del campo di input della password non è supportata, incluso il tag di completamento automatico. Attualmente è in fase di elaborazione una funzionalità che consente un JavaScript personalizzato con cui è possibile aggiungere qualsiasi attributo al campo della password. Dovrebbe essere disponibile più avanti nel 2017.

**D: Nella pagina di accesso di Azure vengono visualizzati i nomi utente per gli utenti che hanno già eseguito l'accesso correttamente.  Questo comportamento può essere disattivato?**</br>
Attualmente la modifica degli attributi HTML della pagina di accesso non è supportata. Attualmente è in fase di elaborazione una funzionalità che consente un JavaScript personalizzato con cui è possibile aggiungere qualsiasi attributo al campo della password. Dovrebbe essere disponibile più avanti nel 2017.

**D: Esiste un modo per impedire sessioni simultanee?**</br>
No.



## <a name="troubleshooting"></a>Risoluzione dei problemi
**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Cercare nella Microsoft Knowledge Base (KB) soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Per cercare e passare in rassegna domande e risposte tecniche della community o porre domande, fare clic [qui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Assistenza clienti per Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

* Usare questo collegamento per ottenere assistenza tramite il portale di Azure.


