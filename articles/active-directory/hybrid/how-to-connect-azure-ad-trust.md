---
title: Azure AD Connect - Gestire il trust di AD FS con Azure AD usando Azure AD Connect | Microsoft Docs
description: Dettagli operativi sulla gestione del trust di Azure AD tramite Azure AD Connect.
keywords: AD FS, ADFS, gestione di AD FS, AAD Connect, Connect, trust di Azure AD, trust, AAD, attestazione, attestazioni, regole attestazione, rilascio, trasformazione, regole, backup, ripristino
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: mtillman
ms.component: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.openlocfilehash: 5ac69c53a6b6c1e4695b88e5806f8e883cd52c66
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432054"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Gestire il trust di AD FS con Azure AD usando Azure AD Connect

## <a name="overview"></a>Panoramica

Azure AD Connect può gestire la federazione tra una distribuzione di Active Directory Federation Services (AD FS) locale e Azure AD. Questo articolo offre una panoramica su:

* Le varie impostazioni configurate sul trust da Azure AD Connect
* Le regole di trasformazione rilascio (regole attestazioni) impostate da Azure AD Connect
* Come eseguire il backup e ripristinare le regole attestazioni tra gli aggiornamenti e gli aggiornamenti della configurazione. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Impostazioni controllate da Azure AD Connect

Azure AD Connect gestisce **solo** le impostazioni correlate al trust di Azure AD. Azure AD Connect non modifica eventuali impostazioni di trust di altre relying party in AD FS. La tabella seguente indica le impostazioni controllate da Azure AD Connect.

| Impostazione | DESCRIZIONE |
| :--- | :--- |
| Certificato per la firma di token | Si può usare Azure AD Connect per reimpostare e ricreare il trust con Azure AD. Azure AD Connect esegue un'operazione una tantum immediata di rollover dei certificati per la firma di token per AD FS e aggiorna le impostazioni di federazione dei domini di Azure AD.|
| Algoritmo di firma dei token | Microsoft consiglia di usare l'algoritmo di firma dei token SHA-256. Azure AD Connect è in grado di rilevare se l'algoritmo di firma di token è impostato su un valore meno sicuro di SHA-256. L'impostazione verrà aggiornata a SHA-256 nella prima operazione di configurazione successiva possibile. Per usare il nuovo certificato per la firma di token è necessario aggiornare altri trust della relying party. |
| Identificatore del trust di Azure AD | Azure AD Connect imposta il valore dell'identificatore corretto per il trust di Azure AD. AD FS identifica in modo univoco il trust di Azure AD usando il valore dell'identificatore. |
| Endpoint di Azure Active Directory | Azure AD Connect garantisce che gli endpoint configurati per il trust di Azure AD siano sempre impostati in base agli ultimi valori consigliati per le prestazioni e la resilienza. |
| Regole di trasformazione rilascio | Per ottenere prestazioni ottimali delle funzionalità di Azure AD in una configurazione federata è necessaria una serie di regole attestazioni. Azure AD Connect verifica che il trust di Azure AD sia sempre configurato con il set di regole attestazioni consigliato. |
| ID alternativo | Se la sincronizzazione è configurata per l'uso dell'ID alternativo, Azure AD Connect configura AD FS per eseguire l'autenticazione tramite ID alternativo. |
| Aggiornamento automatico dei metadati | Il trust di Azure AD è configurato per l'aggiornamento automatico dei metadati. AD FS verifica periodicamente i metadati del trust di Azure AD e li mantiene aggiornati nel caso in cui vengano modificati sul lato Azure AD. |
| Autenticazione integrata di Windows | Durante l'operazione di aggiunta ad Azure AD ibrido, per la registrazione di dispositivi è abilitata l'autenticazione integrata di Windows, per semplificare l'operazione per i dispositivi di livello inferiore |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Flussi di esecuzione e impostazioni di federazione configurati da Azure AD Connect

Azure AD connect non aggiorna tutte le impostazioni del trust di Azure AD durante i flussi di configurazione. Le impostazioni modificate variano a seconda dell'attività o del flusso di esecuzione in esecuzione. La tabella seguente contiene un elenco delle impostazioni interessate nei diversi flussi di esecuzione.

| Flusso di esecuzione | Impostazioni interessate |
| :--- | :--- |
| Installazione al primo passaggio (rapida) | Nessuna |
| Installazione al primo passaggio (nuova farm AD FS) | Viene creata una nuova farm AD FS e viene creato da zero un nuovo trust con Azure AD. |
| Installazione al primo passaggio (farm AD FS esistente, trust di Azure AD esistente) | Identificatore del trust di Azure AD, regole di trasformazione rilascio, endpoint di Azure AD, ID alternativo (se necessario), aggiornamento automatico dei metadati |
| Reimpostazione del trust di Azure AD | Certificato per la firma di token, algoritmo di firma dei token, identificatore del trust di Azure AD, regole di trasformazione rilascio, endpoint di Azure AD, ID alternativo (se necessario), aggiornamento automatico dei metadati |
| Aggiunta del server federativo | Nessuna |
| Aggiunta del server WAP | Nessuna |
| Opzioni del dispositivo | Regole di trasformazione rilascio, autenticazione integrata di Windows per la registrazione del dispositivo |
| Aggiunta di un dominio federato | Se il dominio viene aggiunto per la prima volta, ovvero se la configurazione passa dalla federazione di un singolo dominio alla federazione multidominio, Azure AD Connect ricreerà il trust da zero. Se il trust con Azure AD è già configurato per più domini, vengono modificate solo le regole di trasformazione rilascio |
| Aggiornamento SSL | Nessuna |

