---
title: Uso di b2clogin.com | Microsoft Docs
description: Informazioni sull'uso b2clogin.com anziché di login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440631"
---
# <a name="using-b2clogincom"></a>Uso di b2clogin.com

>[!IMPORTANT]
>Questa funzionalità è in anteprima pubblica 
>

È ora possibile usare il servizio di Azure Active Directory B2C con `<YourTenantName>.b2clogin.com` invece di usare `login.microsoftonline.com`.  Ciò offre numerosi vantaggi:
* Non si condivide più lo stesso limite di dimensione di intestazione cookie con gli altri prodotti Microsoft.
* È possibile rimuovere tutti i riferimenti a Microsoft nell'URL (è possibile sostituire `<YourTenantName>.onmicrosoft.com` con l'ID del tenant). Ad esempio: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Per poter sfruttare b2clogin.com, è necessario impostare alcune delle opzioni seguenti:

1. Per **Run now endpoint** (Endpoint Esegui adesso) assicurarsi di usare `<YourTenantName>.b2clogin.com` invece di `login.microsoftonline.com`.
2. Se si usa MSAL, è necessario impostare `validateauthority=false`.  Poiché l'emittente del token diventa`<YourTenantName>.b2clogin.com`.