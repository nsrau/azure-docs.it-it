---
title: Attributi shadow del servizio di sincronizzazione Azure AD Connect | Microsoft Docs
description: Descrive il funzionamento degli attributi shadow con il servizio di sincronizzazione Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0b6a7f22d744480a40a878c979986cdd7667109c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Attributi shadow del servizio di sincronizzazione Azure AD Connect
La maggior parte degli attributi viene rappresentata allo stesso modo in Azure AD e nel servizio Active Directory locale. Tuttavia, alcuni attributi sono caratterizzati da una gestione speciale e il valore dell'attributo in Azure AD potrebbe essere diverso da quello sincronizzato in Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introduzione agli attributi shadow
Alcuni attributi hanno due rappresentazioni in Azure AD. Vengono archiviati sia il valore locale che un valore calcolato. Questi attributi aggiuntivi sono chiamati attributi shadow. I due attributi più comuni in cui si può notare questo comportamento sono **userPrincipalName** e **proxyAddress**. La modifica dei valori degli attributi avviene quando sono presenti valori in questi attributi che rappresentano domini non verificati. Tuttavia, il motore di sincronizzazione in Connect legge il valore nell'attributo shadow, pertanto dal suo punto di vista l'attributo è stato confermato da Azure AD.

Non è possibile visualizzare gli attributi shadow usando il portale di Azure o con PowerShell. Ma comprendere questo concetto sarà d'aiuto per risolvere determinati scenari in cui l'attributo presenta valori differenti in locale e nel cloud.

Per capire meglio questo comportamento, esaminare l'esempio seguente relativo a Fabrikam:  
![Domini](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Includono più suffissi UPN nel servizio Active Directory locale, ma ne è stato verificato solo uno.

### <a name="userprincipalname"></a>userPrincipalName
Un utente ha i seguenti valori di attributo in un dominio non verificato:

| Attributo | Valore |
| --- | --- |
| userPrincipalName locale | lee.sperry@fabrikam.com |
| shadowUserPrincipalName in Azure AD | lee.sperry@fabrikam.com |
| userPrincipalName in Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

L'attributo userPrincipalName è il valore che viene visualizzato quando si usa PowerShell.

Dal momento che il valore dell'attributo locale reale è archiviato in Azure AD, quando si verifica il dominio fabrikam.com, Azure AD aggiorna l'attributo userPrincipalName con il valore di shadowUserPrincipalName. Non è necessario sincronizzare le modifiche apportate in Azure AD Connect perché questi valori vengano aggiornati.

### <a name="proxyaddresses"></a>proxyAddresses
Lo stesso processo per includere solo domini verificati si verifica anche per proxyAddresses, ma con una logica aggiuntiva. Il controllo dei domini verificati avviene solo per gli utenti delle cassette postali. Un contatto o un utente abilitato alla posta elettronica rappresenta un utente in un'altra organizzazione di Exchange ed è possibile aggiungere qualsiasi valore in proxyAddresses a questi oggetti.

Per un utente della cassetta postale, in locale o in Exchange Online, vengono visualizzati solo i valori per i domini verificati. L'aspetto è simile al seguente:

| Attributo | Valore |
| --- | --- |
| proxyAddresses locale | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| proxyAddresses in Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

In questo caso **smtp:abbie.spencer@fabrikam.com** è stato rimosso poiché tale dominio non è stato verificato. Ma Exchange ha aggiunto anche **SIP:abbie.spencer@fabrikamonline.com**. Fabrikam non ha usato Lync/Skype in locale, ma Azure AD ed Exchange Online sono in fase di preparazione.

Questa logica per proxyAddresses è detta **ProxyCalc**. ProxyCalc viene richiamato con ogni modifica a un utente quando:

- All'utente è stato assegnato un piano di servizio che include Exchange Online, anche se l'utente non disponeva della licenza per Exchange. Ad esempio, se all'utente è assegnato lo SKU Office E3, ma è stato assegnato solo SharePoint Online. Questo vale anche se la cassetta postale dell'utente è ancora in locale.
- L'attributo msExchRecipientTypeDetails ha un valore.
- Viene apportata una modifica a proxyAddresses o userPrincipalName.

ProxyCalc potrebbe richiedere del tempo per elaborare una modifica apportata a un utente e non è sincrono con il processo di esportazione di Azure AD Connect.

> [!NOTE]
> La logica ProxyCalc presenta alcuni comportamenti aggiuntivi per scenari avanzati non documentati in questo argomento. Questo argomento viene fornito per consentire all'utente di comprendere il comportamento e non per documentare l'intera logica interna.

### <a name="quarantined-attribute-values"></a>Valori di attributo in quarantena
Gli attributi shadow vengono usati anche quando sono presenti valori di attributo duplicati. Per altre informazioni, vedere [Resilienza degli attributi duplicati](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Vedere anche
* [Servizio di sincronizzazione Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
