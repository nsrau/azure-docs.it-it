<properties
	pageTitle="Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare le attività di gestione di un'identità ibrida | Microsoft Azure"
	description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/08/2016"
	ms.author="billmath"/>

# Pianificare il ciclo di vita dell’identità ibrida 

L'identità rappresenta uno degli elementi fondamentali della strategia di mobilità aziendale e di accesso alle applicazioni. Quando si accede a un dispositivo mobile personale o a un'app SaaS, l'identità è la chiave con cui si ottiene l'accesso a tutti i componenti. A livello generale, una soluzione di gestione delle identità prevede il consolidamento e la sincronizzazione degli archivi di identità, nonché l'automazione e la centralizzazione del processo di provisioning delle risorse. La soluzione di identità, inoltre, deve essere un'identità centralizzata in locale e nel cloud e ricorrere a una forma di federazione delle identità per gestire l'autenticazione centralizzata e condividere e collaborare con aziende e utenti esterni in modo totalmente sicuro. Le risorse comprendono i sistemi operativi, le applicazioni e gli utenti appartenenti o affiliati a un'azienda. La struttura aziendale, inoltre, può essere modificata per soddisfare determinati criteri o procedure di provisioning.

È importante anche disporre di una soluzione di identità pensata per offrire agli utenti la possibilità di restare produttivi mediante esperienze self-service. La soluzione di identità è più affidabile se consente agli utenti di eseguire un accesso di tipo Single Sign-On a tutte le risorse necessarie e se permette agli amministratori, a tutti i livelli, di usare procedure standard per la gestione delle credenziali utente. È comunque possibile ridurre o eliminare alcuni livelli di amministrazione, in base all'entità della soluzione di gestione dei processi di provisioning. È inoltre possibile distribuire funzionalità di amministrazione in aziende diverse, manualmente o automaticamente e in totale sicurezza. Ad esempio, un amministratore di dominio può gestire solo gli utenti e le risorse presenti nel proprio dominio. In questo caso, l'utente può eseguire attività amministrative e di provisioning, ma non è autorizzato a effettuare attività di configurazione, come la creazione di flussi di lavoro.


## Determinare le attività di gestione di un'identità ibrida
La distribuzione di attività amministrative all'interno di un'azienda consente un'amministrazione più accurata ed efficiente e permette di bilanciare meglio il carico di lavoro aziendale. Di seguito sono illustrati gli elementi principali che definiscono un sistema di gestione delle identità affidabile.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Per definire le attività di gestione di un'identità ibrida, è necessario identificare alcune caratteristiche essenziali dell'azienda che adotterà l'identità ibrida. È importante anche valutare gli archivi attualmente usati per le origini di identità. Conoscendo questi elementi di base, si potranno definire i requisiti fondamentali sulla base dei quali formulare domande più precise che consentano una decisione più consapevole della soluzione di identità da adottare.

Per definire correttamente tali requisiti, assicurarsi che venga fornita una risposta alle domande seguenti:

- Opzioni di provisioning:
 - La soluzione di identità ibrida prevede un sistema affidabile per il provisioning e la gestione delle credenziali di accesso all'account?
 - Come verrebbero gestiti gli utenti, i gruppi e le password?
 - La gestione del ciclo di vita dell'identità è sufficientemente reattiva?
      - Quanto dura la sospensione dell'account per l'aggiornamento delle password?
      
- Gestione delle licenze:
 - La soluzione di identità ibrida è in grado di gestire anche la gestione delle licenze?
     - In caso affermativo, quali funzionalità sono disponibili?
- La soluzione prevede anche la gestione delle licenze basata su gruppo?
      - In caso affermativo, è possibile assegnare ad essa un gruppo di sicurezza?
       - In caso affermativo, la directory cloud assegnerebbe automaticamente le licenze a tutti i membri del gruppo?
        - Cosa accede se, in seguito, un utente viene aggiunto o rimosso dal gruppo? Anche la licenza verrà assegnata o rimossa, a seconda del caso?

- Integrazione con provider di identità di terze parti:
- È possibile integrare questa soluzione ibrida con provider di identità di terze parti per implementare accessi di tipo Single Sign-On?
- È possibile consolidare tutti i diversi provider di identità in un sistema di identità coerente?
- In caso affermativo, che caratteristiche presentano e quali funzionalità offrono?

## Gestione della sincronizzazione
Uno dei principali obiettivi della gestione delle identità è quello di raccogliere tutti i provider di identità e mantenerli sincronizzati. Per la sincronizzazione dei dati viene usato un provider di identità autorevole. In uno scenario di identità ibrido, con un modello di gestione sincronizzato, si gestiscono tutte le identità utente e dispositivo in un server locale e, in seguito, si sincronizzano nel cloud gli account ed eventualmente le password. L'utente immette in locale la stessa password usata per il cloud e, al momento dell'accesso, la password viene verificata dalla soluzione di identità. Questo modello usa uno strumento di sincronizzazione di directory.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Per progettare correttamente la sincronizzazione della propria soluzione di identità ibrida, assicurarsi che venga fornita una risposta alle domande seguenti: • Quali sono le opzioni di sincronizzazione disponibili per la soluzione di identità ibrida? • Quali sono le funzionalità di Single Sign-On disponibili? • Quali sono le opzioni per una federazione delle identità tra B2B e B2C?

## Passaggi successivi
[Determinare la strategia di adozione di una soluzione per la gestione di un'identità ibrida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0928_2016-->