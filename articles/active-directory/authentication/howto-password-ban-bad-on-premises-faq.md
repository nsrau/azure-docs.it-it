---
title: Domande frequenti sulla protezione della password Azure AD locale-Azure Active Directory
description: Domande frequenti sulla protezione password di Azure AD locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ccefec9e548b7981f696712bb4a983f4b577a9b
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779640"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>protezione password di Azure AD locale - Domande frequenti

In questa sezione vengono fornite le risposte a molte domande frequenti sulla protezione Azure AD password.

## <a name="general-questions"></a>Domande generali

**D: quali indicazioni devono avere gli utenti su come scegliere una password sicura?**

Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

**D: la protezione password di Azure AD locale è supportata nei cloud non pubblici?**

No, la protezione password di Azure AD locale è supportata solo nel cloud pubblico. Non è stata annunciata alcuna data per la disponibilità nei cloud non pubblici.

**D: in che modo è possibile applicare i vantaggi della protezione password di Azure AD a un sottoinsieme di utenti in locale?**

Non supportati. Dopo la distribuzione e l'abilitazione, Password di protezione di Azure AD non prevede alcuna discriminazione: tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**D: Qual è la differenza tra una modifica della password e un set di password (o reimpostazione)?**

Una modifica della password si verifica quando un utente sceglie una nuova password dopo avere dimostrato la conoscenza della vecchia password. Ad esempio, una modifica della password è ciò che accade quando un utente accede a Windows e viene quindi richiesto di scegliere una nuova password.

Un set di password (talvolta denominato reimpostazione della password) si verifica quando un amministratore sostituisce la password di un account con una nuova password, ad esempio tramite lo strumento di gestione Active Directory utenti e computer. Questa operazione richiede un livello elevato di privilegi (in genere amministratore di dominio) e la persona che esegue l'operazione in genere non conosce la vecchia password. Gli scenari di supporto tecnico eseguono spesso set di password, ad esempio quando si assiste a un utente che ha dimenticato la password. Gli eventi di impostazione della password vengono visualizzati anche quando viene creato un nuovo account utente per la prima volta con una password.

I criteri di convalida della password si comportano allo stesso modo, indipendentemente dal fatto che sia stata eseguita una modifica o un set di password. Il servizio Azure AD Password Protection Agent esegue il log di eventi diversi per indicare se è stata eseguita una modifica della password o un'operazione di impostazione.  Vedere [Azure ad monitoraggio e registrazione per la protezione delle password](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**D: Perché si registrano eventi di rifiuto delle password duplicati quando si tenta di impostare una password vulnerabile usando lo snap-in gestione Active Directory utenti e computer?**

Lo snap-in gestione utenti e computer Active Directory tenterà innanzitutto di impostare la nuova password utilizzando il protocollo Kerberos. In caso di errore, lo snap-in effettuerà un secondo tentativo di impostazione della password utilizzando un protocollo legacy (SAM RPC) (i protocolli specifici utilizzati non sono importanti). Se la nuova password viene considerata debole dalla protezione Azure AD password, questo comportamento dello snap-in comporterà la registrazione di due set di eventi di rifiuto di reimpostazione della password.

**D: Perché vengono registrati gli eventi di convalida password Azure AD Password Protection con un nome utente vuoto?**

Active Directory supporta la possibilità di testare una password per verificare se supera i requisiti di complessità della password correnti del dominio, ad esempio usando l'API [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Quando una password viene convalidata in questo modo, il test include anche la convalida da parte di prodotti basati su dll con filtro password, ad esempio Azure AD la protezione con password, ma i nomi utente passati a una determinata DLL di filtro password saranno vuoti. In questo scenario, Azure AD la protezione con password convaliderà la password usando i criteri della password attualmente in vigore ed emetterà un messaggio del registro eventi per acquisire il risultato, ma il messaggio del registro eventi avrà campi nome utente vuoti.

**D: l'installazione della protezione password di Azure AD è supportata in affiancamento ad altri prodotti basati su filtri password?**

Sì. Il supporto di più DLL di filtro password registrate è una funzionalità di base di Windows e non è specifico della protezione password di Azure AD. Tutte le DLL di filtro password registrate devono concordare, prima che venga accettata una password.

