---
title: 'Azure AD Connect: topologie supportate | Microsoft Docs'
description: Questo argomento illustra in modo dettagliato le topologie supportate e non supportate per Azure AD Connect
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: dbf531302e53ca52e24dbd2ba954defad391060f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie per Azure AD Connect
Questo articolo descrive diverse topologie locali e di Azure Active Directory (Azure AD) che usano il Servizio di sincronizzazione Azure AD Connect come soluzione di integrazione chiave. Questo articolo include le configurazioni supportate e non supportate.

Ecco la legenda delle immagini usate nell'articolo:

| DESCRIZIONE | Simbolo |
| --- | --- |
| Foresta locale di Active Directory |![Foresta locale di Active Directory](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Active Directory locale con importazione con filtri |![Active Directory con importazione con filtri](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Server di sincronizzazione di Azure AD Connect |![Server di sincronizzazione di Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Server di sincronizzazione di Azure AD Connect in "modalità di staging" |![Server di sincronizzazione di Azure AD Connect in "modalità di staging"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync con Forefront Identity Manager (FIM) 2010 o Microsoft Identity Manager (MIM) 2016 |![GALSync con FIM 2010 o MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Dettagli relativi al server di sincronizzazione di Azure AD Connect |![Dettagli relativi al server di sincronizzazione di Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Scenario non supportato |![Scenario non supportato](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Foresta singola, tenant singolo di Azure AD
![Topologia per una foresta singola e un tenant singolo](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologia più comune è costituita da una singola foresta locale, con uno o più domini, e un tenant singolo di Azure AD. Per l'autenticazione di Azure AD viene usata la sincronizzazione delle password. L'installazione rapida di Azure AD Connect supporta unicamente questa topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Foresta singola, più server di sincronizzazione a un tenant di Microsoft Azure
![Topologia con filtri non supportata per una foresta singola](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Non è supportato lo scenario che prevede più server di sincronizzazione di Azure AD Connect connessi allo stesso tenant di Azure AD, a eccezione di un [server di staging](#staging-server). Non è supportato neanche se i server sono configurati per la sincronizzazione con un set di oggetti che si escludono a vicenda. Questa topologia potrebbe essere stata presa in considerazione se non è possibile raggiungere tutti i domini della foresta da un unico server o se si vuole distribuire il carico tra server diversi.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Più foreste, tenant singolo di Azure AD
![Topologia per più foreste e un tenant singolo](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

In molte organizzazioni sono presenti ambienti con più foreste Active Directory locali. La presenza di più foreste Active Directory locali può essere dovuta a vari motivi. Esempi tipici sono costituiti da progettazioni con foreste di account-risorse e dai risultati di fusioni o acquisizioni.

Quando sono presenti più foreste, devono essere tutte raggiungibili da un singolo server di sincronizzazione di Azure AD Connect. Non è necessario aggiungere il server a un dominio. Se è necessario raggiungere tutte le foreste, è possibile inserire il server in una rete perimetrale.

L'installazione guidata di Azure AD Connect offre diverse opzioni per consolidare gli utenti rappresentati in più foreste. L'obiettivo è fare in modo che un utente sia rappresentato una sola volta in Azure AD. Nell'installazione guidata sono disponibili alcune topologie comuni che è possibile configurare nel percorso di installazione personalizzato. Nella pagina **Identificazione univoca per gli utenti** selezionare l'opzione corrispondente che rappresenta la topologia. Il consolidamento viene configurato solo per gli utenti. I gruppi duplicati non vengono consolidati con la configurazione predefinita.

Le topologie comuni sono illustrate nelle sezioni su [topologie separate](#multiple-forests-separate-topologies), [a maglia completa](#multiple-forests-full-mesh-with-optional-galsync) e [topologie account-risorse](#multiple-forests-account-resource-forest).

La configurazione predefinita del servizio di sincronizzazione Azure AD Connect presuppone quanto segue:

* Ogni utente ha solo un account abilitato e la foresta in cui si trova l'account viene usata per l'autenticazione dell'utente. Questo presupposto riguarda sia la sincronizzazione delle password che la federazione. userPrincipalName e sourceAnchor o immutableID provengono da questa foresta.
* Ogni utente ha solo una cassetta postale.
* La foresta che ospita la cassetta postale di un utente garantisce la migliore qualità dei dati per gli attributi visibili nell'Elenco indirizzi globale di Exchange. Se non è presente una cassetta postale per l'utente, è possibile usare qualsiasi foresta per recuperare questi valori di attributi.
* Se è disponibile una cassetta postale collegata, è presente anche un account in una foresta diversa usato per l'accesso.

Se l'ambiente non soddisfa questi presupposti, si verifica quanto segue:

* Se sono presenti più account attivi o più cassette postali, il motore di sincronizzazione ne seleziona uno e ignora gli altri.
* Una cassetta postale collegata priva di account attivi non viene esportata in Azure AD. L'account utente non è rappresentato come membro in alcun gruppo. Una cassetta postale collegata in DirSync viene sempre rappresentata come cassetta postale normale. Questa modifica introduce un comportamento diverso per migliorare il supporto degli scenari a più foreste.

Per altre informazioni, vedere [Informazioni sulla configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Più foreste, più server di sincronizzazione a un tenant di Microsoft Azure
![Topologia non supportata per più foreste e più server di sincronizzazione](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Non è consentito connettere più server di sincronizzazione di Azure AD Connect a un tenant singolo di Azure AD. Fa eccezione l'uso di un [server di gestione temporanea](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Più foreste, topologie separate
![Opzione per rappresentare gli utenti solo una volta in tutte le directory](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Immagine di più foreste e topologie separate](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

In questo ambiente tutte le foreste locali vengono considerate entità separate. Nessun utente è presente in nessuna altra foresta. Ogni foresta presenta un'organizzazione Exchange dedicata e non viene effettuata alcuna sincronizzazione dell'elenco di indirizzi globale (GALSync) tra le foreste. Questa topologia può presentarsi dopo una fusione o acquisizione o in un'organizzazione in cui ogni business unit opera in modo indipendente. In Azure AD queste foreste si trovano nella stessa organizzazione e vengono visualizzate con un elenco di indirizzi globale unificato. Nell'immagine precedente ogni oggetto di ogni foresta viene rappresentato una sola volta nel metaverse e aggregato nel tenant di Azure AD di destinazione.

### <a name="multiple-forests-match-users"></a>Più foreste: corrispondenze con utenti
Tutti gli scenari hanno in comune il fatto che i gruppi di distribuzione e di sicurezza possono contenere una combinazione di utenti, contatti ed entità di sicurezza esterne. Le entità di sicurezza esterne vengono usate in Active Directory Domain Services (AD DS) per rappresentare i membri di altre foreste in un gruppo di sicurezza. Tutte le entità di sicurezza esterne vengono risolte nell'oggetto reale in Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Più foreste: a maglia completa con GALSync facoltativo
![Opzione per usare l'attributo Mail per trovare la corrispondenza quando le identità utenti sono presenti in più directory](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topologia a maglia completa per più foreste](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topologia a maglia completa consente di individuare utenti e risorse in qualsiasi foresta. In genere esistono trust bidirezionali tra le foreste.

Se Exchange è presente in più di una foresta, potrebbe essere disponibile (facoltativamente) una soluzione GALSync locale. Ogni utente viene quindi rappresentato come un contatto in tutte le altre foreste. GALSync viene in genere implementato tramite FIM 2010 o MIM 2016. Azure AD Connect non può essere usato per la soluzione GALSync locale.

In questo scenario, gli oggetti relativi all'identità vengono aggiunti tramite l'attributo Mail. Un utente che ha una cassetta postale in una foresta viene aggiunto ai contatti nelle altre foreste.

### <a name="multiple-forests-account-resource-forest"></a>Più foreste, foresta di tipo account-risorse
![Opzione per usare gli attributi ObjectSID e msExchMasterAccountSID per trovare la corrispondenza quando le identità sono presenti in più directory](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologia di foresta di tipo account-risorse per più foreste](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

In una topologia di foresta account-risorse sono presenti una o più foreste *account* con account utente attivi. Sono anche presenti una o più foreste *risorse* con account disabilitati.

In questo scenario una o più foreste risorse considerano attendibili tutte le foreste account. La foresta risorse presenta in genere uno schema di Active Directory esteso con Exchange e Lync. Tutti i servizi Exchange e Lync, con altri servizi condivisi, si trovano in questa foresta. Gli utenti hanno un account utente disabilitato in questa foresta e la cassetta postale è collegata alla foresta account.

## <a name="office-365-and-topology-considerations"></a>Considerazioni su Office 365 e sulle topologie
Alcuni carichi di lavoro di Office 365 prevedono determinate restrizioni per le topologie supportate:

| Carico di lavoro | Restrizioni |
| --------- | --------- |
| Exchange Online | Per altre informazioni sulle topologie ibride supportate da Exchange Online, vedere [Distribuzioni ibride con più insiemi di strutture di Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype for Business Online | Quando si usano più foreste locali, sarà supportata solo la topologia di tipo foresta account-risorse. Per altre informazioni, vedere [Requisiti ambientali di Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Un'organizzazione più grande può provare a usare la funzionalità [PreferredDataLocation di Office 365](active-directory-aadconnectsync-feature-preferreddatalocation.md), che consente di definire l'area del data center in cui si trovano le risorse dell'utente.

## <a name="staging-server"></a>server di gestione temporanea
![Server di staging in una topologia](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect supporta l'installazione di un secondo server in *modalità di staging*. Un server in questa modalità legge dati da tutte le directory connesse, ma non vi esegue operazioni di scrittura. Usa il normale ciclo di sincronizzazione e ha quindi a disposizione una copia aggiornata dei dati di identità.

In una situazione di emergenza in cui si verifica un errore nel server primario è possibile eseguire il failover nel server di staging. A tale scopo è possibile usare la procedura guidata di Azure AD Connect. Il secondo server può trovarsi in un data center diverso perché nessuna infrastruttura viene condivisa con il server primario. Eventuali modifiche di configurazione apportate al server primario devono essere copiate manualmente nel secondo server.

È possibile usare un server di staging per testare una nuova configurazione personalizzata e il relativo effetto sui dati. È possibile visualizzare l'anteprima delle modifiche e perfezionare la configurazione. Dopo aver ottenuto la configurazione ottimale, sarà possibile rendere attivo il server di staging e impostare il server attivo precedente sulla modalità di staging.

È anche possibile usare questo metodo per sostituire il server di sincronizzazione attivo. Preparare il nuovo server e impostarlo in modalità di staging. Verificarne lo stato di integrità, disabilitare la modalità di staging rendendolo attivo e arrestare il server attivo corrente.

Per avere a disposizione più backup in data center diversi, è possibile avere più di un server di staging.

## <a name="multiple-azure-ad-tenants"></a>Più tenant di Azure AD
È consigliabile che in Azure AD sia presente un tenant singolo per un'organizzazione.
Prima di pianificare di usare più tenant di Azure AD, vedere l'articolo [Gestione delle unità amministrative in Azure AD](../active-directory-administrative-units-management.md) che illustra gli scenari comuni in cui è possibile usare un singolo tenant.

![Topologia per più foreste e più tenant](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Esiste una relazione 1:1 tra un server di sincronizzazione di Azure AD Connect e un tenant di Azure AD. Per ogni tenant di Azure AD è necessaria un'installazione del server del servizio di sincronizzazione Azure AD Connect. Le istanze del tenant di Azure AD sono isolate per impostazione predefinita. Gli utenti di un tenant infatti non possono visualizzare gli utenti dell'altro tenant. Se la separazione è necessaria, questa è una configurazione supportata. In caso contrario, è consigliabile usare il modello di tenant di Azure AD singolo.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Ogni oggetto una sola volta in un tenant di Azure AD
![Topologia con filtri per una foresta singola](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

In questa topologia un server del servizio di sincronizzazione Azure AD Connect è connesso a ogni tenant di Azure AD. I server di sincronizzazione di Azure AD Connect devono essere configurati per l'applicazione di filtri, in modo che ogni server possa usare un set di oggetti che si escludono a vicenda. Ad esempio, è possibile definire l'ambito di ogni server in modo che corrisponda a un dominio oppure a un'unità organizzativa specifica.

Un dominio DNS può essere registrato unicamente in un tenant singolo di Azure AD. Anche gli UPN degli utenti nell'istanza locale di Active Directory devono usare spazi dei nomi separati. Ad esempio, nella figura precedente tre suffissi UPN separati vengono registrati nell'istanza locale di Active Directory: contoso.com, fabrikam.com e wingtiptoys.com. Gli utenti di ogni dominio di Active Directory locale usano uno spazio dei nomi diverso.

Non è presente alcun GALSync tra le istanze del tenant di Azure AD. La rubrica di Exchange Online e Skype for Business mostra solo gli utenti nello stesso tenant.

Questa topologia presenta le restrizioni seguenti per scenari altrimenti supportati:

* Solo uno dei tenant di Azure AD può abilitare una distribuzione ibrida di Exchange con l'istanza di Active Directory locale.
* I dispositivi Windows 10 possono essere associati a un solo tenant di Azure AD.
* L'opzione Single Sign-On (SSO) per la sincronizzazione password e l'autenticazione pass-through può essere usata solo con un tenant di Azure AD.

Il requisito relativo a un set di oggetti che si escludono a vicenda si applica anche al writeback. Alcune funzionalità di writeback non sono supportate con questa topologia, perché presuppongono una singola configurazione locale. Queste funzionalità includono:

* Writeback dei gruppi con la configurazione predefinita.
* Writeback dispositivi.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Ogni oggetto più volte in un tenant di Azure AD
![Topologia non supportata per una foresta singola e più tenant](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia non supportata per una foresta singola e più connettori](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Queste attività non sono supportate:

* Sincronizzazione dello stesso utente con più tenant di Azure AD.
* Modifica della configurazione per fare in modo che gli utenti di un tenant di Azure AD vengano visualizzati come contatti in un altro tenant di Azure AD.
* Modifica del servizio di sincronizzazione Azure AD Connect per la connessione a più tenant di Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync tramite l'uso di writeback
![Topologia non supportata per più foreste e più directory, con GALSync incentrato su Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia non supportata per più foreste e più directory, con GALSync incentrato su Active Directory locale](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

I tenant di Azure AD sono isolati per impostazione predefinita. Queste attività non sono supportate:

* Modifica della configurazione del servizio di sincronizzazione Azure AD Connect per la lettura di dati da un altro tenant di Azure AD.
* Esportazione di utenti come contatti in un'altra istanza locale di Active Directory con il servizio di sincronizzazione Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync con server di sincronizzazione locale
![GALSync in una topologia per più foreste e più directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

È possibile usare FIM 2010 o MIM 2016 locale per sincronizzare gli utenti (tramite GALSync) tra due organizzazioni di Exchange. Gli utenti di un'organizzazione vengono visualizzati come utenti/contatti esterni nell'altra organizzazione. Sarà quindi possibile sincronizzare le due istanze locali diverse di Active Directory con i rispettivi tenant di Azure AD.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come installare Azure AD Connect per questi scenari, vedere [Installazione personalizzata di Azure Ad Connect](active-directory-aadconnect-get-started-custom.md).

Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni sull'[integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
