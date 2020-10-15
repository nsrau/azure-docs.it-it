---
title: Modificare l'algoritmo hash della firma per Microsoft 365 relying party trust-Azure
description: Questa pagina fornisce le linee guida per la modifica dell'algoritmo SHA per la relazione di trust federativa con Microsoft 365.
keywords: SHA1, SHA256, M365, Federation, aadconnect, ADFS, ad FS, Change Sha, Federation Trust, relying party trust
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660924"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Modificare l'algoritmo hash della firma per Microsoft 365 relying party trust
## <a name="overview"></a>Panoramica
Azure Active Directory Federation Services (AD FS) esegue la firma dei token per Microsoft Azure Active Directory per evitare che possano essere alterati. Questa firma può essere basata sull'algoritmo SHA1 o SHA256. Azure Active Directory ora supporta i token firmati con un algoritmo SHA256, quindi è consigliabile impostare l'algoritmo per la firma di token su SHA256 per garantire il massimo livello di sicurezza. Questo articolo descrive i passaggi necessari per impostare l'algoritmo per la firma di token sul più sicuro livello SHA256.

>[!NOTE]
>Microsoft consiglia di usare SHA256 come algoritmo per la firma dei token poiché è più sicuro rispetto a SHA1 ma SHA1 rimane comunque un'opzione supportata.

## <a name="change-the-token-signing-algorithm"></a>Modificare l'algoritmo per la firma di token
Dopo aver impostato l'algoritmo di firma con uno dei due processi seguenti, AD FS firma i token per Microsoft 365 relying party trust con SHA256. Non è necessario apportare modifiche alla configurazione aggiuntive e questa modifica non ha alcun effetto sulla possibilità di accedere a Microsoft 365 o ad altre applicazioni Azure AD.

### <a name="ad-fs-management-console"></a>Console di gestione di AD FS
1. Aprire la console di gestione di AD FS nel server AD FS primario.
2. Espandere il nodo AD FS e fare clic su **Trust relying party**.
3. Fare clic con il pulsante destro del mouse su Microsoft 365/Azure relying party trust e selezionare **Proprietà**.
4. Nella scheda **Avanzate** selezionare SHA256 come algoritmo hash sicuro.
5. Fare clic su **OK**.

![Algoritmo di firma SHA256 - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlet di AD FS PowerShell
1. Aprire PowerShell da qualsiasi server AD FS con privilegi di amministratore.
2. Impostare l'algoritmo hash sicuro con il cmdlet **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Vedere anche
* [Ripristinare Microsoft 365 trust con Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

