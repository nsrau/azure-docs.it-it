---
title: "Considerazioni di progettazione della soluzione ibrida di gestione delle identità di Azure Active Directory - Determinare i requisiti di protezione dei dati | Documentazione Microsoft"
description: "Quando si pianifica una soluzione ibrida di gestione delle identità, è necessario identificare i requisiti aziendali di protezione dei dati e conoscere le opzioni disponibili per soddisfare adeguatamente tali requisiti."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 69f7b49fd82e4d34b1d54718cfbd2f4dedd2ae47
ms.openlocfilehash: bfa6413bd939b6082de4a88195b1f35cf8210375
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017


---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Pianificare il potenziamento della sicurezza dei dati attraverso soluzioni d’identità avanzate
La prima azione da compiere per garantire la protezione dei dati è identificare gli utenti che possono accedere ad essi. Nell'ambito di questo processo, è necessario disporre di una soluzione di gestione delle identità in grado di integrarsi con l'infrastruttura aziendale per poter fornire funzionalità di autenticazione e autorizzazione. L'autenticazione e l'autorizzazione vengono spesso confuse e i rispettivi ruoli fraintesi. Come illustrato nella figura seguente, rappresentano in realtà due concetti piuttosto diversi:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**Fasi del ciclo di vita di gestione dei dispositivi mobili**

Quando si pianifica una soluzione ibrida di gestione delle identità, è necessario comprendere i requisiti aziendali di protezione dei dati e conoscere le opzioni disponibili per soddisfare adeguatamente tali requisiti.

> [!NOTE]
> Dopo aver completato la pianificazione relativa alla sicurezza dei dati, leggere l'articolo [Determinare i requisiti dell'autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) per verificare che le selezioni effettuate in merito ai requisiti di autenticazione a più fattori non siano interessate dalle scelte compiute in questa sezione.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinare i requisiti di protezione dei dati
Nell'era della mobilità, la maggior parte delle aziende ha un obiettivo comune: consentire agli utenti di essere produttivi con i dispositivi mobili personali, in locale o in remoto, favorendo così la produttività. Se da un lato cercano di perseguire questo obiettivo, dall'altro le aziende devono anche contrastare un numero crescente di minacce esterne, in modo da garantire la sicurezza dei dati e la privacy degli utenti. In questo ambito è possibile che ogni azienda disponga di requisiti differenti. Diverse regole di conformità, che variano in base al settore operativo, possono condurre a decisioni di progettazione diverse. 

Esistono tuttavia alcuni aspetti di sicurezza di cui è importante tenere sempre conto, indipendentemente dal settore operativo, illustrati nella sezione seguente.

## <a name="data-protection-paths"></a>Percorsi di protezione dei dati
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**Percorsi di protezione dei dati**

Nel diagramma precedente, il componente di identità è il primo a essere verificato prima di eseguire l'accesso ai dati. Mentre si accede ai dati, tuttavia, questi possono trovarsi in vari tipi di stato. Ogni numero del diagramma rappresenta un percorso in cui possono trovarsi i dati in un determinato momento nel tempo. I numeri vengono spiegati di seguito:

1. Protezione dei dati a livello di dispositivo.
2. Protezione dei dati in transito.
3. Protezione dei dati quando sono inattivi in locale.
4. Protezione dei dati quando sono inattivi nel cloud.

Sebbene i controlli tecnici che consentono la protezione dei dati in ognuna di queste fasi non siano direttamente forniti dalla soluzione ibrida di gestione delle identità, è necessario che questa sia in grado di usare le risorse di gestione delle identità, in locale o nel cloud, per identificare l'utente prima di concedergli l'accesso ai dati. Quando si pianifica una soluzione ibrida di gestione delle identità, assicurarsi che venga fornita una risposta alle domande seguenti, in base ai requisiti aziendali:

## <a name="data-protection-at-rest"></a>Protezione dei dati inattivi
Indipendentemente dalla posizione dei dati inattivi (sul dispositivo, nel cloud o in locale), è importante eseguire una valutazione per comprendere le esigenze dell'azienda in questo ambito. Assicurarsi che venga fornita una risposta alle domande seguenti:

* Per l'azienda è importante proteggere i dati inattivi?
  * In caso affermativo, è possibile integrare la soluzione ibrida di gestione delle identità con l'infrastruttura locale esistente?
  * In caso affermativo, è possibile integrare la soluzione ibrida di gestione delle identità con i carichi di lavoro presenti nel cloud?
* Il sistema di gestione delle identità nel cloud è in grado di proteggere le credenziali utente e gli altri dati archiviati nel cloud?

## <a name="data-protection-in-transit"></a>Protezione dei dati in transito
I dati in transito tra il dispositivo e il data center o tra il dispositivo e il cloud devono essere protetti. Il transito, tuttavia, non presuppone necessariamente un processo di comunicazione con un componente esterno al servizio cloud. È anche possibile, infatti, che i dati vengono spostati internamente, ad esempio tra due reti virtuali. Assicurarsi che venga fornita una risposta alle domande seguenti:

* Per l'azienda è importante proteggere i dati in transito?
  * In caso affermativo, è possibile integrare la soluzione ibrida di gestione delle identità con controlli sicuri come SSL/TLS?
* Il sistema di gestione delle identità nel cloud mantiene firmato il traffico all'interno e verso l'archivio di directory (all'interno e tra i data center)?

## <a name="compliance"></a>Conformità
Le regole, le norme e i requisiti di conformità alle normative variano in base al settore di appartenenza dell'azienda. Le aziende che operano in settori altamente regolamentati devono tener conto di aspetti di gestione delle identità correlati a problemi di conformità. Normative quali Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) e Payment Card Industry Data Security Standard (PCI DSS), infatti, sono molto rigorose in merito alle procedure di identità e accesso. Le caratteristiche di base della soluzione ibrida di gestione delle identità adottata dall'azienda dovranno quindi soddisfare i requisiti previsti da una o più di queste normative. Assicurarsi che venga fornita una risposta alle domande seguenti:

* La soluzione ibrida di gestione delle identità è conforme ai requisiti normativi a cui deve attenersi l'azienda?
* La soluzione ibrida di gestione delle identità offre le funzionalità necessarie per consentire all'azienda di adeguarsi ai requisiti normativi? 

> [!NOTE]
> Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. [definizione della strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi.  Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
 [Determinare i requisiti di gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)


