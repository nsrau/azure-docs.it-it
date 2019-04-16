---
title: Sicurezza dei dati al bordo casella | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo al bordo casella dei dati, servizio e i dati in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 197c2c021dd9f674d196fb1169155bc6015f2e79
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578611"
---
# <a name="data-box-edge-security-and-data-protection"></a>Sicurezza bordo casella dei dati e protezione dei dati

Sicurezza è delle principali preoccupazioni ad adottare una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Soluzione bordo casella dei dati di Microsoft Azure consente di garantire che solo le entità autorizzate possono visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza applicato al bordo casella dei dati che consentono di proteggere ognuno dei componenti della soluzione e i dati archiviati su di essi.

La soluzione Edge casella dei dati di Azure è costituita da quattro componenti principali che interagiscono tra loro:

- **Bordo casella dati / servizio Gateway di dati finestra ospitata in Azure** : risorsa di gestione che consente la creazione dell'ordine di dispositivo, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Il dispositivo di bordo casella dati** : il dispositivo di trasferimento viene fornita all'utente per importare i dati in locale in Azure.
- **Client/host connessi al dispositivo** – client nell'infrastruttura che si connettono al dispositivo Edge casella dei dati e contiene dati che devono essere protetti.
- **Archiviazione cloud** – Posizione nel cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa bordo casella dei dati che è stato creato.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Protezione di servizio finestra Edge/Data finestra Gateway dati

Il servizio Gateway di dati finestra Edge/Data finestra è un servizio di gestione ospitato in Microsoft Azure. Il servizio viene usato per configurare e gestire il dispositivo.

- Accesso al servizio Gateway di dati finestra Edge/Data finestra richiede l'organizzazione disponga di un Enterprise Agreement (EA) o una sottoscrizione di Cloud Solution Provider (CSP). Per altre informazioni, visitare [iscriversi a una sottoscrizione di Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Poiché il servizio di gestione è ospitato in Azure, viene protetto dalle funzionalità di sicurezza di Azure. Per altre informazioni sulle funzionalità di sicurezza fornite da Microsoft Azure, andare in [Centro protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).
- Per le operazioni di gestione di SDK, è disponibile per il bordo di Data Box chiave di crittografia / risorsa del Gateway di dati finestra sotto **le proprietà del dispositivo**. È possibile visualizzare la chiave di crittografia solo se si dispone delle autorizzazioni per l'API Graph di risorse.

## <a name="data-box-edge-device-protection"></a>Protezione dei dati per il dispositivo bordo casella

Il dispositivo perimetrale casella dei dati è un dispositivo locale che aiuta a trasformare i dati da elaborarlo in locale e quindi inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Gateway di dati finestra Edge/Data finestra.
- È protetta in tutte le volte in cui da una password del dispositivo.
- È un dispositivo bloccato. Il dispositivo BMC e BIOS è protetti da password con accesso utente limitato per il BIOS.
- Avvio protetto è abilitato.
- Viene eseguito Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.
- Include una chiave all'interno di copertina che può essere utilizzata per bloccare il dispositivo. È consigliabile che dopo aver configurato il dispositivo, aprire il coperchio. Individuare la chiave e quindi si blocca la copertura per impedire eventuali accessi non autorizzati ai dischi dati che si trova allocate all'inizio del dispositivo.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite una chiave di attivazione

Solo un dispositivo Edge finestra dati autorizzato è consentito per l'iscrizione al servizio dati casella Edge/Data finestra Gateway creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario utilizzare una chiave di attivazione per attivare il dispositivo con il servizio Gateway di dati finestra Edge/Data finestra. Per altre informazioni, visitare [ottenere una chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key).

La chiave di attivazione che consente di:

- È una chiave di autenticazione basata su Azure Active Directory (AAD).
- Scade dopo tre giorni.
- Non viene usato dopo l'attivazione del dispositivo.
 
Dopo che un dispositivo viene attivato, Usa i token per comunicare con Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Proteggere il dispositivo tramite password

