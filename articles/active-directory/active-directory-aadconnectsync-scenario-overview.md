<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Panoramica dello scenario a più foreste"
    description="Questo argomento illustra alcuni scenari comuni e il modo in cui questi vengono rappresentati in Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Servizio di sincronizzazione Azure AD Connect: Panoramica dello scenario a più foreste

In molte organizzazioni sono presenti ambienti che includono più foreste Active Directory locali. Esistono vari motivi per distribuire più di una foresta Active Directory locale. Tra gli esempi tipici sono incluse progettazioni con foreste di risorse di account, foreste relative a fusioni e acquisizioni o foreste usate per la gestione esterna dei dati.

A questo scopo, Microsoft offre DirSync, una soluzione per scenari con singola foresta e una soluzione FIM (Forefront Identity Manager) per gli scenari a più foreste. La configurazione di FIM può risultare complessa e richiedere una notevole quantità di tempo.

Azure AD Connect Sync consente di semplificare la configurazione, rendendola più prevedibile.

Nella configurazione predefinita disponibile in Azure AD Connect Sync si presuppone quanto segue:

1. Gli utenti dispongono solo di un account abilitato e la foresta in cui si trova l'account viene usata per la federazione dell'utente.
2. Gli utenti dispongono di una sola cassetta postale.
3. La foresta che ospita la cassetta postale dell'utente garantisce la migliore qualità dei dati per gli attributi visibili nell'Elenco indirizzi globale di Exchange. Se non è presente una cassetta postale per l'utente, è possibile usare qualsiasi foresta per recuperare questi valori di attributi.


Questo argomento illustra alcuni scenari comuni e il modo in cui questi vengono rappresentati in Azure AD Connect Sync:

1. Tecnologie separate 
2. A maglia completa con GALSync facoltativo 
3. Foresta Account-Risorse 


## Tecnologie separate

In questo ambiente tutte le foreste locali sono considerate come entità separate e nessun utente di una foresta può essere incluso in un'altra foresta.<br> Ogni foresta presenta un'organizzazione Exchange dedicata e non viene effettuata alcuna sincronizzazione dell'elenco di indirizzi globale (GALSync) tra le foreste. Questa situazione può verificarsi dopo una fusione/acquisizione o in un'organizzazione in cui ogni business unit opera in modo isolato rispetto alle altre.

![Tecnologie separate][1]

In questa immagine ogni oggetto di ogni foresta verrà rappresentato una volta sola nello spazio metaverse e aggregato nella directory di Azure AD di destinazione. Il risultato è lo stesso che si otterrebbe con un server DirSync connesso a ogni foresta AD di origine.
 




## A maglia completa con GALSync facoltativo

In una topologia a maglia completa utenti e risorse possono risiedere in qualsiasi foresta e in genere tra le foreste sono presenti trust bidirezionali.

Se Exchange è installato in più di una foresta, potrebbe essere presente una soluzione GALSync che rappresenta un utente in una foresta come un contatto in ogni altra foresta.

In questo scenario, gli oggetti relativi all'identità vengono aggiunti tramite l'attributo della posta. Di conseguenza, un utente con una cassetta postale in una foresta viene aggiunto ai contatti nelle altre foreste. I gruppi di distribuzione e di sicurezza sono presenti in ogni foresta e possono contenere una combinazione di utenti, contatti ed entità di sicurezza esterne.

La figura seguente descrive questo scenario.

![A maglia completa con GALSync facoltativo][2]


## Foresta Account-Risorse
In una topologia di foresta account-risorse, sono presenti una o più foreste di account con account utente attivi.<br> Questo scenario include una foresta che considera attendibili tutte le foreste di account. Questa foresta presenta in genere uno schema Active Directory esteso con Exchange e Lync. Tutti i servizi di Exchange e Lync, nonché altri servizi condivisi si trovano in questa foresta. Gli account utente degli utenti in questa foresta sono disabilitati e la cassetta postale è collegata alla foresta di account.

L'immagine seguente descrive questo scenario con un solo account.

![Foresta Account-Risorse][3]


## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=August15_HO6-->