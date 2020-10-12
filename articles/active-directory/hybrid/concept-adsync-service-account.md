---
title: 'Azure AD Connect: account del servizio ADSync | Microsoft Docs'
description: Questo argomento descrive l'account del servizio ADSync e fornisce le procedure consigliate per l'account.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 875c503a9959565d76d46902b5ecb386995ef1e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86144713"
---
# <a name="adsync-service-account"></a>Account del servizio ADSync
Azure AD Connect installa un servizio locale che orchestra la sincronizzazione tra Active Directory e Azure Active Directory.  Il servizio di sincronizzazione Microsoft Azure AD sincronizzazione (ADSync) viene eseguito in un server nell'ambiente locale.  Le credenziali per il servizio sono impostate per impostazione predefinita nelle installazioni Express, ma possono essere personalizzate per soddisfare i requisiti di sicurezza dell'organizzazione.  Queste credenziali non vengono usate per la connessione alle foreste locali o Azure Active Directory.

La scelta dell'account del servizio ADSync è una decisione importante da prendere prima di installare Azure AD Connect.  Qualsiasi tentativo di modificare le credenziali dopo l'installazione comporterà il mancato avvio del servizio, la perdita dell'accesso al database di sincronizzazione e la mancata autenticazione con le directory connesse (Azure e AD DS).  Non verrà eseguita alcuna sincronizzazione fino al ripristino delle credenziali originali.

## <a name="the-default-adsync-service-account"></a>Account del servizio ADSync predefinito

Quando viene eseguito in un server membro, il servizio AdSync viene eseguito nel contesto di un account del servizio virtuale (VSA).  A causa di una limitazione del prodotto, viene creato un account del servizio personalizzato quando viene installato in un controller di dominio.  Se l'account del servizio impostazioni rapide non soddisfa i requisiti di sicurezza dell'organizzazione, distribuire Azure AD Connect scegliendo l'opzione Personalizza.  Quindi scegliere l'opzione relativa all'account del servizio che soddisfi i requisiti dell'organizzazione.

>[!NOTE]
>L'account di servizio predefinito quando è installato in un controller di dominio è nel formato dominio \ AAD_InstallationIdentifier.  La password per questo account viene generata in modo casuale e presenta importanti problemi per il ripristino e la rotazione delle password.  Microsoft consiglia di personalizzare l'account del servizio durante l'installazione iniziale in un controller di dominio per l'utilizzo di un account del servizio gestito autonomo o del gruppo (sMSA/gMSA)

|Località Azure AD Connect|Account del servizio creato|
|-----|-----|
|Server membro|NT SERVICE\ADSync|
|Controller di dominio|Dominio \ AAD_74dc30c01e80 (vedere la nota)|

## <a name="custom-adsync-service-accounts"></a>Account del servizio ADSync personalizzati
Microsoft consiglia di eseguire il servizio ADSync nel contesto di un account del servizio virtuale o di un account del servizio gestito autonomo o del gruppo.  L'amministratore di dominio può anche scegliere di creare un account del servizio di cui è stato effettuato il provisioning per soddisfare i requisiti di sicurezza aziendali specifici.   Per personalizzare l'account del servizio utilizzato durante l'installazione, scegliere l'opzione Personalizza nella pagina Impostazioni rapide riportata di seguito.   Sono disponibili le opzioni seguenti:

- account predefinito: Azure AD Connect effettuerà il provisioning dell'account del servizio come descritto in precedenza
- account del servizio gestito: usare un MSA autonomo o di gruppo di cui l'amministratore ha eseguito il provisioning
- account di dominio: usare un account del servizio del dominio di cui l'amministratore ha eseguito il provisioning

![Screenshot della pagina delle impostazioni di Azure AD Connect Express con i pulsanti di opzione "Personalizza" o "Usa impostazioni rapide".](media/concept-adsync-service-account/adsync1.png)

![Screenshot della pagina Azure AD Connect "installazione dei componenti richiesti" con l'opzione per l'uso di un account del servizio gestito esistente selezionato.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosi delle modifiche all'account del servizio ADSync
Se si modificano le credenziali per il servizio ADSync dopo l'installazione, il servizio non viene avviato, perde l'accesso al database di sincronizzazione e non riesce a eseguire l'autenticazione con le directory connesse (Azure e AD DS).  La concessione dell'accesso al database al nuovo account del servizio ADSync non è sufficiente per risolvere il problema. Non verrà eseguita alcuna sincronizzazione fino al ripristino delle credenziali originali.

Il servizio ADSync emetterà un messaggio a livello di errore nel registro eventi quando non è possibile avviarlo.  Il contenuto del messaggio varia a seconda che sia in uso il database predefinito o il database SQL completo.  Di seguito sono riportati esempi delle voci del registro eventi che possono essere presenti.

### <a name="example-1"></a>Esempio 1

Non è stato possibile trovare le chiavi di crittografia del servizio AdSync e sono state ricreate.  La sincronizzazione non verrà eseguita fino a quando il problema non verrà corretto.

Risoluzione del problema la Microsoft Azure AD le chiavi di crittografia della sincronizzazione diventeranno inaccessibili se le credenziali di accesso al servizio AdSync sono state modificate.  Se le credenziali sono state modificate, utilizzare l'applicazione dei servizi per riportare l'account di accesso al relativo valore configurato in origine (ad esempio, NT SERVICE\AdSync) e riavviare il servizio.  Verrà ripristinato immediatamente il funzionamento corretto del servizio AdSync.

Per ulteriori informazioni, vedere l' [articolo](https://go.microsoft.com/fwlink/?linkid=2086764) seguente.

### <a name="example-2"></a>Esempio 2

Impossibile avviare il servizio perché non è stato possibile stabilire una connessione al database locale (local DB).

Risoluzione di questo problema il servizio di sincronizzazione Microsoft Azure AD perderà l'autorizzazione ad accedere al provider di database locale se le credenziali di accesso al servizio AdSync sono state modificate.  Se le credenziali sono state modificate, utilizzare l'applicazione Servizi per riportare l'account di accesso al relativo valore configurato in origine (ad esempio, NT SERVICE\AdSync) e riavviare il servizio.  Verrà ripristinato immediatamente il funzionamento corretto del servizio AdSync.

Per ulteriori informazioni, vedere l' [articolo](https://go.microsoft.com/fwlink/?linkid=2086764) seguente.

Ulteriori dettagli le seguenti informazioni sull'errore sono state restituite dal provider:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
