---
title: 'Azure AD Connect: Account del servizio ADSync Documenti Microsoft'
description: In questo argomento viene descritto l'account del servizio ADSync e vengono illustrate le procedure consigliate relative all'account.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478719"
---
# <a name="adsync-service-account"></a>Account del servizio ADSync
Azure AD Connect installa un servizio locale che orchestra la sincronizzazione tra Active Directory e Azure Active Directory.  Il servizio di sincronizzazione della sincronizzazione di Microsoft Azure AD (ADSync) viene eseguito in un server nell'ambiente locale.  Le credenziali per il servizio sono impostate per impostazione predefinita nelle installazioni Express, ma possono essere personalizzate per soddisfare i requisiti di sicurezza dell'organizzazione.  Queste credenziali non vengono usate per connettersi alle foreste locali o ad Azure Active Directory.These credentials are not used to connect to your on-premises forests or Azure Active Directory.

La scelta dell'account del servizio ADSync è una decisione di pianificazione importante da prendere prima dell'installazione di Azure AD Connect.Choosing the ADSync service account is an important planning decision to make before installing Azure AD Connect.  Qualsiasi tentativo di modificare le credenziali dopo l'installazione comporterà l'avvio del servizio, la perdita dell'accesso al database di sincronizzazione e l'autenticazione con le directory connesse (Azure e Servizi di dominio Active Directory).  Non verrà eseguita alcuna sincronizzazione fino al ripristino delle credenziali originali.

## <a name="the-default-adsync-service-account"></a>Account del servizio ADSync predefinito

Quando viene eseguito in un server membro, il servizio AdSync viene eseguito nel contesto di un account di servizio virtuale (VSA).  A causa di una limitazione del prodotto, viene creato un account di servizio personalizzato quando viene installato in un controller di dominio.  Se l'account del servizio Impostazioni rapide non soddisfa i requisiti di sicurezza dell'organizzazione, distribuire Azure AD Connect scegliendo l'opzione Personalizza.If the Express settings service account does not meet your organizational security requirements, deploy Azure AD Connect by choosing the Customize option.  Scegliere quindi l'opzione dell'account di servizio che soddisfa i requisiti dell'organizzazione.

>[!NOTE]
>L'account di servizio predefinito quando viene installato in un controller di dominio è nel formato Dominio/AAD_InstallationIdentifier.  La password per questo account viene generata in modo casuale e presenta sfide significative per il recupero e la rotazione della password.  Microsoft consiglia di personalizzare l'account del servizio durante l'installazione iniziale in un controller di dominio per utilizzare un account del servizio gestito autonomo o di gruppo (sMSA / gMSA)

|Percorso di Azure AD ConnectAzure AD Connect location|Account di servizio creato|
|-----|-----|
|Server membro|NT SERVICE - ADSync|
|Controller di dominio|Dominio/AAD_74dc30c01e80 (vedere nota)|

## <a name="custom-adsync-service-accounts"></a>Account del servizio ADSync personalizzati
Microsoft consiglia di eseguire il servizio ADSync nel contesto di un account del servizio virtuale o di un account del servizio gestito autonomo o di gruppo.  L'amministratore di dominio può anche scegliere di creare un account di servizio di cui è stato eseguito il provisioning per soddisfare i requisiti di sicurezza specifici dell'organizzazione.   Per personalizzare l'account di servizio utilizzato durante l'installazione, scegliere l'opzione Personalizza nella pagina Impostazioni rapide di seguito.   Sono disponibili le opzioni seguenti:

- account predefinito: Azure AD Connect eseguirà il provisioning dell'account del servizio come descritto in precedenza
- account del servizio gestito: utilizzare un gruppo autonomo o un gruppo MSA di cui è stato eseguito il provisioning dall'amministratore
- account di dominio: utilizzare un account di servizio di dominio di cui è stato eseguito il provisioning dall'amministratore

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosi delle modifiche dell'account del servizio ADSync
La modifica delle credenziali per il servizio ADSync dopo l'installazione comporterà il mancato avvio del servizio, la perdita dell'accesso al database di sincronizzazione e l'autenticazione con le directory connesse (Azure e Servizi di dominio Active Directory).  Concessione dell'accesso al database per il nuovo account del servizio ADSync non è sufficiente per risolvere questo problema. Non verrà eseguita alcuna sincronizzazione fino al ripristino delle credenziali originali.

Il servizio ADSync emetterà un messaggio di livello di errore al registro eventi quando non è in grado di avviarsi.  Il contenuto del messaggio varia a seconda che il database incorporato (localdb) o SQL completo sia in uso.  Di seguito sono riportati esempi di voci del registro eventi che possono essere presenti.

### <a name="example-1"></a>Esempio 1

Le chiavi di crittografia del servizio AdSync non sono state trovate e sono state ricreate.  La sincronizzazione non verrà eseguita fino a quando il problema non viene risolto.

Risoluzione di questo problema Le chiavi di crittografia di sincronizzazione di Microsoft Azure AD diventeranno inaccessibili se vengono modificate le credenziali di accesso del servizio AdSync.  Se le credenziali sono state modificate, utilizzare l'applicazione Servizi per modificare l'account di accesso al valore configurato in origine (ad es. NT SERVICE-AdSync) e riavviare il servizio.  In questo modo verrà immediatamente ripristinato il corretto funzionamento del servizio AdSync.

Per ulteriori informazioni, vedere il seguente [articolo.](https://go.microsoft.com/fwlink/?linkid=2086764)

### <a name="example-2"></a>Esempio 2

Impossibile avviare il servizio perché non è stato possibile stabilire una connessione al database locale (localdb).

Risoluzione di questo problema Il servizio di sincronizzazione di Microsoft Azure AD perderà l'autorizzazione per accedere al provider di database locale se vengono modificate le credenziali di accesso del servizio AdSync.  Se le credenziali sono state modificate, utilizzare l'applicazione Servizi per modificare l'account di accesso al valore configurato in origine (ad es. NT SERVICE-AdSync) e riavviare il servizio.  In questo modo verrà immediatamente ripristinato il corretto funzionamento del servizio AdSync.

Per ulteriori informazioni, vedere il seguente [articolo.](https://go.microsoft.com/fwlink/?linkid=2086764)

Dettagli aggiuntivi Le seguenti informazioni sull'errore sono state restituite dal provider:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).