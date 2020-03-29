---
title: Domande frequenti sulla protezione password di Azure AD locale
description: Esaminare le domande frequenti sulla protezione delle password di Azure AD in un ambiente di Servizi di dominio Active Directory localeReview frequently asked questions for Azure AD Password Protection in an on-premises Active Directory Domain Services environment
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: abef6e52e37cfa2faeb426bc59bb0de5a52a6658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671673"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Domande frequenti su Azure AD Password Protection in localeAzure AD Password Protection on-premises frequently asked questions

In questa sezione vengono fornite le risposte a molte domande frequenti su Azure AD Password Protection.This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>Domande generali

**D: Quali indicazioni devono essere fornite agli utenti su come selezionare una password sicura?**

Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

**D: La protezione con password di Azure AD locale è supportata nei cloud non pubblici?**

No, la protezione password di Azure AD locale è supportata solo nel cloud pubblico. Non è stata annunciata alcuna data per la disponibilità nei cloud non pubblici.

Il portale di Azure AD consente la modifica della configurazione "Password protection for Windows Server Active Directory" specifica dell'ambiente locale anche in cloud non pubblici. tali modifiche saranno perseverate, ma altrimenti non avranno mai effetto. La registrazione di foreste o agenti proxy locali non è supportata quando vengono utilizzate credenziali cloud non pubbliche e qualsiasi tentativo di registrazione avrà sempre esito negativo.

**D: In che modo è possibile applicare i vantaggi di Azure AD Password Protection a un sottoinsieme di utenti locali?**

Non supportato. Dopo la distribuzione e l'abilitazione, Password di protezione di Azure AD non prevede alcuna discriminazione: tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**D: Qual è la differenza tra una modifica della password e un set di password (o reimpostazione)?**

Una modifica della password è quando un utente sceglie una nuova password dopo aver dimostrato di essere a conoscenza della vecchia password. Ad esempio, una modifica della password è ciò che accade quando un utente accede a Windows e viene quindi richiesto di scegliere una nuova password.

Una password impostata (talvolta chiamata reimpostazione della password) è quando un amministratore sostituisce la password di un account con una nuova password, ad esempio utilizzando lo strumento di gestione Utenti e computer di Active Directory. Questa operazione richiede un elevato livello di privilegi (in genere Domain Admin) e la persona che esegue l'operazione in genere non è a conoscenza della vecchia password. Gli scenari di help desk spesso eseguono set di password, ad esempio quando si assiste un utente che ha dimenticato la password. Vedrai anche gli eventi di impostazione della password quando un nuovo account utente viene creato per la prima volta con una password.

