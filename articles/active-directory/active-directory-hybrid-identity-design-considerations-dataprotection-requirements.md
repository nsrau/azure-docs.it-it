<properties
	pageTitle="Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare i requisiti di protezione dei dati | Microsoft Azure"
	description="Quando si pianifica una soluzione di identità ibrida, è necessario identificare i requisiti aziendali di protezione dei dati e conoscere le opzioni disponibili per soddisfare adeguatamente tali requisiti."
	documentationCenter=""
	services="active-directory"
	authors="yuridio"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="yuridio"/>

#Pianificare il potenziamento della sicurezza dei dati attraverso soluzioni di identità avanzate

La prima azione da compiere per garantire la protezione dei dati è identificare gli utenti che possono accedere ad essi. Nell'ambito di questo processo, è necessario disporre di una soluzione di identità in grado di integrarsi con l'infrastruttura aziendale per poter fornire funzionalità di autenticazione e autorizzazione. L'autenticazione e l'autorizzazione vengono spesso confuse e i rispettivi ruoli fraintesi. Come illustrato nella figura seguente, rappresentano in realtà due concetti piuttosto diversi:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Fasi del ciclo di vita di gestione dispositivi mobili**

Quando si pianifica una soluzione di identità ibrida, è necessario comprendere i requisiti aziendali di protezione dei dati e conoscere le opzioni disponibili per soddisfare adeguatamente tali requisiti.
 
>[AZURE.NOTE]Dopo aver completato la pianificazione relativa alla sicurezza dei dati, leggere l'articolo [Determinare i requisiti dell'autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) per verificare che le selezioni effettuate in merito ai requisiti di autenticazione a più fattori non siano interessate dalle scelte compiute in questa sezione.

## Determinare i requisiti di protezione dei dati
Nell'era della mobilità, la maggior parte delle aziende ha un obiettivo comune: consentire agli utenti di essere produttivi anche con i dispositivi mobili personali, in locale o in remoto, favorendo così la produttività complessiva. Se da un lato cercano di perseguire questo obiettivo, dall'altro le aziende devono anche contrastare un numero crescente di minacce esterne, in modo da garantire la protezione dei dati e la privacy degli utenti. E in questo ambito è possibile che ogni azienda disponga di requisiti differenti; diverse regole di conformità, che variano in base al settore operativo, possono condurre a decisioni di progettazione diverse.

Esistono tuttavia alcuni aspetti di sicurezza di cui è importante tenere sempre conto, indipendentemente dal settore operativo, illustrati nella sezione seguente.

## Percorsi di protezione dei dati

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Percorsi di protezione dei dati**

Nel diagramma precedente, il componente di identità è il primo a essere verificato prima di eseguire l'accesso ai dati. Mentre si accede ai dati, tuttavia, questi possono trovarsi in vari tipi di stato. Ogni numero del diagramma rappresenta un percorso in cui possono trovarsi i dati in un determinato momento nel tempo. Ciascun numero viene spiegato di seguito:

1. Protezione dei dati a livello di dispositivo.
2. Protezione dei dati in transito.
3. Protezione dei dati quando sono inattivi in locale.
4. Protezione dei dati quando sono inattivi nel cloud.

Sebbene i controlli tecnici che consentono la protezione dei dati in ognuna di queste fasi non siano direttamente forniti dalla soluzione di identità ibrida, è necessario che questa sia in grado di usare le risorse di gestione delle identità, in locale o nel cloud, per identificare l'utente prima di concedergli l'accesso ai dati. Quando si pianifica una soluzione di identità ibrida, assicurarsi che venga fornita una risposta alle domande seguenti, in base ai requisiti aziendali:

## Protezione dei dati inattivi
Indipendentemente dalla posizione dei dati inattivi (sul dispositivo, nel cloud o in locale), è importante eseguire una valutazione accurata per comprendere le esigenze dell'azienda in questo ambito. Assicurarsi che venga fornita una risposta alle domande seguenti:

- Per l'azienda è importante proteggere i dati inattivi?
 - In caso affermativo, è possibile integrare la soluzione di identità ibrida con l'infrastruttura locale esistente?
 - In caso affermativo, è possibile integrare la soluzione di identità ibrida con i carichi di lavoro presenti nel cloud?
- Il sistema di gestione delle identità nel cloud è in grado di proteggere le credenziali utente e gli altri dati archiviati nel cloud?

## Protezione dei dati in transito
I dati in transito tra il dispositivo e il data center o tra il dispositivo e il cloud devono essere adeguatamente protetti. Il transito, tuttavia, non presuppone necessariamente un processo di comunicazione con un componente esterno al servizio cloud. È anche possibile, infatti, che i dati vengono spostati internamente, ad esempio tra due reti virtuali. Assicurarsi che venga fornita una risposta alle domande seguenti:

- Per l'azienda è importante proteggere i dati in transito?
 - In caso affermativo, è possibile integrare la soluzione di identità ibrida con controlli sicuri come SSL/TLS?
- Il sistema di gestione delle identità nel cloud mantiene firmato il traffico all'interno e verso l'archivio di directory (all'interno e tra i data center)?


## Conformità
Le regole, le norme e i requisiti di conformità alle normative variano in base al settore di appartenenza dell'azienda. Le aziende che operano in settori accuratamente regolamentati devono tener conto anche di aspetti di gestione delle identità correlati a problemi di conformità. Normative quali Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) e Payment Card Industry Data Security Standard (PCI DSS), infatti, sono molto rigorose in merito alle procedure di identità e accesso. Le caratteristiche della soluzione di identità ibrida adottata dall'azienda dovranno quindi soddisfare i requisiti previsti da una o più di queste normative. Assicurarsi che venga fornita una risposta alle domande seguenti:

- La soluzione di identità ibrida è conforme ai requisiti normativi a cui deve attenersi l'azienda?
- La soluzione di identità ibrida offre le funzionalità necessarie per consentire all'azienda di adeguarsi ai requisiti normativi? 
 
>[AZURE.NOTE]Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. L'articolo sulla [definizione della strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi. Rispondendo a queste domande sarà più facile scegliere l'opzione migliore in base alle specifiche esigenze aziendali.

## Passaggi successivi
 [Determinare i requisiti di gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->