**D: in che modo è possibile distribuire e configurare Password di protezione di Azure AD nell'ambiente Active Directory senza usare Azure?**

Non supportati. Password di protezione di Azure AD è una funzionalità di Azure che supporta l'estensione in un ambiente Active Directory locale.

**D: in che modo è possibile modificare il contenuto dei criteri a livello di Active Directory?**

Non supportati. È possibile amministrare i criteri solo tramite il portale di gestione di Azure AD. Vedere anche la domanda precedente.

**D: perché è necessario il servizio DFSR per la replica di sysvol?**

Il servizio Replica file (la tecnologia precedente a DFSR) include molti problemi noti e non è completamente supportato nelle versioni più recenti di Windows Server Active Directory. La protezione password di Azure AD non verrà testata nei domini configurati con il servizio Replica file.

Per altre informazioni, vedere gli articoli seguenti:

[The Case for Migrating sysvol replication to DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) (Caso della migrazione della replica di sysvol a DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (La fine è vicina per il servizio Replica file)

**D: quanto spazio su disco è necessario alla funzionalità nella condivisione sysvol del dominio?**

La quantità esatta di spazio usato dipende da fattori come il numero e la lunghezza dei token esclusi contenuti nell'elenco globale degli elementi esclusi Microsoft e nell'elenco personalizzato del singolo tenant, nonché dal sovraccarico della crittografia. Il contenuto di questi elenchi probabilmente aumenterà in futuro. Tenendo presente tale situazione, è ragionevole prevedere che per la funzionalità siano necessari almeno cinque (5) megabyte di spazio nella condivisione sysvol del dominio.

**D: perché è necessario un riavvio per installare o aggiornare il software dell'agente del controller di dominio?**

Questo requisito dipende dal comportamento di base di Windows.

**D: è disponibile un modo per configurare un agente del controller di dominio per l'uso di un server proxy specifico?**

No. Poiché il server proxy è senza stato, non ha importanza quale server proxy specifico venga usato.

**D: è possibile distribuire il servizio proxy di Password di protezione di Azure AD side-by-side con altri servizi come Azure AD Connect?**

Sì. Il servizio proxy di Password di protezione di Azure AD e Azure AD Connect non devono essere mai direttamente in conflitto l'uno con l'altro.

**D: In quale ordine devono essere installati e registrati gli agenti e i proxy del controller di dominio?**

Sono supportati gli ordini di installazione dell'agente proxy, l'installazione dell'agente DC, la registrazione della foresta e la registrazione del proxy.

**D: La distribuzione di questa funzionalità può avere un impatto preoccupante sulle prestazioni dei controller di dominio?**

Il servizio agente del controller di dominio di Password di protezione di Azure AD non dovrebbe incidere in modo significativo sulle prestazioni dei controller di dominio in una distribuzione di Active Directory esistente e integra.

Per la maggior parte delle distribuzioni di Active Directory attive, le operazioni di modifica delle password rappresentano una piccola parte del carico di lavoro complessivo in un determinato controller di dominio. Ad esempio, si supponga l'esistenza di un dominio di Active Directory con 10000 account utente e criteri MaxPasswordAge impostati su 30 giorni. In media questo dominio vedrà 10000/30=~333 operazioni di modifica delle password ogni giorno, il che rappresenta un numero trascurabile di operazioni persino per un singolo controller di dominio. Considerare un potenziale scenario di caso peggiore, ovvero supporre che queste ~333 modifiche delle password in un singolo controller di dominio siano state effettuate nel giro di un'ora. Questo scenario, ad esempio, può verificarsi quando diversi dipendenti tornano tutti al lavoro il lunedì mattina. Anche in questo caso, si verificheranno ~333/60 minuti = sei modifiche di password al minuto, il che non costituisce comunque un carico significativo.

Se però i controller di dominio correnti già operano a livelli di prestazioni limitate (ad esempio, perché sono stati superati i limiti massimi di CPU, spazio su disco, I/O su disco e così via), prima di distribuire questa funzionalità è consigliabile aggiungere altri controller di dominio o espandere lo spazio su disco disponibile. Vedere anche la domanda precedente relativa all'uso dello spazio su disco per la condivisione sysvol.

**D: si vuole testare la funzionalità Password di protezione di Azure AD solo in pochi controller di dominio presenti nel dominio. È possibile forzare le modifiche delle password utente a usare tali controller di dominio specifici?**

No. È il sistema operativo client Windows a controllare quale controller di dominio viene usato quando un utente modifica la password. Il controller di dominio viene selezionato in base a fattori come le assegnazioni di subnet e siti di Active Directory, la configurazione di rete specifica dell'ambiente e così via. Password di protezione di Azure AD non controlla questi fattori e non può influire sulla selezione del controller di dominio per la modifica della password di un utente.

Un modo per raggiungere parzialmente questo obiettivo consiste nel distribuire Password di protezione di Azure AD in tutti i controller di dominio presenti in un determinato sito di Active Directory. Questo approccio garantirà una copertura ragionevole per i client Windows assegnati al sito e, di conseguenza, anche per gli utenti che accedono a tali client e modificano le loro password.

**D: se si installa il servizio agente del controller di dominio di Password di protezione di Azure AD solo nel controller di dominio primario (PDC), saranno protetti anche tutti gli altri controller di dominio presenti nel dominio?**

No. Quando la password di un utente viene modificata in un determinato controller di dominio non primario, la password non crittografata non viene mai inviata al controller di dominio primario (questa è un'idea errata molto diffusa). Dopo che una nuova password viene accettata in un controller di dominio, quest'ultimo usa tale password per crearne i vari hash specifici del protocollo di autenticazione e quindi rende permanenti gli hash nella directory. La password non crittografata non diventa permanente. Gli hash aggiornati vengono poi replicati nel controller di dominio primario. Le password utente possono in alcune circostanze essere modificate direttamente nel controller di dominio primario, anche in questo caso in base a vari fattori come la topologia di rete e la struttura del sito di Active Directory. Vedere la domanda precedente.

In sintesi, la distribuzione del servizio agente del controller di dominio di Password di protezione di Azure AD nel controller di dominio primario è necessaria per raggiungere una copertura di sicurezza della funzionalità pari al 100% all'interno del dominio. La distribuzione della funzionalità nel solo controller di dominio primario non offre i vantaggi di sicurezza di Password di protezione di Azure AD per gli altri controller di dominio presenti nel dominio.

**D: Perché il blocco Smart personalizzato non funziona anche dopo che gli agenti sono stati installati nell'ambiente di Active Directory locale?**

Il blocco intelligente personalizzato è supportato solo in Azure. Le modifiche apportate alle impostazioni di blocco Smart personalizzato nel portale di gestione di Azure non hanno alcun effetto sull'ambiente di Active Directory locale, anche con gli agenti installati.

**D: è disponibile un Management Pack di System Center Operations Manager per Password di protezione di Azure AD?**

No.

**D: Perché Azure rifiuta ancora le password vulnerabili anche se i criteri sono stati configurati in modalità di controllo?**

La modalità di controllo è supportata solo nell'ambiente di Active Directory locale. Azure è sempre in modo implicito in modalità "applica" quando valuta le password.

## <a name="additional-content"></a>Contenuto aggiuntivo

Gli articoli a cui si accede tramite i collegamenti seguenti non fanno parte della documentazione di base relativa a Password di protezione di Azure AD, ma possono essere una fonte utile di informazioni aggiuntive su tale funzionalità.

[Azure AD la protezione con password è ora disponibile a livello generale.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide - Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/) (Guida alla protezione dal phishing via posta elettronica - Parte 15: Implementare il servizio Password di protezione di Microsoft Azure AD (anche in locale))

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Password di protezione di Azure AD e il blocco intelligente sono ora disponibili in anteprima pubblica)

## <a name="microsoft-premierunified-support-training-available"></a>Disponibilità di training in caso contratti di supporto Microsoft Premier o Unified

Se è interessati ad approfondire la conoscenza di Password di protezione di Azure AD e la distribuzione di tale funzionalità nell'ambiente in uso, è possibile sfruttare i vantaggi di un servizio proattivo Microsoft disponibile per i clienti con un contratto di supporto Premier o Unified. Il servizio riguarda specificamente Azure Active Directory e Password di protezione. Per altre informazioni, contattare il technical account manager.

## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi su Password di protezione di Azure AD locale a cui questo articolo non abbia dato risposta, inviare il proprio feedback tramite la sezione riservata ai commenti di seguito.

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