Il criterio di convalida della password si comporta allo stesso modo indipendentemente dal fatto che venga eseguita una modifica o un set di password. Il servizio Agente controller di dominio di Azure AD Password Protection registra eventi diversi per informare l'utente se è stata eseguita un'operazione di modifica o impostazione della password.  Vedere Monitoraggio e registrazione di [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**D: Perché vengono registrati gli eventi di rifiuto della password duplicati quando si tenta di impostare una password debole utilizzando lo snap-in di gestione Utenti e computer di Active Directory?**

Lo snap-in di gestione Utenti e computer di Active Directory tenterà innanzitutto di impostare la nuova password utilizzando il protocollo Kerberos. In caso di errore, lo snap-in effettuerà un secondo tentativo di impostare la password utilizzando un protocollo legacy (SAM RPC) (i protocolli specifici utilizzati non sono importanti). Se la nuova password viene considerata debole da Azure AD Password Protection, questo comportamento dello snap-in comporterà la registrazione di due set di eventi di rifiuto della reimpostazione della password.

**D: Perché gli eventi di convalida della password di Azure AD Password Protection vengono registrati con un nome utente vuoto?**

Active Directory supporta la possibilità di testare una password per verificare se supera i requisiti di complessità della password correnti del dominio, ad esempio utilizzando l'API [NetValidatePasswordPolicy.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Quando una password viene convalidata in questo modo, il test include anche la convalida tramite prodotti basati su password basati su filtri di password, ad esempio Protezione password di Azure AD, ma i nomi utente passati a una dll del filtro password specificata saranno vuoti. In questo scenario, Azure AD Password Protection continuerà a convalidare la password usando i criteri password attualmente attivi e genererà un messaggio del registro eventi per acquisire il risultato, tuttavia il messaggio del log eventi avrà campi del nome utente vuoti.

**D: È supportata l'installazione di Azure AD Password Protection affiancata ad altri prodotti basati su filtri password?**

Sì. Il supporto di più DLL di filtro password registrate è una funzionalità di base di Windows e non è specifico della protezione password di Azure AD. Tutte le DLL di filtro password registrate devono concordare, prima che venga accettata una password.

**D: In che modo è possibile distribuire e configurare Azure AD Password Protection nell'ambiente Active Directory senza usare Azure?**

Non supportato. Password di protezione di Azure AD è una funzionalità di Azure che supporta l'estensione in un ambiente Active Directory locale.

**D: Come è possibile modificare il contenuto dei criteri a livello di Active Directory?**

Non supportato. I criteri possono essere amministrati solo tramite il portale di Azure AD. Vedere anche la domanda precedente.

**D: perché è necessario il servizio DFSR per la replica di sysvol?**

Il servizio Replica file (la tecnologia precedente a DFSR) include molti problemi noti e non è completamente supportato nelle versioni più recenti di Windows Server Active Directory. La protezione password di Azure AD non verrà testata nei domini configurati con il servizio Replica file.

Per altre informazioni, vedere gli articoli seguenti:

[The Case for Migrating sysvol replication to DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) (Caso della migrazione della replica di sysvol a DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (La fine è vicina per il servizio Replica file)

Se il dominio non usa già DFSR, è necessario eseguirne la migrazione per usare DFSR prima di installare Azure AD Password Protection. Per ulteriori informazioni, vedere il seguente collegamento:

[Guida alla migrazione della replica SYSVOL: Replica da FRS a DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Il software Azure AD Password Protection controller di dominio verrà attualmente installato nei controller di dominio in domini che utilizzano ancora FRS per la replica sysvol, ma il software NON funzionerà correttamente in questo ambiente. Ulteriori effetti collaterali negativi includono singoli file che non vengono replicati e le procedure di ripristino sysvol sembrano avere esito positivo, ma non riescono a replicare tutti i file. È consigliabile eseguire la migrazione del dominio per usare DFSR il prima possibile, sia per i vantaggi intrinseci di DFSR che per sbloccare la distribuzione di Azure AD Password Protection. Le versioni future del software verranno disabilitate automaticamente quando vengono eseguite in un dominio che utilizza ancora FRS.

**D: Quanto spazio su disco è necessaria per la funzionalità nella condivisione sysvol del dominio?**

La quantità esatta di spazio usato dipende da fattori come il numero e la lunghezza dei token esclusi contenuti nell'elenco globale degli elementi esclusi Microsoft e nell'elenco personalizzato del singolo tenant, nonché dal sovraccarico della crittografia. Il contenuto di questi elenchi probabilmente aumenterà in futuro. Tenendo presente tale situazione, è ragionevole prevedere che per la funzionalità siano necessari almeno cinque (5) megabyte di spazio nella condivisione sysvol del dominio.

**D: perché è necessario un riavvio per installare o aggiornare il software dell'agente del controller di dominio?**

Questo requisito dipende dal comportamento di base di Windows.

**D: è disponibile un modo per configurare un agente del controller di dominio per l'uso di un server proxy specifico?**

No. Poiché il server proxy è senza stato, non ha importanza quale server proxy specifico venga usato.

**D: È possibile distribuire il servizio Proxy di password di Azure AD().**

Sì. Il servizio proxy di Password di protezione di Azure AD e Azure AD Connect non devono essere mai direttamente in conflitto l'uno con l'altro.

Sfortunatamente, è stata rilevata un'incompatibilità tra la versione del servizio Microsoft Azure AD Connect Agent Updater installata dal software Proxy di protezione password di Azure AD e la versione del servizio installato dal software [Proxy applicazione di Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Questa incompatibilità può comportare l'impossibilità del servizio Agent Updater di contattare Azure per gli aggiornamenti software. Non è consigliabile installare il proxy di protezione password di Azure AD e il proxy di applicazione di Azure Active Directory nello stesso computer.

**D: In quale ordine gli agenti e i proxy del controller di dominio devono essere installati e registrati?**

È supportato qualsiasi ordine dell'installazione dell'agente proxy, dell'installazione dell'agente controller di dominio, della registrazione della foresta e della registrazione proxy.

**D: È necessario preoccuparsi per l'hit delle prestazioni nei controller di dominio di distribuire questa funzionalità?**

Il servizio agente del controller di dominio di Password di protezione di Azure AD non dovrebbe incidere in modo significativo sulle prestazioni dei controller di dominio in una distribuzione di Active Directory esistente e integra.

Per la maggior parte delle distribuzioni di Active Directory attive, le operazioni di modifica delle password rappresentano una piccola parte del carico di lavoro complessivo in un determinato controller di dominio. Ad esempio, si supponga l'esistenza di un dominio di Active Directory con 10000 account utente e criteri MaxPasswordAge impostati su 30 giorni. In media questo dominio vedrà 10000/30=~333 operazioni di modifica delle password ogni giorno, il che rappresenta un numero trascurabile di operazioni persino per un singolo controller di dominio. Considerare un potenziale scenario di caso peggiore, ovvero supporre che queste ~333 modifiche delle password in un singolo controller di dominio siano state effettuate nel giro di un'ora. Questo scenario, ad esempio, può verificarsi quando diversi dipendenti tornano tutti al lavoro il lunedì mattina. Anche in questo caso, si verificheranno ~333/60 minuti = sei modifiche di password al minuto, il che non costituisce comunque un carico significativo.

Se però i controller di dominio correnti già operano a livelli di prestazioni limitate (ad esempio, perché sono stati superati i limiti massimi di CPU, spazio su disco, I/O su disco e così via), prima di distribuire questa funzionalità è consigliabile aggiungere altri controller di dominio o espandere lo spazio su disco disponibile. Vedere anche la domanda precedente relativa all'uso dello spazio su disco per la condivisione sysvol.

**D: Voglio testare Azure AD Password Protection in pochi controller di dominio nel mio dominio. È possibile forzare le modifiche della password utente a utilizzare tali controller di dominio specifici?**

No. È il sistema operativo client Windows a controllare quale controller di dominio viene usato quando un utente modifica la password. Il controller di dominio viene selezionato in base a fattori quali le assegnazioni di siti e subnet di Active Directory, la configurazione di rete specifica dell'ambiente e così via. Azure AD Password Protection non controlla questi fattori e non può influenzare quale controller di dominio è selezionato per modificare la password di un utente.

Un modo per raggiungere parzialmente questo obiettivo consiste nel distribuire Password di protezione di Azure AD in tutti i controller di dominio presenti in un determinato sito di Active Directory. Questo approccio garantirà una copertura ragionevole per i client Windows assegnati al sito e, di conseguenza, anche per gli utenti che accedono a tali client e modificano le loro password.

**D: Se si installa il servizio Agente controller di dominio di Azure AD Password Protection solo nel controller di dominio primario (PDC), verranno protetti anche tutti gli altri controller di dominio nel dominio?**

No. Quando la password di un utente viene modificata in un determinato controller di dominio non primario, la password non crittografata non viene mai inviata al controller di dominio primario (questa è un'idea errata molto diffusa). Dopo che una nuova password viene accettata in un controller di dominio, quest'ultimo usa tale password per crearne i vari hash specifici del protocollo di autenticazione e quindi rende permanenti gli hash nella directory. La password non crittografata non diventa permanente. Gli hash aggiornati vengono poi replicati nel controller di dominio primario. Le password utente possono in alcune circostanze essere modificate direttamente nel controller di dominio primario, anche in questo caso in base a vari fattori come la topologia di rete e la struttura del sito di Active Directory. Vedere la domanda precedente.

In sintesi, la distribuzione del servizio agente del controller di dominio di Password di protezione di Azure AD nel controller di dominio primario è necessaria per raggiungere una copertura di sicurezza della funzionalità pari al 100% all'interno del dominio. La distribuzione della funzionalità nel solo controller di dominio primario non offre i vantaggi di sicurezza di Password di protezione di Azure AD per gli altri controller di dominio presenti nel dominio.

**D: Perché il blocco intelligente personalizzato non funziona anche dopo l'installazione degli agenti nell'ambiente Active Directory locale?**

Il blocco intelligente personalizzato è supportato solo in Azure AD. Le modifiche alle impostazioni di blocco intelligente personalizzate nel portale di Azure AD non hanno alcun effetto sull'ambiente Active Directory locale, anche con gli agenti installati.

**D: Un Management Pack di System Center Operations Manager è disponibile per Azure AD Password Protection?**

No.

**D: Perché Azure AD rifiuta ancora le password deboli anche se ho configurato il criterio in modalità di controllo?**

La modalità di controllo è supportata solo nell'ambiente Active Directory locale. Azure AD è implicitamente sempre in modalità "imporre" quando valuta le password.

**D: Gli utenti visualizzano il messaggio di errore tradizionale di Windows quando una password viene rifiutata da Azure AD Password Protection. È possibile personalizzare questo messaggio di errore in modo che gli utenti sappiano cosa è realmente successo?**

No. Il messaggio di errore visualizzato dagli utenti quando una password viene rifiutata da un controller di dominio è controllato dal computer client, non dal controller di dominio. Questo comportamento si verifica se una password viene rifiutata dai criteri password di Active Directory predefiniti o da una soluzione basata su filtro password, ad esempio Azure AD Password Protection.

## <a name="additional-content"></a>Contenuto aggiuntivo

Gli articoli a cui si accede tramite i collegamenti seguenti non fanno parte della documentazione di base relativa a Password di protezione di Azure AD, ma possono essere una fonte utile di informazioni aggiuntive su tale funzionalità.

[Azure AD Password Protection è ora disponibile in generale.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guida alla protezione dal phishing della posta elettronica - Parte 15: Implementare il servizio di protezione password di Microsoft Azure AD (anche per locale!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Password di protezione di Azure AD e il blocco intelligente sono ora disponibili in anteprima pubblica)

## <a name="microsoft-premierunified-support-training-available"></a>Disponibilità di training in caso contratti di supporto Microsoft Premier o Unified

Se è interessati ad approfondire la conoscenza di Password di protezione di Azure AD e la distribuzione di tale funzionalità nell'ambiente in uso, è possibile sfruttare i vantaggi di un servizio proattivo Microsoft disponibile per i clienti con un contratto di supporto Premier o Unified. Il servizio è denominato Azure Active Directory: Password Protection.The service is called Azure Active Directory: Password Protection. Per altre informazioni, contattare il technical account manager.

## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi su Password di protezione di Azure AD locale a cui questo articolo non abbia dato risposta, inviare il proprio feedback tramite la sezione riservata ai commenti di seguito.

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
