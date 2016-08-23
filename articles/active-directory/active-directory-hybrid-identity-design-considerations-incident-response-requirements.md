
<properties
	pageTitle="Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare i requisiti di risposta a eventi imprevisti | Requisiti di Microsoft Azure "
	description="Determinare le funzionalità di monitoraggio e reporting della soluzione di identità ibrida di cui può avvalersi il personale IT per identificare e contrastare potenziali minacce."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/08/2016"
	ms.author="billmath"/>  

# Determinare i requisiti di risposta agli eventi imprevisti per una soluzione di identità ibrida

La maggior parte delle aziende di medie o grandi dimensioni dovrà definire una [risposta ad eventi imprevisti](https://technet.microsoft.com/library/cc700825.aspx) in materia di sicurezza per consentire al personale IT di prendere le azioni appropriate in base al livello di gravità. Il sistema di gestione delle identità è una componente importante del processo di risposta agli eventi imprevisti, poiché consente di identificare l'utente che ha eseguito una determinata azione nel perseguimento dell'obiettivo. La soluzione di identità ibrida, inoltre, deve essere in grado di fornire funzionalità di monitoraggio e reporting di cui può avvalersi il personale IT per intraprendere le azioni necessarie per identificare e contrastare una potenziale minaccia. In un piano di risposta agli eventi imprevisti, sono generalmente contenute le fasi seguenti:

1.	Valutazione iniziale.
2.	Comunicazione dell'evento.
3.	Controllo dei danni e riduzione dei rischi.
4.	Identificazione degli elementi danneggiati e livello di gravità.
5.	Conservazione delle prove.
6.	Informazione delle parti interessate.
7.	Ripristino del sistema.
8.	Documentazione.
9.	Valutazione dei danni e dei costi.
10.	Revisione del piano e del processo.

Durante l'identificazione degli elementi danneggiati e la definizione del livello di gravità, sarà necessario identificare i sistemi che sono stati compromessi e i file ai quali è stato eseguito l'accesso, determinandone anche il livello di riservatezza. Il sistema di identità ibrida deve essere in grado di soddisfare tali requisiti in modo da permettere l'identificazione dell'utente che ha effettuato tali modifiche.

## Monitoraggio e reporting
In molti casi, il sistema di identità è utile anche nella fase di valutazione iniziale, soprattutto se dispone di funzionalità di controllo e reporting integrate. Durante la valutazione iniziale, l'amministratore IT deve essere in grado di identificare eventuali attività sospette oppure è necessario che il sistema lo informi automaticamente in base a un'attività preconfigurata. Sebbene alcune attività possano effettivamente indicare un possibile attacco, in altri casi un sistema mal configurato può generare numerosi falsi positivi nell'ambito di un sistema di rilevamento delle intrusioni.

Il sistema di gestione delle identità deve aiutare gli amministratori IT a identificare e segnalare qualsiasi attività sospetta. In genere, è possibile soddisfare questi requisiti tecnici monitorando tutti i sistemi e adottando una funzionalità di reporting in grado di evidenziare potenziali minacce. Usare le domande seguenti per definire una soluzione di identità ibrida tenendo conto dei requisiti di risposta agli eventi imprevisti:

- L'azienda ha già definito una risposta ad eventi imprevisti in materia di sicurezza?
 - In caso affermativo, il sistema di gestione delle identità in uso è coinvolto in questo processo?
- L'azienda ha esigenza di identificare eventuali tentativi di accesso sospetti eseguiti con vari tipi di dispositivi?
- Per l'azienda può essere importante rilevare credenziali utente potenzialmente compromesse?
- L'azienda desidera poter controllare gli accessi e le azioni degli utenti?
- L'azienda desidera sapere quando un utente reimposta la password?

## Imposizione dei criteri

Durante le fasi di controllo dei danni e riduzione dei rischi, è importante limitare rapidamente gli effetti, potenziali ed effettivi, dell'attacco. Le azioni che vengono intraprese in questa fase possono fare una grande differenza a questo proposito. La risposta esatta dipende dal tipo di azienda e dalla natura dell'attacco che si sta affrontando. Se dalla valutazione iniziale è emerso che è stato compromesso un account, è necessario applicare il criterio che impone il blocco dell'account. Questo, ovviamente, è solo uno dei tanti modi in cui è possibile avvalersi del sistema di gestione delle identità. Usare le domande seguenti per definire una soluzione di identità ibrida tenendo conto delle modalità di applicazione dei criteri per rispondere a eventi imprevisti:

- L'azienda ha già definito dei criteri che, se necessario, impediscono agli utenti di accedere alla rete?
 - In caso affermativo, sarà possibile integrarli con la soluzione di gestione dell'identità ibrida che si intende adottare?
- L'azienda desidera poter attivare l'accesso condizionale per gli utenti in quarantena?
 
>[AZURE.NOTE]
Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. L'articolo sulla [definizione della strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi. Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.

## Passaggi successivi
[Definire la strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0810_2016-->