In tutte le operazioni in cui vengono modificate impostazioni, Azure AD Connect crea un backup delle impostazioni di trust correnti in **%ProgramData%\AADConnect\ADFS**

![Pagina di Azure AD Connect con messaggio sul backup del trust di Azure AD esistente](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Prima della versione 1.1.873.0, il backup era costituito solo dalle regole di trasformazione rilascio e il backup veniva eseguito nel file di log di traccia della procedura guidata.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regole di trasformazione rilascio impostate da Azure AD Connect

Azure AD Connect verifica che il trust di Azure AD sia sempre configurato con il set di regole attestazioni consigliato. Microsoft consiglia di usare Azure AD Connect per la gestione del trust di Azure AD. Questa sezione elenca il set di regole di trasformazione rilascio e la relativa descrizione.

| Nome regola | DESCRIZIONE |
| --- | --- |
| Issue UPN | Questa regola esegue una query per recuperare il valore di userprincipalname dall'attributo configurato nelle impostazioni di sincronizzazione per userprincipalname.|
| Query objectguid and msdsconsistencyguid for custom ImmutableId claim | Questa regola aggiunge un valore temporaneo nella pipeline per objectguid e il valore msdsconsistencyguid se esistente |
| Check for the existence of msdsconsistencyguid | In base all'esistenza o meno del valore per msdsconsistencyguid, viene impostato un flag temporaneo per indicare cosa usare come ImmutableId |
| Issue msdsconsistencyguid as Immutable ID if it exists | Rilasciare msdsconsistencyguid come ImmutableId se il valore esiste |
| Issue objectGuidRule if msdsConsistencyGuid rule does not exist | Se il valore per msdsconsistencyguid non esiste, il valore di objectguid verrà rilasciato come ImmutableId |
| Issue nameidentifier | Questa regola rilascia il valore per l'attestazione nameidentifier.|
| Issue accounttype for domain-joined computers | Se l'entità da autenticare è un dispositivo aggiunto a un dominio, questa regola rilascia il tipo di account come DJ (domain-joined), che indica appunto un dispositivo aggiunto a un dominio |
| Issue AccountType with the value USER when it is not a computer account | Se l'entità da autenticare è un utente, questa regola rilascia il tipo di account come Utente |
| Issue issuerid when it is not a computer account | Questa regola rilascia il valore issuerId quando l'entità che esegue l'autenticazione non è un dispositivo. Il valore viene creato tramite un'espressione regolare, che viene configurata da Azure AD Connect. L'espressione regolare viene creata dopo aver preso in considerazione tutti i domini federati con Azure AD Connect. |
| Issue issuerid for DJ computer auth | Questa regola rilascia il valore issuerId quando l'entità che esegue l'autenticazione è un dispositivo |
| Issue onpremobjectguid for domain-joined computers | Se l'entità da autenticare è un dispositivo aggiunto a un dominio, questa regola rilascia l'objectguid locale per il dispositivo |
| Pass through primary SID | Questa regola rilascia il SID primario dell'entità che esegue l'autenticazione |
| Pass through claim - insideCorporateNetwork | Questa regola rilascia un'attestazione che indica ad Azure AD se l'autenticazione proviene dall'interno della rete aziendale o dall'esterno |
| Pass Through Claim – Psso |   |
| Issue Password Expiry Claims | Questa regola rilascia tre attestazioni per l'ora di scadenza della password, il numero di giorni per la scadenza della password dell'entità da autenticare e l'URL a cui eseguire il routing per modificare la password.|
| Pass through claim – authnmethodsreferences | Il valore nell'attestazione rilasciata in base a questa regola indica il tipo di autenticazione eseguito per l'entità |
| Pass through claim - multifactorauthenticationinstant | Il valore di questa attestazione specifica l'ora, in formato UTC, in cui l'utente ha eseguito per l'ultima volta l'autenticazione a più fattori. |
| Pass through claim - AlternateLoginID | Questa regola rilascia l'attestazione AlternateLoginID se l'autenticazione è stata eseguita usando l'ID di accesso alternativo. |

> [!NOTE]
> Le regole attestazioni per Issue UPN e ImmutableId saranno diverse se si usano opzioni non predefinite durante la configurazione di Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Ripristinare regole di trasformazione rilascio

Azure AD Connect versione 1.1.873.0 o successiva crea un backup delle impostazioni di trust di Azure AD ogni volta che queste vengono aggiornate. Il backup delle impostazioni di trust di Azure AD viene salvato in **%ProgramData%\AADConnect\ADFS**. Il nome file è nel formato AadTrust-&lt;data&gt;-&lt;ora&gt;.txt, ad esempio AadTrust-20180710-150216.txt

![Snapshot di un backup di esempio del trust di Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

È possibile ripristinare le regole di trasformazione rilascio seguendo la procedura suggerita di seguito

1. Aprire l'interfaccia utente di gestione di AD FS in Server Manager
2. Aprire le proprietà del trust di Azure AD passando a **AD FS &gt; Attendibilità componente &gt; Piattaforma delle identità di Microsoft Office 365 &gt; Edit Claims Issuance Policy** (Modifica criteri di rilascio attestazioni)
3. Fare clic su **Aggiungi regola**
4. Nel modello di regola attestazione selezionare Inviare attestazioni mediante una regola personalizzata e fare clic su **Avanti**
5. Copiare il nome della regola attestazioni dal file di backup e incollarlo nel campo **Nome regola attestazione**
6. Copiare la regola attestazioni dal file di backup al campo di testo **Regola personalizzata** e fare clic su **Fine**

> [!NOTE]
> Assicurarsi che le regole aggiuntive non siano in conflitto con le regole configurate da Azure AD Connect.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire e personalizzare Active Directory Federation Services con Azure AD Connect](how-to-connect-fed-management.md)
