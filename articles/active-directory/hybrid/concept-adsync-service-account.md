---
title: 'Azure AD Connect: Account del servizio ADSync | Microsoft Docs'
description: In questo argomento descrive l'account del servizio ADSync e fornisce procedure consigliate riguardo l'account.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478719"
---
# <a name="adsync-service-account"></a>Account del servizio ADSync
Azure AD Connect viene installato un servizio locale che Orchestra la sincronizzazione tra Active Directory e Azure Active Directory.  Il servizio di sincronizzazione di Microsoft Azure AD Sync (ADSync) viene eseguito in un server in locale nell'ambiente in uso.  Le credenziali per il servizio sono impostate per impostazione predefinita in installazioni Express, ma possono essere personalizzate per soddisfare i requisiti di sicurezza dell'organizzazione.  Queste credenziali non vengono usate per la connessione per le foreste locali o Azure Active Directory.

Scelta di ADSync account del servizio è un'importante decisione pianificazione prima di installare Azure AD Connect.  Qualsiasi tentativo di modificare le credenziali dopo l'installazione del servizio al mancato avvio, si otterrà la perdita dell'accesso al database di sincronizzazione e riesce a eseguire l'autenticazione con le directory connesse (Azure e Active Directory Domain Services).  Fino a quando non vengono ripristinate le credenziali originali, si verificherà alcuna sincronizzazione.

## <a name="the-default-adsync-service-account"></a>L'account del servizio ADSync predefinito

Quando eseguita in un server membro, il servizio AdSync viene eseguito nel contesto di un Account di servizio virtuale (VSA).  A causa di una limitazione del prodotto, viene creato un account del servizio personalizzato quando installato in un controller di dominio.  Se l'account del servizio impostazioni Express non soddisfa i requisiti di sicurezza dell'organizzazione, è possibile distribuire Azure AD Connect, scegliere l'opzione di personalizzazione.  Scegliere quindi l'opzione di account di servizio che soddisfa i requisiti dell'organizzazione.

>[!NOTE]
>L'account del servizio predefinito durante l'installazione in un controller di dominio è nel formato Domain\AAD_InstallationIdentifier.  La password per questo account è generata casualmente e presenta sfide significative per la rotazione di ripristino e la password.  Microsoft consiglia di personalizzare l'account del servizio durante l'installazione iniziale in un controller di dominio per usare una modalità autonoma o Account del servizio gestito di gruppo (sMSA / gMSA)

|Località di Azure AD Connect|Account del servizio creato|
|-----|-----|
|Server membro|NT SERVICE\ADSync|
|Controller di dominio|Domain\AAD_74dc30c01e80 (vedere la nota)|

## <a name="custom-adsync-service-accounts"></a>Account del servizio ADSync personalizzati
Microsoft consiglia che esegue la sincronizzazione di Active Directory del servizio nel contesto di un Account del servizio virtuale o un computer autonomo o Account del servizio gestito di gruppo.  L'amministratore di dominio può anche scegliere di creare un account di servizio eseguito il provisioning per soddisfare i requisiti di sicurezza dell'organizzazione specifica.   Per personalizzare l'account del servizio utilizzato durante l'installazione, scegliere l'opzione di personalizzazione della pagina Impostazioni rapide riportato di seguito.   Sono disponibili le opzioni seguenti:

- account predefinito: Azure AD Connect effettuerà il provisioning di account del servizio, come descritto in precedenza
- Managed service account: usare un computer autonomo o il provisioning dall'amministratore dell'account del servizio gestito di gruppo
- account di dominio, usare il provisioning dall'amministratore dell'account del servizio un dominio

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosticare modifiche account del servizio ADSync
Modifica le credenziali per il servizio ADSync dopo l'installazione del servizio al mancato avvio, si otterrà la perdita dell'accesso al database di sincronizzazione e riesce a eseguire l'autenticazione con le directory connesse (Azure e Active Directory Domain Services).  Concessione dell'accesso al database per il nuovo account del servizio ADSync non è sufficiente per risolvere questo problema. Fino a quando non vengono ripristinate le credenziali originali, si verificherà alcuna sincronizzazione.

Il servizio ADSync genererà un messaggio a livello di errore nel registro eventi quando non è possibile avviare.  Il contenuto del messaggio variano a seconda che il database incorporato (localdb) o SQL completo sia in uso.  Di seguito è riportati esempi di voci del registro eventi che possono essere presenti.

### <a name="example-1"></a>Esempio 1

Le chiavi di crittografia del servizio AdSync non è stato trovato e sono state ricreate.  Sincronizzazione non verrà eseguita fino a quando non viene risolto il problema.

Risoluzione dei problemi relativi a questo problema di Microsoft Azure AD Sync le chiavi di crittografia diventerà inaccessibile se vengono modificate le credenziali del Log nel servizio di sincronizzazione Active Directory.  Se sono state modificate le credenziali, usare l'applicazione di servizi per modificare l'account di accesso al valore originariamente configurato (ad es. NT SERVICE\AdSync) e riavviare il servizio.  Questo verrà ripristinato immediatamente il corretto funzionamento del servizio AdSync.

Vedere di seguito [articolo](https://go.microsoft.com/fwlink/?linkid=2086764) per altre informazioni.

### <a name="example-2"></a>Esempio 2

Il servizio è riuscito ad avviare perché non è stato possibile stabilire una connessione al database locale (localdb).

Risoluzione dei problemi di questo servizio problema di Microsoft Azure AD Sync perderà l'autorizzazione per accedere al provider di database locale se si modificano le credenziali del Log nel servizio di sincronizzazione Active Directory.  Se sono state modificate le credenziali, usare l'applicazione di servizi per modificare l'account di accesso al valore originariamente configurato (ad es. NT SERVICE\AdSync) e riavviare il servizio.  Questo verrà ripristinato immediatamente il corretto funzionamento del servizio AdSync.

Vedere di seguito [articolo](https://go.microsoft.com/fwlink/?linkid=2086764) per altre informazioni.

Le seguenti informazioni sull'errore è stati restituiti dal provider di dettagli aggiuntivi:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).