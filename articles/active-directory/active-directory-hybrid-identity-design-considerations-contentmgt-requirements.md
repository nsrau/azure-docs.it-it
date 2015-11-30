<properties
	pageTitle="Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare i requisiti di gestione dei contenuti | Microsoft Azure"
	description="Fornisce importanti informazioni su come determinare i requisiti di gestione dei contenuti dell'azienda. In genere, quando un utente usa un dispositivo personale, si avvale di più credenziali diverse, in base all'applicazione a cui deve accedere. È importante quindi distinguere i contenuti creati usando credenziali personali rispetto a quelli realizzati ricorrendo a credenziali aziendali. La soluzione di identità adottata deve essere in grado di interagire con i servizi cloud, in modo da fornire un'esperienza trasparente per l'utente finale, garantirne la privacy e migliorare la protezione contro la perdita di dati."
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

# Determinare i requisiti di gestione dei contenuti per una soluzione di identità ibrida

Comprendere i requisiti aziendali relativi alla gestione dei contenuti può incidere direttamente sulla decisione in merito alla soluzione di identità ibrida da usare. Con la proliferazione delle tipologie di dispositivi e la possibilità per gli utenti di usare dispositivi personali anche nelle attività lavorative (approccio [BYOD](http://aka.ms/byodcg)), è sempre più forte l'esigenza per le aziende di proteggere i dati senza compromettere la privacy degli utenti. In genere, quando un utente usa un dispositivo personale, si avvale di più credenziali diverse, in base all'applicazione a cui deve accedere. È importante quindi distinguere i contenuti creati usando credenziali personali rispetto a quelli realizzati ricorrendo a credenziali aziendali. La soluzione di identità adottata deve essere in grado di interagire con i servizi cloud, in modo da fornire un'esperienza trasparente per l'utente finale, garantirne la privacy e migliorare la protezione contro la perdita di dati.

La soluzione di identità deve essere inoltre sottoposta a vari controlli tecnici per permettere la gestione dei contenuti, come illustrato nella figura seguente:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controlli di protezione che interessano il sistema di gestione delle identità**

In generale, i requisiti di gestione dei contenuti interessano il sistema di gestione delle identità nelle aree seguenti:

- Privacy: identificare l'utente proprietario di una risorsa e applicare i controlli appropriati per garantirne l'integrità.
- Classificazione dei dati: identificare l'utente o il gruppo e il livello di accesso a un oggetto in base a una classificazione prestabilita. 
- Protezione contro la perdita di dati: per poter convalidare l'identità degli utenti, il sistema di gestione delle identità deve poter interagire con i controlli di protezione adottati per evitare perdite di dati. Questo aspetto è importante anche per eventuali esigenze di auditing.

>[AZURE.NOTE]Leggere l'articolo sulla [classificazione dei dati per la conformità al cloud](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) per maggiori informazioni sulle linee guida e le procedure consigliate per la classificazione dei dati.

Quando si pianifica una soluzione di identità ibrida, assicurarsi che venga fornita una risposta alle domande seguenti, in base ai requisiti aziendali:

- Sono presenti in azienda controlli di sicurezza per garantire la riservatezza dei dati?
 - In caso affermativo, sarà possibile integrarli con la soluzione di identità ibrida che si intende adottare?
- È presente in azienda un sistema di classificazione dei dati?
 - In caso affermativo, sarà possibile integrarlo con la soluzione di identità ibrida che si intende adottare?
- È presente in azienda un sistema di protezione contro la perdita di dati? 
 - In caso affermativo, sarà possibile integrarlo con la soluzione di identità ibrida che si intende adottare?
- L'azienda desidera controllare l'accesso alle risorse?
 - In caso affermativo, che tipo di risorse?
 - Quale livello di informazioni è necessario?
 - Dove deve trovarsi il log di controllo, in locale o nel cloud?
- L'azienda desidera crittografare i messaggi e-mail che contengono dati riservati (numeri di previdenza sociale, numeri di carta di credito e così via)?
- L'azienda desidera crittografare tutti i documenti e i contenuti condivisi con partner commerciali esterni?
- L'azienda intende applicare criteri specifici su determinati tipi di e-mail ("non rispondere a tutti", "non inoltrare" e così via)?
 
>[AZURE.NOTE]Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. L'articolo sulla [definizione della strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi. Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.


## Passaggi successivi
[Determinare i requisiti di controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=Nov15_HO4-->