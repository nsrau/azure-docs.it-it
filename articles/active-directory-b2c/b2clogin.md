---
title: Uso di b2clogin.com| Microsoft Docs
description: Informazioni sull'uso b2clogin.com anziché di login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660421"
---
#<a name="using-b2clogincom"></a>Uso di b2clogin.com

>[!IMPORTANT]
>Questa funzionalità è in anteprima pubblica 
>

È ora possibile usare il servizio di Azure Active Directory B2C con `<YourTenantName>.b2clogin.com` invece di usare `login.microsoftonline.com`.  Ciò offre numerosi vantaggi:
* Non si condivide più lo stesso limite di dimensione di intestazione cookie con gli altri prodotti Microsoft
* È possibile rimuovere tutti i riferimenti a Microsoft nell'URL (è possibile sostituire `<YourTenantName>.onmicrosoft.com` con l'ID del tenant)

 Per poter sfruttare b2clogin.com, è necessario impostare alcune delle opzioni seguenti:

1. Per **Run now endpoint** (Endpoint Esegui adesso) assicurarsi di usare `<YourTenantName>.b2clogin.com` invece di `login.microsoftonline.com`.
2. Se si usa MSAL, è necessario impostare `validateauthority=false`.  Poiché l'emittente del token diventa`<YourTenantName>.b2clogin.com`.