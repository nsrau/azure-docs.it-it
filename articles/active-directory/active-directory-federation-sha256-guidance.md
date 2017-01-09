---
title: Modificare l&quot;algoritmo hash della firma relativo al trust della relying party di Office 365 | Documentazione Microsoft
description: Questa pagina fornisce le linee guida per la modifica dell&quot;algoritmo SHA per il trust federativo con Office 365
keywords: SHA1,SHA256,O365,federazione,aadconnect,adfs,ad fs,modifica algoritmo hash della firma,trust federativo,trust della relying party
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: samueld
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 8a4e26b7ccf4da27b58a6d0bcfe98fc2b5533df8
ms.openlocfilehash: 4afdde65fe4c69d1944c0e1a3c5dd3300c5b970c


---
# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Modificare l'algoritmo hash della firma relativo al trust della relying party di Office 365
## <a name="overview"></a>Overview
Azure Active Directory Federation Services (ADFS) firmare i token per Microsoft Azure Active Directory per assicurare che non possano essere manomessi. Questa firma può essere basata sull'algoritmo SHA1 o SHA256. Azure Active Directory ora supporta i token firmati con un algoritmo SHA256, quindi è consigliabile impostare l'algoritmo per la firma di token su SHA256 per garantire il massimo livello di sicurezza. Questo articolo descrive i passaggi necessari per impostare l'algoritmo per la firma di token sul più sicuro livello SHA256.

## <a name="change-the-token-signing-algorithm"></a>Modificare l'algoritmo per la firma di token
Dopo avere impostato l'algoritmo di firma con uno dei due processi seguenti, AD FS firma i token per il trust della relying party di Office 365 con SHA256. Non è necessario apportare modifiche di configurazione aggiuntive e questa modifica non incide sulla possibilità di accedere a Office 365 o altre applicazioni Azure AD.

### <a name="ad-fs-management-console"></a>Console di gestione di AD FS
1. Aprire la console di gestione di AD FS nel server AD FS primario.
2. Espandere il nodo AD FS e fare clic su **Trust relying party**.
3. Fare clic con il pulsante destro del mouse sul trust della relying party di Office 365/Azure e scegliere **Proprietà**.
4. Nella scheda **Avanzate** selezionare SHA256 come algoritmo hash sicuro.
5. Fare clic su **OK**.

![Algoritmo di firma SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlet di AD FS PowerShell
1. Aprire PowerShell da qualsiasi server AD FS con privilegi di amministratore.
2. Impostare l'algoritmo hash sicuro con il cmdlet **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Vedere anche
* [Ripristinare il trust di Office 365 con Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)




<!--HONumber=Jan17_HO1-->