Le password assicurarsi che i dati siano accessibili solo agli utenti autorizzati. Bordo casella dei dati e il Gateway di dati finestra dispositivi avvio in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente del dispositivo tramite un browser Web locale e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. Gestione remota è attivata per impostazione predefinita. È quindi possibile fornire la password del dispositivo per accedere al dispositivo. Per altre informazioni, visitare [Connect in modalità remota al dispositivo Edge finestra dati](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Tenere presenti le procedure consigliate seguenti:

- Il servizio dati casella Edge non è possibile recuperare le password esistenti: è possibile ripristinare solo tramite il portale di Azure. È consigliabile archiviare tutte le password in un luogo sicuro per non dover reimpostare una password nel caso la si dimentichi. Se si reimposta una password, assicurarsi di notificarlo a tutti gli utenti prima di reimpostarla.
- Utilizzo interfaccia utente al web locale [modificare la password](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che non riscontrino un errore di accesso.
- È possibile accedere l'interfaccia di Windows PowerShell del dispositivo in modalità remota su HTTP. Per una protezione ottimale, è necessario utilizzare HTTP solo su reti attendibili.
- Assicurarsi che le password del dispositivo siano complesse e protette. Seguire le [procedure consigliate di Password](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>La protezione dei dati

In questa sezione descrive le funzionalità di sicurezza applicato al bordo casella dei dati che proteggono i dati in transito e i dati archiviati.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

Per i dati inattivi:

- Per i dati inattivi, bordo casella dei dati utilizza la crittografia BitLocker XTS AES-256 per proteggere i dati locali.
- Per i dati che risiede nelle condivisioni, l'accesso alle condivisioni è limitato.

    - Per i client SMB che accedono ai dati di condivisione, necessarie le credenziali utente associate alla condivisione. Queste credenziali vengono definite al momento della creazione della condivisione.
    - Per i client NFS che accedono a condivisioni, gli indirizzi IP dei client devono essere aggiunti al momento della creazione della condivisione.


### <a name="protect-data-in-flight"></a>Proteggere i dati in movimento

Per i dati in volo:

- Per i dati destinati tra il dispositivo e Azure, viene usato TLS 1.2 standard. Non vi è Nessun fallback a TLS 1.1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1.2 non è supportato. È necessario per le operazioni di gestione SDK e portale anche TLS 1.2.
- Quando i client di accedere al dispositivo tramite l'interfaccia utente web locale in un browser, viene usato TLS 1.2 standard come il protocollo di protezione predefinito.

    - La procedura consigliata consiste nel configurare il browser per usare TLS 1.2.
    - Se il browser non supporta TLS 1.2, è possibile usare TLS 1.1 o TLS 1.0.
- Per proteggere i dati quando lo si copia dal server di dati, è consigliabile usare SMB 3.0 con crittografia.

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

Il dispositivo è associato a un account di archiviazione che viene usato come una destinazione per i dati in Azure. Accede all'account di archiviazione viene controllato tramite la sottoscrizione e l'archiviazione a 512 bit due tasti di associato a tale account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo perimetrale finestra dati accede all'account di archiviazione. L'altra chiave è tenuta di riserva per far ruotare periodicamente le chiavi.

Per motivi di sicurezza, molti Data Center richiedono la rotazione della chiave. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non distribuire la password per altri utenti, hard codificarlo o salvarlo in un punto qualsiasi in testo normale accessibile ad altri utenti.
- [Rigenerare la chiave dell'account](../storage/common/storage-account-manage.md#regenerate-access-keys) usando il portale di Azure se si ritiene possa essere stata compromessa.
- Ruotare e quindi [sincronizzare le chiavi dell'account di archiviazione](data-box-gateway-manage-shares.md#sync-storage-keys) regolarmente per garantire che l'account di archiviazione non è accessibile a utenti non autorizzati.
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione Archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.


## <a name="manage-personal-information"></a>Gestire le informazioni personali

Il bordo di finestra di dati / servizio Gateway di dati finestra raccoglie i dati personali nelle istanze principali seguenti:

- **Dettagli dell'ordine** - Dopo aver creato l'ordine, l'indirizzo di spedizione, l'indirizzo di posta elettronica e le informazioni di contatto degli utenti vengono archiviate nel portale di Azure. Le informazioni salvate includono:
  - Nome del contatto
  - Numero di telefono
  - Email
  - Indirizzo
  - city
  - CAP
  - Stato
  - Paese/provincia/area geografica
  - Numero di tracciabilità della spedizione

    I dettagli dell'ordine vengono crittografati e archiviati nel servizio. Il servizio mantiene le informazioni fino a quando non si elimina la risorsa o l'ordine in modo esplicito. Inoltre, l'eliminazione delle risorse e l'ordine corrispondente viene bloccato dal momento in cui che il dispositivo viene spedito fino a quando il dispositivo torna a Microsoft.

- **Indirizzo di spedizione** – dopo l'ordine viene effettuato, servizio Data Box offre l'indirizzo di spedizione ai vettori di terze parti, ad esempio gruppi di continuità.

- **Gli utenti di condividere** -gli utenti nel dispositivo possono anche accedere ai dati presenti nelle condivisioni. Viene mostrato e può essere visualizzato un elenco di utenti che possono accedere ai dati della condivisione. Questo elenco viene eliminato anche quando le condivisioni vengono eliminate. Per visualizzare l'elenco di utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni sul bordo di finestra dati](data-box-gateway-manage-shares.md).

Per altre informazioni, consultare l'Informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo periferico finestra dati](data-box-edge-deploy-prep.md).

