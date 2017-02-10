---
title: "Considerazioni di progettazione della soluzione ibrida di gestione delle identità di Azure Active Directory - Determinare i requisiti di sincronizzazione delle directory | Microsoft Docs"
description: Identificare i requisiti necessari per sincronizzare tutti gli utenti per le applicazioni locali e nel cloud.
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 32039e6c9291879a4151cd12dfa5dac11a153ee0


---
# <a name="determine-directory-synchronization-requirements"></a>Determinare i requisiti di sincronizzazione delle directory
La sincronizzazione consiste nel fornire agli utenti un'identità nel cloud basata sulla rispettiva identità locale. Gli utenti, infatti, devono necessariamente disporre di un'identità nel cloud, indipendentemente dal fatto che per autenticarsi si avvalgano di un account sincronizzato o di un'autenticazione federata.  L'identità dovrà inoltre essere gestita e aggiornata periodicamente.  Gli aggiornamenti possono assumere molte forme, tra cui modifiche al titolo o alla password.  

È necessario valutare in primo luogo i requisiti degli utenti e della soluzione di identità locale dell'azienda. Questa valutazione è importante per definire i requisiti tecnici cui attenersi per creare e mantenere le identità utente nel cloud.  Nella maggior parte delle aziende, Active Directory si trova in locale e costituisce la directory locale con la quale vengono sincronizzati gli utenti. In alcuni casi, tuttavia, la situazione può essere diversa.  

Rispondere alle domande seguenti:

* Sono presenti una o più foreste di Active Directory?
  
  * Con quante directory di Azure Active Directory viene eseguita la sincronizzazione?
    
    1. Sono attivi dei filtri?
    2. Sono stati pianificati più server Azure AD Connect?
* È presente uno strumento di sincronizzazione locale?
  
  * In caso affermativo, gli utenti dispongono di una directory virtuale/integrazione delle identità?
* Sono presenti altre directory locali che si desidera sincronizzare (ad esempio, la directory LDAP, il database delle risorse umane e così via)?
  * Si intende eseguire sincronizzazioni con GALSync?
  * Qual è lo stato corrente dei nomi di entità utente (UPN) presenti in azienda? 
  * È presente una directory diversa per l'autenticazione degli utenti?
  * Si usa Microsoft Exchange in azienda?
    * Si prevede di eseguire una distribuzione ibrida di Exchange?

Dopo aver determinato i requisiti di sincronizzazione, è necessario ora individuare lo strumento corretto per soddisfare tali requisiti.  Microsoft offre diversi strumenti per eseguire la sincronizzazione e l'integrazione di directory.  Per altre informazioni, vedere [Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità](active-directory-hybrid-identity-design-considerations-tools-comparison.md) . 

Dopo aver individuato i requisiti di sincronizzazione e lo strumento corretto per la propria azienda, è necessario ora valutare le applicazioni che useranno i servizi di directory. Questa valutazione è importante per definire i requisiti tecnici cui attenersi per integrare le applicazioni nel cloud. Rispondere alle domande seguenti:

* Queste applicazioni verranno migrate nel cloud e useranno la directory?
* Sono presenti attributi speciali da sincronizzare nel cloud in modo che le applicazioni possano usarli correttamente?
* Sarà necessario riscrivere le applicazioni per poter usufruire dei vantaggi offerti dall'autenticazione nel cloud?
* Le applicazioni continueranno a risiedere in locale anche se gli utenti vi accederanno tramite l'identità cloud?

È inoltre necessario definire la sincronizzazione delle directory relative ai requisiti di sicurezza e alle limitazioni. Questa valutazione è importante per ottenere un elenco dei requisiti a cui attenersi per creare e gestire le identità utente nel cloud. Rispondere alle domande seguenti:

* Dove verrà installato il server di sincronizzazione?
* Verrà aggiunto al dominio?
* Il server verrà installato in una rete con limitazioni dietro un firewall, ad esempio una rete perimetrale?
  * Sarà possibile aprire le porte del firewall necessarie per supportare la sincronizzazione?
* È stato definito un piano di ripristino di emergenza per il server di sincronizzazione?
* Si dispone di un account con le autorizzazioni appropriate per tutte le foreste con cui si desidera eseguire la sincronizzazione?
  * Se non si conosce la risposta a questa domanda, consultare la sezione "Autorizzazioni per la sincronizzazione delle password" dell'articolo [Installare il servizio Azure Active Directory Sync](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e verificare se si dispone già di un account con queste autorizzazioni o se è necessario crearne uno.
* Se è prevista la sincronizzazione con più foreste, il server di sincronizzazione è in grado di accedere a ognuna di esse?

> [!NOTE]
> Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. [Determinare i requisiti di risposta agli eventi imprevisti](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) illustrerà le opzioni disponibili. Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
[Determinare i requisiti dell'autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)




<!--HONumber=Dec16_HO4-->


